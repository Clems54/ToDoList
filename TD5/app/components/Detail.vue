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
            <StackLayout width="100%">
                <Button text="PRENDRE PHOTO" @tap='prendrePhoto' width="100%"></Button>
            </StackLayout>
            <Image v-if="image == null" :src="this.groceryItem.imgUrl" width="150" height="150"/>
            <Image v-else :src="image.src" width="150" height="150"/>
            <Button v-if="image != null && !uploadFini && !uploadEnCours" text="Enregistrer l'image" @tap="sauvegarderImg"></Button>
            <Label v-if='image != null && !uploadFini && uploadEnCours' class="enrInfo" text="Upload..."></Label>
            <Label v-if='image != null && uploadFini && !uploadEnCours' class="enrInfo" text="Terminé"></Label>
        </StackLayout>
    </Page>
</template>

<script>
    import * as camera from "nativescript-camera";
    import {Image} from "tns-core-modules/ui/image";

    const bghttp = require("nativescript-background-http");
    const session = bghttp.session("image-upload");

    export default {
        props: ['groceryItem'],
        data() {
            return {
                image: null,
                uploadEnCours: false,
                uploadFini: false
            }
        },
        computed: {
            statusText() {
                return this.groceryItem.done ? 'Fait' : 'A faire';
            }
        },
        methods: {
            toggle() {
                this.groceryItem.done = !this.groceryItem.done;
            },

            prendrePhoto() {
                camera.requestPermissions()
                    .then(() => {
                        camera.takePicture({width: 300, height: 300, keepAspectRatio: true, saveToGallery: false})
                            .then(imageAsset => {
                                let img = new Image();
                                img.src = imageAsset;
                                this.image = img;
                                this.groceryItem.imgUrl = img.src;
                            })
                    })
            },
            sauvegarderImg() {
                let apiUrl = "https://api.imgbb.com/1/upload?key=5a3a1c82eeb38ff8bb55a52493809ba8";
                let path = this.image.src._android;

                const request = {
                    url: apiUrl,
                    method: "POST",
                    headers: {
                        "Content-type": "application/octet-stream"
                    },
                    description: "Img upload"
                };

                const params = [
                    {
                        filename: path,
                        name: "image",
                        mimeType: "img/jpeg"
                    }
                ];

                const task = session.multipartUpload(params, request);
                this.uploadEnCours = true;
                task.on("responded", (res) => {
                    this.uploadEnCours = false;
                    this.uploadFini = true;
                    let result = JSON.parse(res.data);
                    this.groceryItem.imgUrl = result.data.url;
                });
            }
        }
    }
</script>

<style lang="scss" scoped>
    StackLayout {
        text-align: center;
    }

    StackLayout Label {
        font-size: 50px;
    }

    StackLayout button {
        width: 50%;
    }

    .enrInfo {
        font-size: 15px;
    }
</style>