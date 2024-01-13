<template>
  <v-card v-if="session==false" class="loggin">
    <v-card class="logg">
    
        <v-text-field  label="Usuario" style="stop-color: antiquewhite;"
        variant="outlined"
        color="primary"
        prepend-icon="mdi-account"
        v-model="user"></v-text-field>

        <v-text-field  label="Contraseña"
        prepend-icon="mdi-lock"
        color="primary"
        variant="outlined"
        type="password"
        v-model="password"></v-text-field>

        <v-btn variant="outlined" color="#fff" style="background: #0074c7;" @click="login">
        Ingresar
        </v-btn>
    </v-card>
  </v-card>
  <v-card v-else>
  <navigation-bars @menuactiv="onMenu"></navigation-bars>
    <v-card class="content">
      <component :is="componentSelect"/>
    </v-card>
  </v-card>

</template>

<script>
import NavigationBars from './components/NavigationBars.vue'
import MaterialesPage from './screens/MaterialesPage.vue'

export default {

  name: 'App',
  components: {
    NavigationBars,
    MaterialesPage
  },
  data: () => ({

        contenido: "",
        componentSelect: null,
        session: false,
        user: "",
        password: "",
        userdta: []
    }),
    created(){ 
      var token = localStorage.getItem('token')
      if(token == null){
        localStorage.clear()
        this.session = false
      }else{
        this.session = true
      }

      this.userdta = JSON.parse(window.localStorage.getItem('userd'));
    },
    methods:{
      login(){
        let data = {
              "name": this.user,
              "password": this.password
          }

          console.log(data)

          // axios.post(url+"/user/login",data)
          //     .then((result) => {  
          //     console.log(result.data.User)
          //     localStorage.setItem('token', result.data.token)
          //     const usuario = {
          //         idusr: result.data.User[0].id,
          //         usuario: result.data.User[0].name
          //     }
          //     console.log(JSON.stringify(usuario))
          //     localStorage.setItem('userd', JSON.stringify(usuario))
          //     window.location.reload()
          // }).catch(e=>{console.log(e)})
      },
      onMenu(mn){      
          if(!mn.includes('-')){
            this.componentSelect = ""
            this.contenido = ""
          
          }else{
            this.componentSelect = mn
            this.contenido = mn
          }
        
          console.log(this.componentSelect)
      },
  }
}
</script>

<style>
#app {
  text-align: center;
  color: #2c3e50;
  margin-top: 00px;
}


.content{
  /* margin-top: 10vh; */
  width: 100%;
  height: 93vh;
  margin-left:0%;
  border-style: double;
  /* background: #2c3e50; */
  padding: 10px;
  /* padding-left: 15px; */
  overflow: auto;
}


/* .conenidogen{
  height: 90vh;
  overflow-y: auto;
} */


.card-datas{
    width: 90%;
    margin-bottom: 10px;
    padding: 15px;
  }




  .loggin{
  width: 100%;
  height: 100vh;
  /* background-image: url(assets/baclog1.jpeg); */
  background-size: 100% 100% ;

  display: flex;
  justify-content: center;
}

.logg{
  width: 50%;
  padding: 10%;
  padding-top: 50px;
  border-radius: 20px;
  height: 40vh;
  margin-top: 25vh;
  background: #c1e4ecd3;
}

.logo{width: 25%; margin-left: 37%; margin-bottom: 10%
}


@media (max-width:600px){
    .logg{
      width: 90%;
    }

    /* .loggin{
      background-image: url(assets/backmovil.jpeg);
    } */

    .logo{width: 35%; margin-left: 34%; margin-bottom: 15%
    }
}
</style>