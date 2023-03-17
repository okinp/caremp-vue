<template>
  <div>
    <button @click="loginWithMitId">Login with MitID</button>
    <span>{{  state.url  }}</span>
    <ul>
      <li v-for="(value, key) in state.claims" :key="key">
        {{ key }}: {{ value }}
      </li>
    </ul>
  </div>
</template>
<script setup>
import CriiptoAuth from "@criipto/auth-js";
import { reactive } from "vue";
import { Browser } from '@capacitor/browser';
import { App } from '@capacitor/app';


const criiptoAuth = new CriiptoAuth({
  domain: "rokoko-care-x-test.criipto.id",
  clientID: "urn:my:application:identifier:9922",
  store: sessionStorage,
});

const state = reactive({
  claims: {},
  url: "none"
});



const loginWithMitId = async () => {

  const params = await criiptoAuth.buildAuthorizeParams({
    redirectUri: "http://localhost:3000",
    acrValues: "urn:grn:authn:dk:mitid:substantial",
    responseType: "id_token",
    responseMode: "query",
    scope: "openid",
  })
  
  const url = await criiptoAuth.buildAuthorizeUrl(params);

  console.log(url);

  

  App.addListener('appUrlOpen', async (data) => {
    state.url = data.url;
    if (data.includes("http://localhost:3000")){
      await Browser.close();
      // state.url = data;

    }
  })

  const b = await Browser.open({ url });
    

  // console.log(result, result.id_token, result.claims);
  // state.claims = result.claims;



  // criiptoAuth.redirect
  //   .authorize({
  //     width: 300,
  //     height: 400,
  //     redirectUri: "http://localhost:3000",
  //     acrValues: "urn:grn:authn:dk:mitid:substantial",
  //   })
  //   .then((result) => {
  //     console.log(result, result.id_token, result.claims);
  //     state.claims = result.claims;
  //   });
};
</script>
