<template>
    <Page>
        <ActionBar title="TODO-LIST" color="black">
            <ActionItem @tap="onAddTap">AJOUTER</ActionItem>
        </ActionBar>
        <StackLayout>
            <GroceryList :items="itemsNotDeleted"/>
        </StackLayout>
    </Page>
</template>

<script>
    import GroceryList from "./GroceryList";
    import AddItem from './AddItem';
    import * as localstorage from 'nativescript-localstorage';

    export default {
        components: {GroceryList, AddItem},
        data() {
            return {
                items: [],
            }
        },
        methods: {
            onAddTap() {
                const newId = new Date().getTime();
                this.$showModal(AddItem, {
                    props: {
                        id: newId
                    }
                }).then(newItem => {
                    if (newItem) {
                        this.items.push(newItem);
                    }
                })
            }
        },

        computed: {
            itemsNotDeleted() {
                let itemsNotDeleted = [];
                this.items.forEach(function (item) {
                    if (item.deleted === false) {
                        itemsNotDeleted.push(item);
                    }
                });
                localStorage.setItem("data", JSON.stringify(this.items));
                return itemsNotDeleted;
            }
        },

        created: function () {
            if (localStorage.getItem("data") !== null) {
                this.items = JSON.parse(localStorage.getItem("data"));
            }
        }
    }
</script>

<style scoped>
    ActionBar {
        background-color: #53ba82;
        color: #ffffff;
    }
</style>