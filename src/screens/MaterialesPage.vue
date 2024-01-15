<template>

<v-row style="padding-left:5px; margin-top:20px; padding-right: 15px; ">
    Materiales 
    <v-spacer></v-spacer>
    <!-- <v-btn icon="mdi-reload" variant="outlined"></v-btn> -->
    
    <v-tooltip text="Actualizar" location="bottom" >
        <template v-slot:activator="{ props }">
            <v-btn v-bind="props" prepend-icon="mdi-reload" color="deep-orange-accent-3" @click="obtener" ></v-btn>
        </template>
    </v-tooltip>
    <v-btn prepend-icon="mdi-plus" color="deep-orange-accent-3"  style="margin-left:5px; margin-bottom:20px " @click="material.dialog= true;getCalogos()">Nuevo material</v-btn>
    <v-tooltip text="Catalogos" location="bottom" >
        <template v-slot:activator="{ props }">
            <v-btn v-bind="props" prepend-icon="mdi-book-open-page-variant" color="cyan-darken-4"  style="margin-left:5px; " @click="catalogos.dialog = true; getCalogos()"></v-btn>
        </template>
    </v-tooltip>
</v-row>



    <v-card
    >

    <template v-slot:text>
        <v-text-field
            height="20px"
            v-model="search"
            label="Search"
            single-line
            variant="outlined"
            hide-details
            density="compact"
            style="width: 45%;"
        ></v-text-field>
    </template>

        <v-data-table :loading="loadingTable"
        :headers="material.headers"
        :items="material.data"
        :search="search"
        density="compact"
        class="tabledts"
        >
        <template v-slot:[`item.delete`]="{ item }">
            <v-btn icon="mdi-delete" color="red"  density="compact"
            @click="dialog.warning = true; material.select = item; dialog.message= 'Estas por eliminar '+item.Descripcion "></v-btn>
        </template>

        <template v-slot:[`item.updated_at`]="{ item }">
            {{ item.updated_at.slice(0, 10) }}
        </template>

    </v-data-table>
    </v-card>



    <v-dialog v-model="material.dialog" persistent  width="1024" >
      <v-card>
        <v-card-title>
            <span class="text-h5">Materiales </span>
        </v-card-title>
        <v-card-text>
            <v-form @submit.prevent>
                <v-row>
                    <v-col cols="12" sm="12"  md="12">
                        <v-text-field  v-model="material.Descripcion " :rules="rules"  label="Material"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6"  md="4">

                    <v-select
                    clearable
                    label="unidad"
                    :items="catalogos.unidad"
                    item-title="Descripcion"
                    item-value="Codigo"
                    >
                    <template v-slot:item="{ props, item }">
                        <v-list-item v-if="item.raw.Descripcion == 'Administrar Catalogos'"
                        v-bind="props" @click="catalogos.dialog = true;"
                         prepend-icon="mdi-cog"
                        :subtitle="item.raw.Descripcion"
                        ></v-list-item>
                        <v-list-item v-else
                        v-bind="props"
                        :title="item.raw.Descripcion"
                        ></v-list-item>
                    </template>

                    </v-select>
                    
                    </v-col>
                    <v-col cols="12" sm="6"  md="4">
                        <!-- <v-text-field  v-model="material.Family " :rules="rules"  label="Familia"></v-text-field> -->
                        <v-select
                            clearable
                            label="unidad"
                            :items="catalogos.familias"
                            item-title="Descripcion"
                            item-value="Codigo"
                            >
                            <template v-slot:item="{ props, item }">
                                <v-list-item v-if="item.raw.Descripcion == 'Administrar Catalogos'"
                                v-bind="props"  @click="catalogos.dialog = true;"
                                prepend-icon="mdi-cog"
                                title=""
                                :subtitle="item.raw.Descripcion"
                                ></v-list-item>
                                <v-list-item v-else
                                v-bind="props"
                                :title="item.raw.Descripcion"
                                ></v-list-item>
                            </template>

                         </v-select>
                    </v-col>
                    <v-col cols="12" sm="6"  md="4">
                        <v-text-field  v-model="material.Costounitario "   label="Costo Unitario"></v-text-field>
                    </v-col>
                </v-row>

                <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn
                            color="blue-darken-1"
                            variant="text"
                            @click="material.dialog = false"
                        >
                            Close
                        </v-btn>
                        <v-btn
                            color="blue-darken-1"
                            type="submit"
                            @click="material.dialog = false"
                        >
                            Save
                        </v-btn>
                </v-card-actions>
            </v-form>
            <small>*Campos obligatorios</small>
        </v-card-text>
        
      </v-card>
    </v-dialog>


    <v-dialog v-model="catalogos.dialog"
        transition="dialog-bottom-transition"
        width="60%" >
        <v-card style="padding:15px">
            <v-card-title>Catalogos</v-card-title>
            <v-tabs v-model="catalogos.tab"  color="deep-purple-accent-4" align-tabs="start">
                <v-tab value="Unidad">Unidad</v-tab>
                <v-tab value="Familias">Familia</v-tab>
            </v-tabs>
            <v-window v-model="catalogos.tab">
                <v-window-item value="Unidad" style="padding:15px; margin-right:10px;">
                    <v-row style="margin-bottom:10px"> 
                        <v-spacer></v-spacer><v-btn prepend-icon="mdi-plus" color="cyan-darken-4" 
                        @click="catalogos.newc= true, catalogos.Codigo = ''; catalogos.Descripcion=''; catalogos.action='new'"  >Nuevo </v-btn>
                    </v-row>
                
                        <v-data-table  :headers="catalogos.headers" :loading="loadingTableDlg"
                        :items="catalogos.unidad"  density="compact"  class="tabledts"  >
                        <template v-slot:[`item.Codigo`]="{ item }">
                                <v-btn  density="compact"
                                @click="catalogos.newc=true;catalogos.Codigo = item.Codigo; catalogos.action='update'
                                catalogos.Descripcion=item.Descripcion; catalogos.id=item.id">{{ item.Codigo }}</v-btn>
                            </template>
                            <template v-slot:[`item.delete`]="{ item }">
                                <v-btn icon="mdi-delete" color="red"  density="compact"
                                @click="console.log(item) "></v-btn>
                            </template>
                        </v-data-table>
                </v-window-item>
                <v-window-item value="Familias">
                    <v-row style="margin-bottom:10px; margin-top:10px; margin-right:10px;"> 
                        <v-spacer></v-spacer><v-btn prepend-icon="mdi-plus" color="cyan-darken-4" 
                        @click="catalogos.newc= true,catalogos.Codigo = ''; catalogos.Descripcion=''; catalogos.action='new'" >Nuevo </v-btn>
                    </v-row>
                    <v-data-table  :headers="catalogos.headers" :loading="loadingTableDlg"
                        :items="catalogos.familias"  density="compact"  class="tabledts"  >
                         <template v-slot:[`item.Codigo`]="{ item }">
                                <v-btn  density="compact"
                                @click="catalogos.newc=true;catalogos.Codigo = item.Codigo; catalogos.action='update'
                                catalogos.Descripcion=item.Descripcion; catalogos.id=item.id">{{ item.Codigo }}</v-btn>
                            </template>
                            <template v-slot:[`item.delete`]="{ item }">
                                <v-btn icon="mdi-delete" color="red"  density="compact"
                                @click="console.log(item) "></v-btn>
                            </template>
                        </v-data-table>
                </v-window-item>
            </v-window>

                <v-card-text>
                <div class="text-h6 pa-12">{{dialog.message}}</div>
                </v-card-text>
                <v-card-actions class="justify-end">
                <v-btn
                    variant="text"
                    @click="catalogos.dialog = false; "
                >Cerrar</v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="catalogos.newc"
        transition="dialog-bottom-transition"
        width="50%" >
        <v-card>
            <v-card-title v-if="catalogos.action=='new'">Nuevo catalogo {{ catalogos.tab }}</v-card-title>
            <v-card-title v-else>Catalogo {{ catalogos.tab }}</v-card-title>
            <v-card-text>
                <v-form @submit.prevent>
                <v-row>
                    <v-col cols="12" sm="12"  md="4">
                        <v-text-field :disabled="catalogos.action=='new' ? false : true" v-model="catalogos.Codigo " :rules="rules"  label="Codigo"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12"  md="8">
                        <v-text-field  v-model="catalogos.Descripcion " :rules="rules"  label="Descripcion"></v-text-field>
                    </v-col>
                </v-row>
                        <v-card-actions class="justify-end">
                        <v-btn
                            variant="text"
                            @click="catalogos.newc = false; "
                        >Cancelar</v-btn>
                        <v-btn
                            type="submit"
                            @click="gurdarCatalogos "
                        >Guardar</v-btn>
                    </v-card-actions>
                </v-form>
            </v-card-text>
        </v-card>


    </v-dialog>



    <v-dialog v-model="dialog.error"
        transition="dialog-bottom-transition"
        width="auto" >
        <v-card>
            <v-toolbar color="red-accent-4" title="Error"></v-toolbar>
                <v-card-text>
                <div class="text-h6 pa-12">{{dialog.message}}</div>
                </v-card-text>
                <v-card-actions class="justify-end">
                <v-btn
                    variant="text"
                    @click="dialog.error = false;close(); "
                >OK</v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="dialog.warning"
        transition="dialog-bottom-transition"
        width="auto" >
        <v-card>
            <v-toolbar color="orange-darken-3" title="Accion requiere confirmacion"></v-toolbar>
                <v-card-text>
                <div class="text-h6 pa-12">{{dialog.message}}</div>
                </v-card-text>
                <v-card-actions class="justify-end">
                <v-btn
                    variant="text"
                    @click="dialog.warning = false "
                >Cancelar</v-btn>
                <v-btn
                    @click="deleteMaterial "
                >Confirmar</v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</template>

