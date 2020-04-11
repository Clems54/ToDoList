<template>
    <Page>
        <ActionBar title=""></ActionBar>
        <ScrollView>
            <FlexboxLayout class="page">
                <StackLayout class="form">
                    <Image class="logo" src="~/images/logo.png"/>
                    <Label class="header" text="TODO-LIST"/>

                    <StackLayout class="input-field" marginBottom="25">
                        <TextField class="input" hint="Email" keyboardType="email" autocorrect="false"
                                   autocapitalizationType="none" v-model="user.email"
                                   returnKeyType="next" @returnPress="focusPassword" fontSize="18"/>
                        <StackLayout class="hr-light"/>
                    </StackLayout>

                    <StackLayout v-show="isLoggingIn" class="input-field" marginBottom="25">
                        <TextField ref="password" class="input" hint="Mot de passe" secure="true"
                                   v-model="user.password"
                                   :returnKeyType="isLoggingIn ? 'done' : 'next'"
                                   @returnPress="focusConfirmPassword" fontSize="18"/>
                        <StackLayout class="hr-light"/>
                    </StackLayout>

                    <StackLayout v-show="!isLoggingIn" class="input-field">
                        <TextField ref="firstname" class="input" hint="Prénom" secure="false"
                                   v-model="user.firstname" returnKeyType="done"
                                   fontSize="18"/>
                        <StackLayout class="hr-light"/>
                    </StackLayout>

                    <StackLayout v-show="!isLoggingIn" class="input-field">
                        <TextField ref="lastname" class="input" hint="Nom" secure="false"
                                   v-model="user.lastname" returnKeyType="done"
                                   fontSize="18"/>
                        <StackLayout class="hr-light"/>
                    </StackLayout>

                    <StackLayout v-show="!isLoggingIn" class="gender">
                        <RadioGroup orientation="horizontal" @selected="selectGender">
                            <RadioButton text="male" checked="true"></RadioButton>
                            <RadioButton text="female"></RadioButton>
                        </RadioGroup>
                    </StackLayout>

                    <Button :text="isLoggingIn ? 'Se connecter' : 'S\'inscrire'" @tap="submit"
                            class="btn btn-primary m-t-20"/>
                    <Label v-show="isLoggingIn" text="Mot de passe oublié?" class="login-label" @tap="forgotPassword"/>
                </StackLayout>

                <Label class="login-label sign-up-label" @tap="toggleForm">
                    <FormattedString>
                        <Span :text="isLoggingIn ? 'Vous n\'avez pas de compte? ' : 'Retour à la page de connexion'"/>
                        <Span :text="isLoggingIn ? 'S\'inscrire' : ''" class="bold"/>
                    </FormattedString>
                </Label>
            </FlexboxLayout>
        </ScrollView>
    </Page>
</template>

<script>
    import App from "./App";
    import * as btoa from 'btoa';
    import {encode, decode} from "base-64";
    import * as utf8 from "utf8";
    import axios from 'axios';
    // A stub for a service that authenticates users.
    const userService = {
        register(user) {
            let data = {
                "firstname": user.firstname,
                "lastname": user.lastname,
                "email": user.email,
                "gender": user.gender
            }

            return axios({
                method: "post",
                url: "https://api.todolist.sherpa.one/users/signup",
                data: JSON.stringify(data)
            });
        },
        login(user) {
            return axios({
                method: "post",
                url: "https://api.todolist.sherpa.one/users/signin",
                auth: {
                    username: user.email,
                    password: user.password
                }
            });
        },
        resetPassword() {
            if (localStorage.getItem("token") !== undefined) {
                return axios({
                    method: "post",
                    url: "https://api.todolist.sherpa.one/users/reset/password",
                    headers: {
                        'Authorization': 'Bearer ' + localStorage.getItem("token")
                    }
                });
            } else {
                return Promise.reject("noconnect");
            }
        }
    };

    export default {
        components: {App},
        data() {
            return {
                isLoggingIn: true,
                user: {
                    email: "clement.mercier3@etu.univ-lorraine.fr",
                    password: "ycscznIAdJ",
                    firstname: "",
                    lastname: "",
                    gender: "male"
                }
            };
        },
        methods: {
            selectGender(data) {
                this.gender = data.value;
            },

            toggleForm() {
                this.isLoggingIn = !this.isLoggingIn;
            },

            submit() {
                if (!this.user.email || !this.user.password) {
                    this.alert(
                        "Il faut remplir tous les champs."
                    );
                    return;
                }
                if (this.isLoggingIn) {
                    this.login();
                } else {
                    this.register();
                }
            },

            login() {
                if (this.user.email !== "" && this.user.password !== "") {
                    userService
                        .login(this.user)
                        .then((response) => {
                            let {data} = response;
                            localStorage.setItem("token", data.token);
                            localStorage.setItem("uuid", data.user.uuid);

                            this.$navigateTo(App);
                        })
                        .catch((response) => {
                            this.alert("Echec de la connexion\n" + response);
                        });
                } else {
                    this.alert("Tous les champs doivent être saisies")
                }
            },

            register() {
                if (this.user.email !== "" && this.user.firstname !== "" && this.user.lastname !== "" && this.user.gender !== "") {
                    userService
                        .register(this.user)
                        .then((response) => {
                            let {data} = response;
                            this.alert("Vous pouvez maintenant vous connecter avec les identifiants suivant: \n" +
                                "- Email : " + data.email + "\n" +
                                "- Password : " + data.password);
                        })
                        .catch((result) => {
                            this.alert("Impossible de créer le compte\n" + result);
                        });
                } else {
                    this.alert("Tous les champs doivent être saisies")
                }
            },

            forgotPassword() {
                alert("La route pour la nouvelle génération d'un mot de passe ne fonctionne pas (404 not found).");
            },

            focusPassword() {
                this.$refs.password.nativeView.focus();
            },
            focusConfirmPassword() {
                if (!this.isLoggingIn) {
                    this.$refs.confirmPassword.nativeView.focus();
                }
            },

            alert(message) {
                return alert({
                    title: "TODO-LIST",
                    okButtonText: "OK",
                    message: message
                });
            }
        },
        mounted() {
            if (!global.btoa) {
                global.btoa = encode;
            }
        }
    };
</script>

<style scoped>
    .page {
        align-items: center;
        flex-direction: column;
    }

    .form {
        margin-left: 30;
        margin-right: 30;
        flex-grow: 2;
        vertical-align: middle;
    }

    .logo {
        margin-top: 10;
        margin-bottom: 12;
        height: 90;
        font-weight: bold;
    }

    .header {
        horizontal-align: center;
        font-size: 25;
        font-weight: 600;
        margin-bottom: 30;
        text-align: center;
        color: #002f58;
    }

    .input-field {
        margin-bottom: 25;
    }

    .input {
        font-size: 18;
        placeholder-color: #A8A8A8;
    }

    .input-field .input {
        font-size: 54;
    }

    .btn-primary {
        height: 50;
        margin: 30 5 15 5;
        background-color: #002f58;
        border-radius: 5;
        font-size: 20;
        font-weight: 600;
        color: white;
    }

    .login-label {
        horizontal-align: center;
        color: #A8A8A8;
        font-size: 16;
    }

    .sign-up-label {
        margin-bottom: 20;
    }

    .bold {
        color: #000000;
    }
</style>
