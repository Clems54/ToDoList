<template>
    <Page>
        <ActionBar title="DETAILS">
        </ActionBar>
        <StackLayout>
            <Label :text="groceryItem.content"></Label>
            <StackLayout orientation="horizontal">
                <Button text="Retour" @tap="$navigateBack"></Button>
                <Button :text="statusText" @tap="toggle"></Button>
            </StackLayout>
        </StackLayout>
    </Page>
</template>

<script>
    import axios from "axios";
    import * as localstorage from "nativescript-localstorage";
    import Account from "./Account";

    export default {
        props: ["groceryItem"],
        data: function () {
            return {}
        },
        computed: {
            statusText: function () {
                return this.groceryItem.done ? 'Fait' : 'A faire';
            }
        },
        methods: {
            toggle: function () {
                axios({
                    method: "post",
                    url: "https://api.todolist.sherpa.one/users/check-token",
                    headers: {
                        'Authorization': 'Bearer ' + localStorage.getItem("token")
                    }
                }).then(() => {
                    axios({
                        method: "patch",
                        url: "https://api.todolist.sherpa.one/users/" + localstorage.getItem("uuid") + "/todos/" + this.groceryItem.uuid,
                        headers: {
                            'Authorization': 'Bearer ' + localStorage.getItem("token")
                        },
                        data: {
                            "done": !this.groceryItem.done
                        }
                    }).then(() => {
                        this.groceryItem.done = !this.groceryItem.done;
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
    };
</script>

<style scoped>
    StackLayout {
        text-align: center;
    }

    StackLayout Label {
        font-size: 50px;
    }

    StackLayout button {
        width: 50%;
    }
</style>