<script>
import axios from "axios";
const url = process.env.VUE_APP_SERVICE_URL; 
export default {
    data () {
        return {
            loadingTable: false,
            loadingTableDlg: false,
            search: '',
            dialog: {
                error: false,
                warning: false,
                message: ''
            },
            material: {
                dialog: false,
                headers: [
                    // {align: 'center', key: 'name', sortable: false, title: 'Codigo',},
                    { key: 'Descripcion', title: 'Material' ,align: 'center'},
                    { key: 'Unidad', title: 'Unidad' ,align: 'center'},
                    { key: 'Costo_Unitario', title: 'Costo unitario' ,align: 'center'},
                    { key: 'updated_at', title: 'Ultima Actualizacion' ,align: 'center'},
                    { key: 'delete', title: 'Eliminar' ,align: 'center',sortable: false,},
                ],
                data: [],
                select: [],
                Descripcion: '',
                Unidad: '',
                Family: '',
                Costounitario: 0.0

            },
            catalogos:{
                dialog: false,
                tab: 'Unidad',
                headers: [
                    { key: 'Codigo', title: 'Codigo' ,align: 'center'},
                    { key: 'Descripcion', title: 'Descripcion' ,align: 'center'},
                    { key: 'delete', title: 'Eliminar' ,align: 'center',sortable: false,},
                ],
                unidad:[],
                familias:[],
                newc: false,
                id: 0,
                Codigo: '',
                Descripcion: '',
                action:''
            },
            rules: [
                value => {
                    if (value) return true
                    return 'Este campo no puede ir vacio'
                },
            ],
        }
},
    created(){
        this.obtener()
    },
    methods:{
        async obtener(){
            this.loadingTable = true
            axios.get(url+"/materials")
            .then((result) => {    
                console.log(result.data)
                this.material.data = result.data
                this.loadingTable = false
            }) 
        },
        async getCalogos(){
            this.loadingTableDlg = true
            axios.get(url+"/materials/create")
            .then((result) => {    
                console.log(result.data)
                this.catalogos.unidad = result.data.unidades
                this.catalogos.familias = result.data.familys
                this.loadingTableDlg = false
                var setting = {
                    id: 0,
                    Codigo: "s",
                    Descripcion: "Administrar Catalogos"
                }
                this.catalogos.unidad.push(setting)
                this.catalogos.familias.push(setting)
            }) 
        },
        async deleteMaterial(){
            this.loadingTable = true
            console.log(this.material.select)
            var idd = this.material.select.Codigo
            axios.delete(url+"/materials/"+idd)
            .then((result) => {    
                console.log(result.data)
                this.dialog.warning = false
                this.obtener()
                // this.loadingTable = false
            }) 
        },
        async gurdarCatalogos(){
            this.loadingTableDlg = true
            var urls =url

            let data = {
                "Codigo": this.catalogos.Codigo,
                "Descripcion" : this.catalogos.Descripcion
            }
            
            if(this.catalogos.action == 'new'){
                 urls =this.catalogos.tab == "Unidad" ? url+"/Unidades": url+"/Familia"

                 await axios.post(urls, data)
                .then((result) => {    
                    console.log(result.data)
                    this.catalogos.newc = false
                    this.getCalogos()   }) 
            }else{
                 urls =this.catalogos.tab == "Unidad" ? url+"/Unidades/"+this.catalogos.id: url+"/Familia/"+this.catalogos.id
                 await axios.put(urls, data)
                .then((result) => {    
                    console.log(result.data)
                    this.catalogos.newc = false
                    this.getCalogos()   }) 
            }
        }
    }
}
</script>

<style>

</style>