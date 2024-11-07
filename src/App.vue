<template>
  <v-app>
    <!-- Criando header da aplicação -->
    <v-app-bar color="primary">
      <v-btn icon @click.stop="toggleDrawer">
        <v-icon>mdi-menu</v-icon>
      </v-btn>
      <v-app-bar-title>Cadastro de produtos do Pedrinho</v-app-bar-title>
    </v-app-bar>

    <!-- Criando menu lateral -->
    <v-navigation-drawer v-model="drawer" color="primary">
      <v-list-item to="Home" link prepend-icon="mdi-home-circle-outline">
        Home
      </v-list-item>

      <v-list-item to="Products" link prepend-icon="mdi-shopping">Products</v-list-item>

      <v-list-item  to="Logout" link prepend-icon="mdi-logout">Logout</v-list-item>
    </v-navigation-drawer>

    <!-- Criando conteúdo principal -->
    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<script setup>
// ref responsável por mudar o valor dos dados automaticamente
import { ref } from "vue";

// Criando variável para controlar o menu lateral
const drawer = ref(false);

// Função para abrir e fechar o menu lateral
const toggleDrawer = () => {
  drawer.value = !drawer.value;
};
</script>