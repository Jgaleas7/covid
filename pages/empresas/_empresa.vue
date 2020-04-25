<template>
  <v-data-table
    :headers="headers"
    :items="desserts"
   
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat >
        <v-toolbar-title>Empresa: {{nombre_empresa }}</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
            Su empresa tiene que tener al menos UNA Sucursal
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" persistent max-width="600px">
          <template v-slot:activator="{ on }">
            <v-btn color="primary" dark class="mb-2" v-on="on">Crear Sucursal</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
              <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.ciudad" label="Ciudad"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                   <!-- <v-time-picker v-model="editedItem.hora_abre" label="Hora Inicio"></v-time-picker> -->

                                                    <v-menu
                                    ref="menu"
                                    v-model="menu"
                                    :close-on-content-click="false"
                                    :nudge-right="40"
                                    :return-value.sync="editedItem.hora_abre"
                                    transition="scale-transition"
                                    offset-y
                                    max-width="290px"
                                    min-width="290px"
                                  >
                                    <template v-slot:activator="{ on }">
                                      <v-text-field
                                        v-model="editedItem.hora_abre"
                                        label="Hora Inicio"
                                        
                                        readonly
                                        v-on="on"
                                      ></v-text-field>
                                    </template>
                                    <v-time-picker dark
                                      v-if="menu"
                                      v-model="editedItem.hora_abre"
                                      full-width
                                      @click:minute="$refs.menu.save(editedItem.hora_abre)"
                                    ></v-time-picker>
                                  </v-menu>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                  <!--  <v-time-picker v-model="editedItem.hora_cierra" label="Hora Final"></v-time-picker>-->
                               <v-menu
                                    ref="menu3"
                                    v-model="menu3"
                                    :close-on-content-click="false"
                                    :nudge-right="40"
                                    :return-value.sync="editedItem.hora_cierra"
                                    transition="scale-transition"
                                    offset-y
                                    max-width="290px"
                                    min-width="290px"
                                  >
                                    <template v-slot:activator="{ on }">
                                      <v-text-field
                                        v-model="editedItem.hora_cierra"
                                        label="Hora Cierre"
                                        
                                        readonly
                                        v-on="on"
                                      ></v-text-field>
                                    </template>
                                    <v-time-picker dark
                                      v-if="menu3"
                                      v-model="editedItem.hora_cierra"
                                      full-width
                                      @click:minute="$refs.menu3.save(editedItem.hora_cierra)"
                                    ></v-time-picker>
                                  </v-menu>
                  
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.capacidad" label="Capacidad de Personas"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.telefono" label="Telefono"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-textarea v-model="editedItem.direccion" label="Direccion Completa"></v-textarea>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="red darken-1" text @click="close">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="save">Guardar</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-btn  @click="editItem(item)" color="secondary">
      <v-icon
        small
        class="mr-2"
        
      >
        mdi-pencil
      </v-icon>
      Editar</v-btn>
      
     <!-- <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon> -->
      
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">Reset</v-btn>
    </template>
  </v-data-table>
</template>
<script>
  export default {
     asyncData ({ params }) {
  
      return { id: params.empresa}
    },
    data: () => ({
      dialog: false,
      headers: [
        {
          text: 'Nombre Empresa',
          align: 'start',
          sortable: false,
          value: 'ciudad',
        },
        { text: 'Hora Inicio', value: 'hora_abre' },
        { text: 'Hora Cierre', value: 'hora_cierra' },
        { text: 'Capacidad Personas', value: 'capacidad' },
        { text: 'Telefono', value: 'direccion' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      //categorias: ['Farmacia', 'Supermercado', 'Gasolinera', 'Otro'],
      desserts: [],
      menu:false,
      menu3:false,
      nombre_empresa:'',
      editedIndex: -1,
      editedItem: {
         ciudad: '',
        hora_abre: null,
        hora_cierra: null,
        capacidad: '',
        telefono: '',
        direccion:'',
        id_empresa:''
      },
      defaultItem: {
        ciudad: '',
        hora_abre: null,
        hora_cierra: null,
        capacidad: '',
        telefono: '',
        direccion:'',
        id_empresa:''
      },
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Nueva Sucursal' : 'Edit Sucursal'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
    },

    mounted () {
      this.initialize()
      this.get_sucursales()
  
    },

    methods: {
      async initialize () {
            this.defaultItem.id_empresa=this.id
            this.editedItem.id_empresa=this.id
             const messageRef = this.$fireStore.collection('empresas').doc(this.id)
                        try {
                          const messageDoc = await messageRef.get()
                          this.nombre_empresa=messageDoc.data().nombre
                        } catch (e) {
                        alert(e)
                        return
                        }
      },
     async get_sucursales(){
      
          const messageRef = this.$fireStore.collection("sucursales").where("id_empresa", "==", this.id).get().then((querySnapshot) =>{
                    querySnapshot.forEach((doc) =>{
                          
                        // doc.data() is never undefined for query doc snapshots
                            this.desserts.push({
                              id: doc.id,
                                ciudad: doc.data().ciudad,
                                hora_abre: doc.data().hora_abre,
                                hora_cierra: doc.data().hora_cierra,
                                capacidad: doc.data().capacidad,
                                telefono: doc.data().telefono,
                                direccion:doc.data().direccion
                            })
                        //console.log(doc, " => ", doc.data());
                    })
                })
     },

      editItem (item) {
        this.editedIndex = this.desserts.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        const index = this.desserts.indexOf(item)
        confirm('Are you sure you want to delete this item?') && this.desserts.splice(index, 1)
      },

      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },

      async save () {
        if (this.editedIndex > -1) { //editar
          Object.assign(this.desserts[this.editedIndex], this.editedItem)
        } else { // agregar nuevo
                          const messageRef = this.$fireStore.collection('sucursales').doc()
                    try {
                      this.editedItem.nombre=this.nombre_empresa
                      await messageRef.set(this.editedItem)
                                } catch (e) {
                                          console.log(e)
                                          return
                                }
                            //alert('Success.')
                            
                            let row={
                              id: messageRef.id,
                              ciudad:this.editedItem.ciudad,
                              hora_abre:this.editedItem.hora_abre,
                              hora_cierra:this.editedItem.hora_cierra,
                              capacidad:this.editedItem.capacidad,
                              telefono:this.editedItem.telefono,
                              direccion:this.editedItem.direccion, 
                              id_empresa:this.id,
                              nombre:this.nombre_empresa
                              }
                            //console.log(row)
                            this.desserts.unshift(row)
                          }
                          this.close()
      },
    },
  }
</script>
