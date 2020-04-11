<template>
    <StackLayout>
        <GridLayout columns="100, 75, *, 50">
            <Button :class="{'bgColorDone' : this.groceryItem.done}" col="0" :text="statusText" @tap="toggle"></Button>
            <StackLayout style="background-color: black" col="1" width="75">
            <Image v-if="groceryItem.imgUrl !== ''" :src="this.groceryItem.imgUrl" width="75" height="75" @tap="callItemTap"/>
            </StackLayout>
            <Label :class="{'line-through' : this.groceryItem.done}" class="item-name" col="2"
                   :text="this.groceryItem.name" @tap="callItemTap" style="text-align: center"></Label>
            <Label class="poubelle" text="🗑️" col="3" @tap="callItemSup"></Label>
        </GridLayout>
    </StackLayout>
</template>

<script>
    import {Image} from "tns-core-modules/ui/image";

    export default {
        name: "GroceryItem",
        props: ['groceryItem'],
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
                this.$emit("toggleDone", this.groceryItem);
            },
            callItemTap: function () {
                this.$emit('itemTap', this.groceryItem);
            },
            callItemSup: function () {
                this.$emit('itemSup', this.groceryItem);
            }
        }
    }
</script>

<style lang="scss" scoped>
    page {
        label {
            color: black;
        }

        .line-through {
            text-decoration: line-through;
        }
    }

    Button {
        background-color: rgba(255, 0, 0, 0.44);
    }

    .bgColorDone {
        background-color: rgba(0, 128, 0, 0.45);
    }

    .item-name {
        animation-name: show;
        animation-duration: 1s;
        animation-fill-mode: forwards;

    }

    @keyframes show {
        from {
            opacity: 0;
        }
        to {
            opacity: 1;
        }
    }

    GridLayout * {
        vertical-align: center;
        text-align: center;
    }

    .poubelle {
        background-color: darkred;
        border-radius: 50%;
    }
</style>