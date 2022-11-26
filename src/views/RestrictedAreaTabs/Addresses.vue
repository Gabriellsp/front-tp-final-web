<template>
  <div class="addresses">
    <v-container>
      <v-data-table :headers="headers" :items="data">
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>{{ title }}</v-toolbar-title>
          </v-toolbar>
        </template>
        <template v-slot:no-data>
          <v-btn color="primary" @click="initialize"> Atualizar </v-btn>
        </template>
      </v-data-table>
    </v-container>
  </div>
</template>

<script>
export default {
  props: ["config"],
  created() {
        this.initialize();
  },
  methods: {    
    initialize () {
        const requestOptions = {method: "GET", credentials: 'include'};
        fetch(`${this.$store.state.apiUrl}/api/enderecos`, requestOptions)
        .then(response => response.json())
        .then(data => (this.data = data));
    },
  },
  data() {
    return {
      title: "Endereços",
      headers: [
        {
          text: "CEP",
          value: "cep",
        },
        {
          text: "Logradouro",
          value: "logradouro",
        },
        {
          text: "Bairro",
          value: "bairro",
        },
        {
          text: "Cidade",
          value: "cidade",
        },
        {
          text: "Estado",
          value: "estado",
        }
      ],
      data: [],
    };
  },
};
</script>