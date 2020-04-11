<template>
    <Page>
        <ActionBar title="TODO-LIST" color="black">
            <ActionItem @tap="onAddTap">AJOUTER</ActionItem>
        </ActionBar>
        <GroceryList :items="listItems"/>
    </Page>

</template>

<script>
    import GroceryList from "./GroceryList";
    import AddItem from "./AddItem";
    import axios from 'axios';
    import * as localstorage from "nativescript-localstorage";
    import Account from "./Account";

    export default {
        components: {GroceryList, AddItem},
        data() {
            return {
                items: [],
            }
        },
        methods: {
            onAddTap() {
                this.$showModal(AddItem).then(newItem => {
                    if (newItem) {
                        axios({
                            method: "post",
                            url: "https://api.todolist.sherpa.one/users/check-token",
                            headers: {
                                'Authorization': 'Bearer ' + localStorage.getItem("token")
                            }
                        }).then(() => {
                            axios({
                                method: "post",
                                url: "https://api.todolist.sherpa.one/users/" + localstorage.getItem("uuid") + "/todos",
                                headers: {
                                    'Authorization': 'Bearer ' + localStorage.getItem("token")
                                },
                                data: newItem
                            }).then((response) => {
                                let {data} = response;
                                this.items.push(data.todo);
                            }).catch((response) => {
                                alert("Erreur API\n" + response);
                            });
                        }).catch(() => {
                            this.$navigateTo(Account, {
                                transitionAndroid: {
                                    name: "slideRight",
                                    duration: 1000,
                                    curve: "easeOut"
                                }
                            });
                        });
                    }
                });
            }
        },
        computed: {
            listItems() {
                localStorage.setItem("data", JSON.stringify(this.items));
                return this.items;
            }
        },
        created: function () {
            axios({
                method: "post",
                url: "https://api.todolist.sherpa.one/users/check-token",
                headers: {
                    'Authorization': 'Bearer ' + localStorage.getItem("token")
                }
            }).then(() => {
                axios({
                    method: "get",
                    url: "https://api.todolist.sherpa.one/users/" + localstorage.getItem("uuid") + "/todos",
                    headers: {
                        'Authorization': 'Bearer ' + localStorage.getItem("token")
                    }
                }).then((response) => {
                    let {data} = response;
                    this.items = data.todos;
                }).catch((response) => {
                    alert("Erreur API\n" + response);
                });
            }).catch(() => {
                this.$navigateTo(Account, {
                    transitionAndroid: {
                        name: "slideRight",
                        duration: 1000,
                        curve: "easeOut"
                    }
                });
            });
        }
    }
</script>

<style scoped>
    ActionBar {
        background-color: #53ba82;
        color: #ffffff;
    }

    .message {
        vertical-align: center;
        text-align: center;
        font-size: 16;
        color: #333333;
    }
</style>