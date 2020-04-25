
<template>
 <!-- <v-card
    class="mx-auto"
  > -->

      <v-container fluid>
        <v-row dense>
            <v-col   v-for="card in cards" :key="card.id"  cols="4" >
                        <v-card
                                class="mx-auto"
                                max-width="344"
                                max-height="400"
                                dark
                                elevation="13"
                            >
                                <v-card-text>
                                <div> {{card.ciudad}}</div>
                                <p class="headline mb-1 ">
                                   {{card.nombre}} 
                                </p>
                                <p>Horario: <v-chip>{{card.hora_abre}}</v-chip>  -  <v-chip>{{card.hora_cierra}}</v-chip></p>
                                <div class="text--primary">
                                    {{card.direccion}}
                                </div>
                                </v-card-text>
                                <v-card-actions>
                                <v-btn
                                    text
                                    color="success"
                                    outlined
                                >
                                    Reservar
                                </v-btn>
                                </v-card-actions>
                </v-card>
           </v-col>


        </v-row>
      </v-container>

 <!-- </v-card> -->
</template>

<script>
 import { mapState, mapGetters } from 'vuex'
  export default {
    data: () => ({
        isActive:false,
        cards: [],
        user_id:'', 
        ciudad:''
    }),
        computed: {
    ...mapState({
      authUser: (state) => state.authUser
    }), 
    ...mapGetters({
      isLoggedIn: 'isLoggedIn'
    })
  },
    mounted(){
        this.user_id= this.$store.state.authUser.uid
        this.getCity(this.user_id)
    },
    methods: {
      async get_sucursales(ciudad){
        
          const messageRef = this.$fireStore.collection("sucursales").where("ciudad", "==", ciudad).get().then((querySnapshot) =>{
                    let sucursalesArray=[]
                    querySnapshot.forEach((doc) =>{
                          let sucursal=doc.data()
                          sucursal.id=doc.id
                          sucursal.flex=4
                          sucursalesArray.push(sucursal)
                    })
                    this.cards=sucursalesArray
                })
     },

          async getCity(id) {
        const messageRef = this.$fireStore.collection('usuarios').where("user_id", "==", id)
              .get()
    .then((querySnapshot)=> {
        querySnapshot.forEach((doc)=> {
            // doc.data() is never undefined for query doc snapshots
           this.get_sucursales(doc.data().ciudad)
           
        });
    })
    .catch(function(error) {
        console.log("Error getting documents: ", error);
    });
      }

    }
  }
</script>
