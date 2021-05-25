<template>
    <div>
        <v-layout row justify-center>
            <v-dialog v-model="dialog" >
                <v-card :loading="loading">
                    <v-card-title class="headline">{{data.titulo}}</v-card-title>
                    <v-card-text>
                        <v-row v-show="data.status == 0">
                            <v-col>
                                <web-cam ref="webcam" v-on:cameras="init()" v-on:started="loading = false" height="300" />
                            </v-col>
                        </v-row>
                        <v-row v-if="data.status == 1">
                            <v-col>
                                <v-img
                                    contain
                                    :src="data.img"
                                    lazy-src="https://picsum.photos/id/11/100/60"
                                    height="300px"
                                >
                                </v-img>
                            </v-col>
                        </v-row>
                        <v-row no-gutters v-if="data.status == 0 && loading == false">    
                            
                            <v-menu
                                bottom
                                left
                            >
                                <template v-slot:activator="{ on, attrs }">
                                    <v-btn class="mx-2" fab dark color="indigo" v-bind="attrs"
                                        v-on="on" >
                                        <v-icon>mdi-video-switch</v-icon>
                                    </v-btn>
                                </template>

                                <v-list>
                                <v-list-item
                                    v-for="(item, i) in cameras"
                                    :key="i"
                                    @click="changeCamera(item)"
                                >
                                    <v-list-item-title>{{ item.label }}</v-list-item-title>
                                </v-list-item>
                                </v-list>
                            </v-menu>
                            
                            <v-btn class="mx-2 r-btn" fab dark color="indigo"  @click="getPicture()">
                                <v-icon :disabled="loading" >mdi-camera-iris</v-icon>
                            </v-btn>
                     
                        </v-row>
                        <v-row v-if="data.status == 1">
                            <v-col class="ta-center" cols="12" xs="12">
                                Haz tomado exitosamente tu foto
                            </v-col>
                            <v-col class="ta-center" cols="12" xs="12">
                                <v-btn
                                rounded
                                color="primary"
                                dark
                                @click="close()"
                                >
                                Continuar
                                </v-btn>
                            </v-col>
                            <v-col class="ta-center" cols="12" xs="12">
                                <v-btn
                                rounded
                                @click="openPicture()"
                                >
                                Volver a tomar
                                </v-btn>
                            </v-col>
                        </v-row>
                    </v-card-text>
                    
                </v-card>
            </v-dialog>
        </v-layout>
    </div>
</template>

<script>
    import { WebCam } from "vue-web-cam";
    export default {
        components: {
            WebCam
        },
        // VARIABLES DEL COMPONENTE
        data: () => ({
            dialog:false,
            data: {
                status : 0
            },
            F:          null,
            loading:    false,
            cameras:    []
        }),
        watch:{
            dialog: function(val){
                if(!val){
                    this.loading = true;
                    this.$refs.webcam.stop();
                }
            }
        },
        //
        methods:{
            // INICIO DE LA CAMARA
            init(){
                this.cameras = this.$refs.webcam.cameras;
                this.$refs.webcam.changeCamera(this.$refs.webcam.cameras[0].deviceId);
                this.$refs.webcam.start();
            },
            // ABRE LA CAMARA PARA LA TOMA DE FOTOGRAFIA
            open(data , F){
                this.data   = data;
                this.dialog = true;
                this.F      = F;
                this.loading = true;
                
                // ABRE LA RIMERA CAMARA
                if(this.$refs.webcam){
                    this.$refs.webcam.changeCamera(this.$refs.webcam.cameras[0].deviceId).then(() =>{
                        this.$refs.webcam.start();
                        this.loading = false;
                    })
                }
            },
            // CIERRA EL MODAL Y REGRESA LA INFORMACION INICIAL
            close(){
                const D         = this.data;
                this.data       = {},
                this.dialog     = false;
                this.F.next(D);
            },
            // PARA TOMAR DE NUEVO OTRA FOTO
            openPicture(){
                this.data.status = 0;
                this.open(this.data , this.F);
            },
            // TOMA LA FOTO
            getPicture(){
                const captura       = this.$refs.webcam.capture();
                this.data.img       = captura;
                this.data.status    = 1;
            },
            // CAMBIA LA CAMARA CON EL ID DEL DISPOSITIVO
            changeCamera(item){
                this.$refs.webcam.changeCamera(item.deviceId);
            }
        }
    }
</script>

<style >
    .ta-center{
        text-align: center;
    }
    .r-btn{
        position: absolute !important;
    right: 1%;
    }
</style>