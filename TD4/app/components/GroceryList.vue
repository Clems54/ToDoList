<template>
    <ListView for="item in items">
        <v-template>
            <GroceryItem :groceryItem="item" @toggleDone="onToggleDone" @itemTap="onItemTap"
                         @itemSup="itemSup"></GroceryItem>
        </v-template>
    </ListView>
</template>

<script>
    import GroceryItem from "./GroceryItem";
    import Detail from "./Detail";
    import axios from "axios";
    import * as localstorage from "nativescript-localstorage";
    import Account from "./Account";

    export default {
        components: {GroceryItem, Detail},
        props: ['items'],

        methods: {
            onToggleDone(groceryItem) {
                let index = this.items.findIndex(i => i.uuid === groceryItem.uuid);

                axios({
                    method: "post",
                    url: "https://api.todolist.sherpa.one/users/check-token",
                    headers: {
                        'Authorization': 'Bearer ' + localStorage.getItem("token")
                    }
                }).then(() => {
                    axios({
                        method: "patch",
                        url: "https://api.todolist.sherpa.one/users/" + localstorage.getItem("uuid") + "/todos/" + this.items[index].uuid,
                        headers: {
                            'Authorization': 'Bearer ' + localStorage.getItem("token")
                        },
                        data: {
                            "done": !groceryItem.done
                        }
                    }).then(() => {
                        this.items[index].done = !groceryItem.done;
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

            },
            onItemTap(args) {
                this.$navigateTo(Detail, {
                    props: {
                        groceryItem: args
                    },
                    transitionAndroid: {
                        name: "slideLeft",
                        duration: 1000,
                        curve: "easeOut"
                    }
                });
            },
            itemSup(args) {
                let id = this.items.findIndex(i => i.uuid === args.uuid);
                if (this.items[id].done === true) {
                    axios({
                        method: "post",
                        url: "https://api.todolist.sherpa.one/users/check-token",
                        headers: {
                            'Authorization': 'Bearer ' + localStorage.getItem("token")
                        }
                    }).then(() => {
                        axios({
                            method: "delete",
                            url: "https://api.todolist.sherpa.one/users/" + localstorage.getItem("uuid") + "/todos/" + this.items[id].uuid,
                            headers: {
                                'Authorization': 'Bearer ' + localStorage.getItem("token")
                            }
                        }).then(() => {
                            this.items.splice(id, 1);
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
                } else {
                    alert("L'item n'est pas fait !");
                }
            },
        }
    }
</script>

<style scoped>

</style>