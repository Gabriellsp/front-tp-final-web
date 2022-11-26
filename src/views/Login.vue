<template>
  <div class="login">
    <v-container fluid fill-height justify-center>
      <v-flex xs12 sm4 elevation-6>
        <v-alert type="error" v-if="error">Usuário ou senha inválidos</v-alert>
        <v-toolbar>
          <v-toolbar-title>
            <h4>{{ title }}</h4>
          </v-toolbar-title>
        </v-toolbar>
        <v-card>
          <v-card-text class="pt-4">
            <div>
              <v-card flat>
                <v-card-text class="pt-4">
                  <div>
                    <v-form>
                      <v-text-field
                        shaped
                        outlined
                        label="E-mail"
                        v-model="data.login"
                        :rules="emailRules"
                      ></v-text-field>
                      <v-text-field
                        shaped
                        outlined
                        label="Senha"
                        v-model="data.senha"
                        type="password"
                      ></v-text-field>
                      <v-layout justify-space-between>
                        <v-btn @click="login" color="primary">Entrar</v-btn>
                      </v-layout>
                    </v-form>
                  </div>
                </v-card-text>
              </v-card>
            </div>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-container>
  </div>
</template>

<script>
export default {
  methods: {
    login() {
      const requestOptions = {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(this.data),
        credentials: 'include',
      };
      fetch(`${this.$store.state.apiUrl}/api/usuarios/login`, requestOptions)
        .then((response) => {
          if (response.status == 200) {
            this.$store.commit('getUser');
            this.$router.push('/restricted-area');
          } else {
            this.error = true;
            setTimeout(() => (this.error = false), 3000);
          }
        })
  
    },
  },
  data() {
    return {
      title: "Login",
      data: {
        login: "",
        senha: "",
      },
      emailRules: [
        (v) =>
          /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) ||
          "E-mail deve ser válido",
      ],
      error: false,
    };
  },
};
</script>