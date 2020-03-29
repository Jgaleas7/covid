<template>
    <v-layout
            column
            justify-center
            align-center>
        <v-flex
                xs12
                sm8
                md6>
            <v-card width="400" class="elevation-12">
                <v-toolbar dark color="success">
                    <v-toolbar-title>Iniciar Sesion </v-toolbar-title>
                </v-toolbar>
                <v-form  v-if="!$store.state.authUser" @submit.prevent="login">
                    <v-snackbar v-model="formError" color="red darken-1" >
                        Ha Ocurrido un Error {{ formError }}
                    </v-snackbar>
                    <v-card-text>

                        <v-text-field  v-model="formUsername" name="login" label="Usuario" type="text"></v-text-field>
                        <v-text-field id="password"  v-model="formPassword" name="password" label="Password" required type="password"></v-text-field>


                    </v-card-text>
                    <v-card-actions>
                        <v-btn  large block type="submit">Login </v-btn>


                    </v-card-actions>
                </v-form>
                <div v-else>
                    Hello {{ $store.state.authUser.message}}!
                    <pre>I am the secret content, I am shown only when the user is connected.</pre>
                    <p><i>You can also refresh this page, you'll still be connected!</i></p>
                    <v-btn @click="logout">Log out </v-btn>
                </div>
            </v-card>
        </v-flex>
    </v-layout>
</template>
