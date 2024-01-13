<template>
    <v-toolbar dark prominent color="indigo-darken-4" density="compact">
        <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
        <v-avatar size="80px" >
            <v-img
            alt="Avatar"
            src="../assets/logo.png"
            ></v-img>
        </v-avatar>
        <!-- C.A.N. Sistemas -->
        <v-spacer></v-spacer>

       
        <v-btn @click="menuUser = !menuUser">
            {{ userdta.usuario }}
            <v-icon >mdi-menu-down</v-icon>
        </v-btn>
    </v-toolbar>

    <v-card min-width="300" width="300" v-if="menuUser"  style="position: absolute;  right: 10px; z-index: 1;">
        <v-list>
            <v-list-item
                prepend-avatar="../assets/logo.png"
                :title="user"
                :subtitle="section"
            >
            </v-list-item>
        </v-list>
        <v-divider></v-divider>

        <v-list>
            <v-list-item @click="logOut">
            Cerrar sesion
            </v-list-item>
        </v-list>

    </v-card>



    <v-layout>
        <v-navigation-drawer color="indigo-darken-4" style="min-width: 3%;"
        v-model="drawer" 
        temporary>
        <v-list-item style="height: 65px;" nav title="C.A.N. Sistema" prependIcon="mdi-apps" variant="tonal" @click="drawer=!drawer">
            <!-- <v-icon icon="mdi-apps"></v-icon> -->
        </v-list-item>

        <v-divider></v-divider>

            <v-list density="compact" nav>
                <v-list-item prepend-icon="mdi-file-document-multiple-outline" title="Cotizaciones" value="Cotizaciones"></v-list-item>
                <v-list-item prepend-icon="mdi-truck-trailer" title="Productos"  @click="openContent('worship-sistem')"></v-list-item> 
                <!-- truck-minus -->
                <v-list-item prepend-icon="mdi-grain" title="Materiales"  @click="openContent('materiales-page')"></v-list-item>
                <v-list-item prepend-icon="mdi-account-group" title="Clientes"  @click="openContent('worship-sistem')"></v-list-item>
            </v-list>
        </v-navigation-drawer>
    </v-layout>




</template>

<script>
// import axios from "axios";
// const url = process.env.VUE_APP_SERVICE_URL
export default {
    data(){
        return{
            menuUser: false,
            user: 'usuario',
            section: 'hfhjfgh',
            userdta:[],
            drawer: false,
        rail: true,
            }
    },
    created(){
        this.userdta = JSON.parse(window.localStorage.getItem('userd'));
    },
    methods:{
        async logOut(){            
            localStorage.clear()
            window.location.reload()
        },
        openContent(page){
            this.mosSubmen = false
            this.menutal = false 
            this.menuActiv = page
            this.$emit('menuactiv', page)
        },
    }
}

</script>