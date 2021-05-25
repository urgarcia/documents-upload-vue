<template>
  <v-app id="inspire">
    <!-- MENU LATERAL -->
    <v-navigation-drawer
      v-model="drawer"
      app
    >
      <!-- TITULO DE LA LISTA -->
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title class="title">
            {{textApp.titulo}}
          </v-list-item-title>
          <v-list-item-subtitle>
            App para subir documentación
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>
      <v-divider></v-divider>
      <!-- LISTA DEL MENU -->
      <v-list
        dense
        nav
      >
        <v-list-item
          v-for="item in itemLeftBar"
          :key="item.title"
          link
        >
      
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>
          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
      <!-- SE TERMINA ICONOS DEL MENU -->
    </v-navigation-drawer>
    <!-- FIN DE MENU LATERAL -->

    <!-- BARRA SUPERIOR DE LA APLICACIÓN -->
    <v-app-bar app>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>App Documentos</v-toolbar-title>
    </v-app-bar>
    <!-- TERMINA LA BARRA DE LA APP -->
    
    <!-- APLICATIVO / AQUI SE PUEDE PONER EL ROUTER PARA MAS PAGINAS -->
    <v-main>
      <v-container grid-list-xs>
        <!-- CHIP PARA EL NOMBRE Y EL ICONO O AVATAR -->
        <v-row no-gutters>
          <v-col 
          md="6"
          offset-md="4"
          class="title"
          >
            <v-chip
              outlined
            >
              <v-icon>mdi-account-circle</v-icon>
              Uriel Misael Garcia Rosales
            </v-chip>
            
          </v-col>
        </v-row>
        <!-- TARJETAS CON LOS PASOS A SEGUIR -->
        <v-row no-gutters>
          <v-col 
          cols="12"
          xs="12" sm ='6'  md="4"
          class="pt-3 pl-3"
          v-for="item in documentsCards" :key="item.id"
          >
            <v-card @click="edita(item)">
              <img v-if="item.status == 1" src="/icons-data/check.svg" class="check">
              <v-img
                :src="item.img"
                lazy-src="https://picsum.photos/id/11/100/60"
                height="100px"
              >
                <template v-slot:placeholder>
                <v-row
                  class="fill-height ma-0"
                  align="center"
                  justify="center"
                >
                  <v-progress-circular
                    indeterminate
                    color="grey lighten-5"
                  ></v-progress-circular>
                </v-row>
              </template>
              </v-img>

              <v-card-title>
                {{item.titulo}}
              </v-card-title>

              <v-card-subtitle>
                <div v-if="item.data">
                  {{item.data.name}}
                </div>
                <div v-else>
                  {{item.subtitulo}}
                </div>
              </v-card-subtitle>
            </v-card>
          </v-col>
        </v-row>
        <!-- SE TERMINAN LAS TARJETAS DEL MENU -->

        <!-- MODAL PARA LAS IMAGENES -->
        <cam-modal ref="camModal" />

        <!-- COMPONENTE WEBCAM UNICAMENTE PARA SOLICITAR PERMISOS AL INICIO -->
        <web-cam v-show="false" />

      </v-container>
    </v-main>
  </v-app>
</template>

<script>
// COMPONENTES ADICIONALES
import { WebCam }   from "vue-web-cam";
import CamModal     from "./components/camModal.vue";
import FileSelector from 'vue-file-selector';

  export default {
    components: {
        CamModal,
        FileSelector,
        WebCam
    },
    data: () => ({ 
      drawer: null ,
      inputFile: null,
      itemLeftBar: [
        {icon: 'mdi-view-dashboard' , title: 'Documentación'}
      ],
      textApp: {
        titulo:'Proyecto Uriel Garcia',
        subtitulo:'App para subir documentación'
      },
      documentsCards:[
        {img:'' , titulo: 'Foto Selfie' , subtitulo: 'Sube tu foto',  id:1 , status: 0},
        {img:'' , titulo: 'Sube tu INE' , subtitulo: 'Sube tu ID',    id:2 , status:0},
        {img:'' , titulo: 'Documento' ,   subtitulo: 'Sube algún documento', id:3 , status:0 , data: null},
      ]
    }),
    methods:{
      // FUNCION PARA GESTIONAR LOS PASOS A SEGUIR Y SABER CUAL ES EL PROXIMO PASO
      next(item){
        if(item.id < this.documentsCards.length){
          let auxItem = JSON.parse(JSON.stringify(item));
          auxItem.id ++;
          this.documentsCards.map( (val) =>{
            if(auxItem.id == val.id){
              auxItem = val;
            }
          })
          this.edita(auxItem);
        }
      },
      // FUNCION AL DARLE CLIC ALGUNA TARJETA HACE LA FUNCIONALIDAD PARA ABRIR O EDITAR 
      edita: function (item) {
        switch (item.id) {
          case 1: this.$refs.camModal.open(item, this); break;
          case 2: this.$refs.camModal.open(item, this); break;
          case 3:  this.subeDoc(item);
          break;
        }
      },
      // FUNCION PARA GUARDAR UN ARCHIVO / FOTO O DOCUMENTO
      subeDoc(item){
        if(this.inputFile == null){
          this.inputFile = document.createElement('input');
          this.inputFile.type = 'file';
        }

        this.inputFile.onchange = e => { 
          var file = e.target.files[0]; 
          if(file){
            item.data = file;
            item.status = 1;
            item.img = 'https://emojipedia-us.s3.dualstack.us-west-1.amazonaws.com/socialmedia/apple/279/check-mark_2714-fe0f.png';
          }
        }

        this.inputFile.click();
      }
    }
  }
</script>
<style lang="css">
  .check{
    position: absolute;
    top: 10px;
    right: 10px;
    height: 20px;
    z-index: 1;
  }
</style>