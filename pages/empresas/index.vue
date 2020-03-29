<template>
  <v-data-table
    :headers="headers"
    :items="desserts"
    sort-by="calories"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat >
        <v-toolbar-title>Todas las empresas</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
            No olvidar ingresar las sucursales
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="600px">
          <template v-slot:activator="{ on }">
            <v-btn color="primary" dark class="mb-2" v-on="on">Nueva Empresa</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.nombre" label="NombreEmpresa"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">

                     <v-select
                     v-model="editedItem.categoria"
                      :items="categorias"
                      label="Categoria"
                     ></v-select>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.correo" label="Correo"></v-text-field>
                  </v-col>
                </v-row>
               <!-- <v-divider></v-divider> <v-divider></v-divider>

                    <v-tooltip bottom>
                          <template v-slot:activator="{ on }">
                            <v-chip class="ma-2"  text-color="white"  v-on="on"> SUCURSALES </v-chip>
                          </template>
                          <span>Dede Tener al menos 1 sucursal</span>
                  </v-tooltip> -->
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
      <v-btn :to="'/empresas/'+item.id"  nuxt><v-icon
        small
      >
        mdi-plus
      </v-icon>
      Agregar Sucursal</v-btn>

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
    data: () => ({
      dialog: false,
      headers: [
        {
          text: 'Nombre Empresa',
          align: 'start',
          sortable: false,
          value: 'nombre',
        },
        { text: 'Categoria', value: 'categoria' },
        { text: 'Correo', value: 'correo' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      categorias: ['Farmacia', 'Supermercado', 'Gasolinera', 'Otro'],
      desserts: [],
      editedIndex: -1,
      editedItem: {
        name: '',
        categoria: 0,

      },
      defaultItem: {
        name: '',
        categoria: 0,

      },
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Nueva Empresa' : 'Edit Empresa'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
    },

    mounted () {
      this.initialize()

    },

    methods: {
      initialize () {
              const messageRef = this.$fireStore.collection("empresas").get().then((querySnapshot) =>{
                    querySnapshot.forEach((doc) =>{
                        // doc.data() is never undefined for query doc snapshots
                            this.desserts.push({
                              id: doc.id,
                              nombre: doc.data().nombre,
                              correo: doc.data().correo,
                              categoria: doc.data().categoria,
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
                          const messageRef = this.$fireStore.collection('empresas').doc()
                    try {
                      await messageRef.set(this.editedItem)
                                } catch (e) {
                                          console.log(e)
                                          return
                                }
                            //alert('Success.')

                            let row={
                              id: messageRef.id,
                              nombre:this.editedItem.nombre,
                              categoria:this.editedItem.categoria,
                              correo:this.editedItem.correo
                              }
                            //console.log(row)
                            this.desserts.push(row)
                          }
                          this.close()
      },
    },
  }
</script>
