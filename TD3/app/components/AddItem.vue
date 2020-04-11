<template>
    <Page>
        <StackLayout>
            <Label text="Ajouter item"></Label>
            <TextField v-model="name" hint="Nom de l'item"></TextField>
            <Button text="Ajouter" @tap="onSaveTap"></Button>
        </StackLayout>
    </Page>
</template>

<script>
    export default {
        props: ["id"],
        data: function () {
            return {
                name: "",
            }
        },
        methods: {
            onSaveTap() {
                if(this.name != "") {
                    const newItem = {
                        id: this.id,
                        name: this.name,
                        done: false,
                        deleted: false,
                    };
                    this.$modal.close(newItem);
                } else {
                    alert("Où est le nom ?");
                }
            }
        }
    };
</script>

<style scoped>
    StackLayout {
        text-align: center;
        width: 90%;
    }

    Label {
        font-size: 30px;
        margin-bottom: 50px;
    }

    TextField{
        margin-bottom: 50px;
    }

    Button {
        background-color: #53ba82;
    }
</style>