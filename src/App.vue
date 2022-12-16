<script setup lang="ts">
// import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import Login from './components/Login.vue'
import SignUp from './components/SignUp.vue'
import {ref} from 'vue'


const States = {
  Logging:1,
  Main:2,
  SignUp:3
}

const state = ref(States.Logging);

function HandleLogging( LogForm : any){
  console.log("qwq");
  let request : XMLHttpRequest =new XMLHttpRequest();
  request.open("POST","",true);
  request.setRequestHeader("Content-type","application/json");
  // request.sent()
  state.value=States.Main
}

function HandleSignUp(SignUpForm : any){
  console.log("qwq");
  state.value=States.Logging;
}

</script>

<template>
  <header>
    <img alt="Site logo" class="logo" src="./assets/LOGO_5120.png" width="125" height="125" />

    <!-- <div class="wrapper">
      <HelloWorld msg="You did it!" />
    </div> -->
  </header>

  <main>
    <!-- <TheWelcome /> -->
    <TheWelcome v-if="state == States.Main"/>
    <Login v-else-if="state == States.Logging" @login="HandleLogging" @createNewUser="()=>state=States.SignUp"/>
    <SignUp v-else-if="state == States.SignUp" @signup="HandleSignUp"/>
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
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
