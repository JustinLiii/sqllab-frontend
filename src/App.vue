<script setup lang="ts">
// import HelloWorld from './components/HelloWorld.vue'
// import TheWelcome from './components/TheWelcome.vue'
import Login from './components/Login.vue'
import SignUp from './components/SignUp.vue'
import Main from './components/Main.vue'
import EventList from './components/EventList.vue'
import {ref} from 'vue'
import axios from 'axios';

const backendUrl:string = "http://localhost:8001/api/db";

const currentUser= {
  name:'',
  admin:false,
}

const States = {
  Logging:1,
  Main:2,
  SignUp:3
}

const state = ref(States.Logging);

function HandleLogging(LogForm : any){
  console.log("qwq");
  
  const user = {
    name:'',
    passwd:''
  }
  user.name = LogForm.username;
  user.passwd = LogForm.password;
  axios({
    method:'post',
    url:backendUrl,
    data: {
      header:'Login',
      user
    }
  })
  .then((response: any)=>{
    console.log("Main:",response.data);
    if(response.data.result == true) {
      currentUser.name = user.name;
      currentUser.admin = response.data.admin;
      state.value=States.Main
    }
  })
  .catch(err=>console.log(err));

  // debug
  // state.value=States.Main
}

function HandleSignUp(SignUpForm : any){
  console.log("qaq");
  state.value=States.Logging;
}

</script>

<template>

  <login-and-signup class="login-and-signup" v-if="state==States.Logging || state==States.SignUp">
    <header >
      <img alt="Site logo" class="logo" src="./assets/LOGO_5120.png" width="125" height="125" />
    </header>

    <Login v-if="state == States.Logging" @login="HandleLogging" @createNewUser="()=>state=States.SignUp"/>
    <SignUp v-else-if="state == States.SignUp" @signup="HandleSignUp" @go-back="()=>state=States.Logging"/>
  </login-and-signup>

  <Main :currentUser="currentUser" v-else-if="state == States.Main"/>
</template>

<style scoped>

header {
  line-height: 1.5;
}

.login-and-signup {
  display: flex;
  flex-direction: row;
  justify-content: center;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
} 

  @media (min-width: 512px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
