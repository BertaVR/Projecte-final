<template>
  <v-card class="mx-auto my-12 packCard" max-width="374">
    <template slot="progress">
      <v-progress-linear color="deep-purple" height="10" indeterminate></v-progress-linear>
    </template>

    <v-img height="250"
      src="https://www.rainforest-alliance.org/wp-content/uploads/2021/06/capybara-square-1.jpg.optimal.jpg"></v-img>

    <v-card-title id="titulo">{{ objeto.nombre }}</v-card-title>

    <v-card-text>

      <div id="price">
        <div id="amount">
          {{ objeto.precio }}
        </div>
        <div id="currency">
          €
        </div>

      </div>

      <div id="calidad">Calidad: {{ objeto.calidad }}</div>
      <div id="stock">Stock: {{ objeto.stock }}</div>

    </v-card-text>
    <v-card-actions class="botonesPack">

      <EditPackForm :inventarioItems=inventarioItems :objeto=objeto></EditPackForm>

      <v-btn color="error" elevation="2" @click="borrarPack(objeto)">Borrar</v-btn>

      <BotonAñadirACarrito :objeto="objeto"></BotonAñadirACarrito>
    </v-card-actions>
    <v-snackbar v-model="snackbar">
      {{ objeto.nombre }} borrado del inventario. Actualice la página si quiere visualizar el cambio.

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="actualizar">
          Actualizar
        </v-btn>
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
          Cerrar
        </v-btn>
      </template>
    </v-snackbar>

    <v-card-actions>


      <v-spacer></v-spacer>
      <div @click="show = !show">{{ show ? 'Ocultar items' : 'Ver Items' }}</div>
      <v-btn icon @click="show = !show">
        <v-icon>{{ show ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
      </v-btn>

    </v-card-actions>

    <v-expand-transition>
      <div v-show="show">
        <v-divider></v-divider>

        <v-card-text>
          <div id="items" v-for="item in objeto.items">
            <GetItem :item="item"></GetItem>

          </div>
        </v-card-text>
      </div>
    </v-expand-transition>
  </v-card>
</template>



<style>
#price {
  display: flex;
  color: rgb(1, 8, 24);
  font-size: larger;
}

#amount {
  font-weight: 500;

}

#items div {
  padding-top: 5px;
}

.botonesPack {
  display: flex;
  justify-content: space-evenly;

}

.botonesPack div {
  justify-self: flex-end;
}
</style>
<script>
import EditPackForm from "./EditPackForm.vue";
import axios from "axios";
import GetItem from "./GetItem.vue";
import Carrito from "./Carrito.vue";
import BotonAñadirACarrito from "./BotonAñadirACarrito.vue";

export default {
  components: { EditPackForm, GetItem, Carrito, BotonAñadirACarrito },
  data: () => ({

    serverip: "127.0.0.1:3000",
    packs: [],
    show: false,
    snackbar: false,


  }),

  props: ['objeto', 'inventarioItems'],
  methods: {
    actualizar() {
      window.location.reload();
    },
    async inventarioPacks() { // TODO: Esto hay que refactorizarlo ya que es codigo repetido -> Hay que cogerlo del padre
      try {
        const packs = await axios.get(`http://${this.serverip}/packs`);
        this.packs = packs.data;
      }
      catch (e) {
        console.log(e);
      }
    },
    borrarPack(pack) {
      fetch(`http://${this.serverip}/packs/${pack.nombre}`, {
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json'
        }
      })
        .then((response) => {
          if (response.ok) {
            console.log("Response OK Status:", response.status);
            console.log("Item borrado:", response.statusText);
            this.snackbar = true

          } else {
            console.log("Response Status:", response.status);
            console.log("Reponse statuts text:", response.statusText);
          }
        })
        .catch((error) => {
          console.log(error.message);
        })
    },
  },




}

</script>