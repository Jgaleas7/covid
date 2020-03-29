<template>
<v-card dark>
     <v-card-title class="headline">Registrate</v-card-title>
  <v-form
    ref="form"
    v-model="valid"
  > <v-container>
      <v-row>
        <v-col cols="12"   md="4" >
            <v-text-field
            v-model="nombres"
            label="Nombres"
            :rules="nameRules"
            required
        
            ></v-text-field>
    </v-col>
    <v-col cols="12"   md="4" >
            <v-text-field
            v-model="apellidos"
            label="Apellidos"
            required
            ></v-text-field>
    </v-col>
     <v-col cols="12"   md="4" >
            <v-text-field
           
            v-model="telefono"
            label="Telefono"
            required
            ></v-text-field>
    </v-col>
     <v-col cols="12"   md="4" >
            <v-text-field
            v-model="identidad"
            label="Identidad"
            required
            ></v-text-field>
    </v-col>
   
    <v-col cols="12"   md="4" >
            <v-select
            v-model="ciudad"
            :items="ciudades"
            :rules="[v => !!v || 'Ciudad es required']"
            label="Donde vives?"
            required
            ></v-select>
    </v-col>

     <v-col cols="12"   md="4" >
            <v-text-field
            v-model="email"
            :rules="emailRules"
            label="E-mail"
            required
            type="email"
            ></v-text-field>
    </v-col>
    <v-col cols="12"   md="4" >
            <v-text-field
            v-model="password"
            :rules="passwordRules"
            label="Contrasena"
            type="password"
            required
            ></v-text-field>
    </v-col>
      </v-row>
    </v-container>
    <v-divider></v-divider>
       <v-card-actions>
    <v-btn
      :disabled="!valid"
      color="success"
      class="mr-4"
      @click="validate"
    >
      Registrar
    </v-btn>
   </v-card-actions>
  </v-form>
</v-card>
</template>
<script>
import { mapState, mapGetters } from 'vuex'

  export default {
    data: () => ({
      valid: false,
      password:'',
      nombres:'',
      apellidos:'',
      telefono:'',
      identidad:'',
      nameRules: [
        v => !!v || 'Nombre es Required',
        v => (v && v.length >= 3) || 'Nombre debe de tener mas 3 caracteres',
      ],

      passwordRules: [
        v => !!v || 'Contrasena es Required',
        v => (v && v.length >= 6) || 'Contrasena debe tener mas de 6 characters',
      ],
      email: '',
      emailRules: [
        v => !!v || 'E-mail es required',
        v => /.+@.+\..+/.test(v) || 'ingrese E-mail valido',
      ],
      masktel:'(###) ### - ####',
      ciudad: null,
      ciudades: [
        'Tegucigalpa',
        'San Pedro Sula',
        'La Ceiba',
        'Choluteca',
      ],
      
    }),
    computed: {
    ...mapState({
      authUser: (state) => state.authUser
    }),
    ...mapGetters({
      isLoggedIn: 'isLoggedIn'
    })
  },

    methods: {
      
         async validate() {
                          this.$fireAuth.createUserWithEmailAndPassword(
                            this.email,
                            this.password
                        ).then((response)=>{
                             console.log(response.user.uid)
                              const messageRef = this.$fireStore.collection('usuarios').doc()
                              messageRef.set({
                                user_id:response.user.uid,
                                nombres:this.nombres,
                                apellidos:this.apellidos,
                                telefono:this.telefono,
                                identidad:this.identidad,
                                correo:this.email,
                                ciudad:this.ciudad
                              })
                               
                             
                        })
                   
                     .catch ((e)=>{
                        console.log(e)
                     })  
             },
    },
  }
</script>