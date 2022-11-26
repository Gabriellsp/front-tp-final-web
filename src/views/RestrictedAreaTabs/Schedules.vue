<template>
  <div class="schedules">
    <v-container>
      <v-data-table :headers="headers" :items="data">
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>{{ title }}</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn v-if="$store.state.user.isMedico" v-on:click="toggleFilterByDoctor()" :class="filterByDoctor ? 'v-btn--active' : ''">Somente meus</v-btn>
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
    toggleFilterByDoctor() {
      this.filterByDoctor = !this.filterByDoctor;
      this.initialize();
    },
    initialize () {
        const requestOptions = {method: "GET", credentials: 'include'};
        let url = `${this.$store.state.apiUrl}/api/agenda`;
        if (this.filterByDoctor) {
          url = `${this.$store.state.apiUrl}/api/agenda/medico`;
        }
        fetch(url, requestOptions)
        .then(response => response.json())
        .then(data => (this.data = data));
    },
  },
  data() {
    return {
      title: "Agendamentos",
      headers: [
        {
          text: "Nome",
          value: "nome",
        },
        {
          text: "Email",
          value: "email",
        },
        {
          text: "Telefone",
          value: "telefone",
        },
        {
          text: "Data",
          value: "data",
        },
        {
          text: "Horário",
          value: "horario",
        },
        {
          text: "Médico",
          value: "nomeMedico",
        }
      ],
      data: [],
      filterByDoctor: false,
    };
  },
};
</script>