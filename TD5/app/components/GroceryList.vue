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
    import Detail from './Detail';

    export default {
        components: {GroceryItem, Detail},
        props: ['items'],
        methods: {
            onToggleDone(groceryItem) {
                let newItem = Object.assign(groceryItem, {done: !groceryItem.done});
                this.items = Object.assign([], this.items, {idx: newItem});
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
                let id = this.items.findIndex(i => i.id === args.id);
                if (this.items[id].done === true) {
                    this.items[id].deleted = true;
                } else {
                    alert("L'item n'est pas fait !");
                }
            },
        },
    }
</script>

<style lang="scss" scoped>

</style>