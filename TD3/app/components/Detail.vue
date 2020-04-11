<template>
    <Page>
        <ActionBar title="DETAILS">
        </ActionBar>
        <StackLayout>
            <Label :text="groceryItem.name"></Label>
            <StackLayout orientation="horizontal">
                <Button text="Retour" @tap="$navigateBack"></Button>
                <Button :text="statusText" @tap="toggle"></Button>
            </StackLayout>
        </StackLayout>
    </Page>
</template>

<script>
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
                this.groceryItem.done = !this.groceryItem.done;
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