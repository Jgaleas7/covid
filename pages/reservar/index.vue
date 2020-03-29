
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
                                dark
                                elevation="13"
                            >
                                <v-card-text>
                                <div> {{card.ciudad}}</div>
                                <p class="display-1 text--primary">
                                   {{card.nombre}} walmart
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
  export default {
    data: () => ({
        isActive:false,
      cards: [],
    }),
    mounted(){
         this.get_sucursales()
    },
    methods: {
      async get_sucursales(){

          const messageRef = this.$fireStore.collection("sucursales").get().then((querySnapshot) =>{
                    querySnapshot.forEach((doc) =>{

                        // doc.data() is never undefined for query doc snapshots
                            this.cards.push({
                              id: doc.id,
                                ciudad: doc.data().ciudad,
                                hora_abre: doc.data().hora_abre,
                                hora_cierra: doc.data().hora_cierra,
                                capacidad: doc.data().capacidad,
                                telefono: doc.data().telefono,
                                direccion:doc.data().direccion,
                                flex:4
                            })
                        //console.log(doc, " => ", doc.data());
                    })
                })
     },

    }
  }
</script>
