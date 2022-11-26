<template>
  <nav>
    <v-app-bar flat app>
      <v-toolbar-title class="pr-10">
        <div class="d-flex flex-no-wrap justify-space-between">
          <span class="font-weight-light">Clínica</span>
          <span class="font-weight-dark">Saúde</span>
          <v-img
            :src="require('../assets/logo-icon.png')"
            height="35px"
            width="35px"
            class="ml-3"
            contain
            center
          ></v-img>
        </div>
      </v-toolbar-title>

      <span
        v-for="page in pages"
        :key="page.route"
        router
        :to="page.route"
        text
        color="secondary"
      >
        <v-btn
          router
          :to="page.route"
          v-if="!page.isLoggedIn || $store.state.isLoggedIn"
          text
          color="secondary"
        >
          <span>{{ page.title }}</span>
        </v-btn>
      </span>
      <v-spacer></v-spacer>
      <v-btn
        v-if="!this.$store.state.isLoggedIn"
        router
        :to="'/login'"
        text
        color="secondary"
      >
        <span>Entrar</span>
        <v-icon right color="primary">mdi-exit-to-app</v-icon>
      </v-btn>
      <v-btn
        v-if="this.$store.state.isLoggedIn"
        router
        :to="'/profile'"
        text
        color="secondary"
      >
        <span>Meu Perfil</span>
        <v-icon right color="primary">mdi-account</v-icon>
      </v-btn>
      <v-btn
        v-if="this.$store.state.isLoggedIn"
        v-on:click="logout"
        text
        color="secondary"
      >
        <span>Sair</span>
        <v-icon right color="primary">mdi-logout</v-icon>
      </v-btn>
    </v-app-bar>
  </nav>
</template>

<script>
export default {
  created() {
    this.$store.commit("getUser");
  },
  methods: {
    logout() {
      const requestOptions = { method: "GET", credentials: "include" };
      fetch(
        `${this.$store.state.apiUrl}/api/usuarios/logout`,
        requestOptions
      ).then((response) => {
        if (response.status == 200) {
          this.$store.commit("getUser");
          this.$router.push("/");
        }
      });
    },
  },
  data() {
    return {
      pages: [
        { title: "Home", route: "/" },
        { title: "Área restrita", route: "/restricted-area", isLoggedIn: true },
      ],
      isLoggedIn: false,
      user: {},
    };
  },
};
</script>
