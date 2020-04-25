<template>
    <v-layout  column   justify-center align-center>
        <v-flex
                xs12
                sm8
                md6>
            <v-card width="400" class="elevation-12">
                <v-toolbar dark color="success">
                    <v-toolbar-title>Iniciar Sesion </v-toolbar-title>
                </v-toolbar>
                <v-form  v-if="!isLoggedIn" v-model="formValid">
                    <v-snackbar v-model="formError" color="red darken-1" >
                        Ha Ocurrido un Error {{ formError }}
                    </v-snackbar>
                    <v-card-text>

                        <v-text-field :rules="emailRules" v-model="email"  label="Correo" type="email"></v-text-field>
                        <v-text-field :rules="passwordRules" id="password"  v-model="password"  label="Password" required type="password"></v-text-field>

                    </v-card-text>
                    <v-card-actions>
                        <v-btn :disabled="!formValid" large block  @click="signInUser()">Login </v-btn>
                    </v-card-actions>
                </v-form>
                <div v-else>
                   <p>You are logged in with {{ authUser.email }}.</p>
                    <v-btn @click="logout">Salir </v-btn>
                </div>
            </v-card>
        </v-flex>
    </v-layout>
</template>
<script>
import { mapState, mapGetters } from 'vuex'

  export default {
        computed: {
    ...mapState({
      authUser: (state) => state.authUser
    }), 
    ...mapGetters({
      isLoggedIn: 'isLoggedIn'
    })
  },

    data: () => ({
      password:'',
      formError:false,
      formValid:false,
      passwordRules: [
        v => !!v || 'Contrasena es Required',
        v => (v && v.length >= 2) || 'Contrasena debe tener mas de 6 characters',
      ],
      email: '',
      emailRules: [
        v => !!v || 'E-mail es required',
        v => /.+@.+\..+/.test(v) || 'ingrese E-mail valido',
      ],
     
    }),
  
    methods: {
           async signInUser() {
                    try {
                       let user= await this.$fireAuth.signInWithEmailAndPassword(
                            this.email,
                            this.password
                        )
                        this.formError=false
                        this.$router.push('/');
                    } catch (e) {
                        alert(e)
                        this.formError=true
                    }
    },
      async logout() {
      try {
        await this.$fireAuth.signOut()
      } catch (e) {
        alert(e)
      }
    },
      
        
   
   
   
   },
  }
</script>
