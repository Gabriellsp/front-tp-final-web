<template>
  <div class="doctors">
    <v-container>
      <v-data-table :headers="headers" :items="data">
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>{{ title }}</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialogNew" max-width="500px">
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color="primary"
                  dark
                  class="mb-2"
                  v-bind="attrs"
                  v-on="on"
                >
                  Novo Médico
                </v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="text-h5">Novo Médico</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="12">
                        <v-text-field
                          label="Nome"
                          v-model="editedItem.nome"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="8">
                        <v-text-field
                          label="E-mail"
                          v-model="editedItem.email"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-checkbox
                          label="Médico"
                          v-model="editedItem.isMedico"
                          disabled
                        ></v-checkbox>
                      </v-col>
                      <v-col cols="12" sm="6" md="12">
                        <v-text-field
                          label="Senha"
                          v-model="editedItem.senha"
                          type="password"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="6">
                        <v-text-field
                          label="Telefone"
                          v-model="editedItem.telefone"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="6">
                        <v-text-field
                          label="CEP"
                          v-model="editedItem.cep"
                          shaped
                          outlined
                          v-mask="cepMask"
                          @change="findCEP()"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="6">
                        <v-text-field
                          label="Logradouro"
                          v-model="editedItem.logradouro"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="6">
                        <v-text-field
                          label="Bairro"
                          v-model="editedItem.bairro"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="8">
                        <v-text-field
                          label="Cidade"
                          v-model="editedItem.cidade"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          label="UF"
                          v-model="editedItem.estado"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="6">
                        <v-menu
                          ref="menu"
                          v-model="dateMenu"
                          :close-on-content-click="true"
                          transition="scale-transition"
                          offset-y
                          min-width="auto"
                        >
                          <template v-slot:activator="{ on, attrs }">
                            <v-text-field
                              v-model="editedItem.datacontrato"
                              label="Data de Início"
                              prepend-icon="mdi-calendar"
                              readonly
                              v-bind="attrs"
                              v-on="on"
                            ></v-text-field>
                          </template>
                          <v-date-picker
                            v-model="editedItem.datacontrato"
                            :active-picker.sync="dateActivePicker"
                            :min="
                              new Date(Date.now()).toISOString().substr(0, 10)
                            "
                          ></v-date-picker>
                        </v-menu>
                      </v-col>
                      <v-col cols="12" sm="6" md="6">
                        <v-text-field
                          label="Salário"
                          v-model="editedItem.salario"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col v-if="editedItem.isMedico" cols="12" sm="6" md="6">
                        <v-autocomplete
                          v-model="editedItem.especialidade"
                          :items="especialidades"
                          prepend-icon="mdi-format-list-bulleted-type"
                          label="Especialidade"
                        ></v-autocomplete>
                      </v-col>
                      <v-col v-if="editedItem.isMedico" cols="12" sm="6" md="6">
                        <v-text-field
                          label="CRM"
                          v-model="editedItem.crm"
                          shaped
                          outlined
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="close">
                    Cancelar
                  </v-btn>
                  <v-btn color="blue darken-1" text @click="save">
                    Salvar
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
        </template>
        <template v-slot:[`item.isMedico`]="{ item }">
          <v-simple-checkbox
            v-model="item.isMedico"
            disabled
          ></v-simple-checkbox>
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
  created() {
    this.initialize();
    this.$nextTick(() => {
      this.editedItem = Object.assign({}, this.defaultItem);
    });
  },
  props: ["config"],
  methods: {
    findCEP() {
      const requestOptions = { method: "GET", credentials: "include" };
      fetch(
        `${this.$store.state.apiUrl}/api/enderecos/${this.editedItem.cep}`,
        requestOptions
      )
        .then((response) => response.json())
        .then((data) => {
          let cepData = data[0];
          if (cepData) {
            this.editedItem.logradouro = cepData.logradouro;
            this.editedItem.bairro = cepData.bairro;
            this.editedItem.cidade = cepData.cidade;
            this.editedItem.estado = cepData.estado;
          }
        });
    },
    initialize() {
      this.fetchDoctors();
    },
    close() {
      this.dialogNew = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
      });
    },
    save() {
      const requestOptions = {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(this.editedItem),
        credentials: "include",
      };

      let url = `${this.$store.state.apiUrl}/api/funcionario`;
      if (this.editedItem.isMedico) {
        url = `${this.$store.state.apiUrl}/api/medico`;
      }
      fetch(url, requestOptions)
        .then(() => {
          this.success = true;
          setTimeout(() => (this.success = false), 3000);
          this.close();
          this.fetchDoctors();
          this.$nextTick(() => {
            this.editedItem = Object.assign({}, this.defaultItem);
          });
        })
        .catch(() => {
          this.error = true;
          setTimeout(() => (this.error = false), 3000);
          this.$nextTick(() => {
            this.editedItem = Object.assign({}, this.defaultItem);
          });
        });
    },
    fetchDoctors() {
      const requestOptions = { method: "GET", credentials: "include" };
      fetch(`${this.$store.state.apiUrl}/api/funcionario`, requestOptions).then(
        (response) => {
          if (response.status == 201) {
            response.json().then((data) => (this.data = data));
          }
        }
      );
    },
  },
  data() {
    return {
      dateMenu: false,
      dateActivePicker: null,
      dialogNew: false,
      title: "Médicos",
      formTitle: "Novo Médico",
      cepMask: "##-###-###",
      headers: [
        {
          text: "Nome",
          value: "nome",
        },
        {
          text: "E-mail",
          value: "email",
        },
        {
          text: "Telefone",
          value: "telefone",
        },
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
        },
        {
          text: "Data de Início",
          value: "datacontrato",
        },
        {
          text: "Salário",
          value: "salario",
        },
        {
          text: "Médico",
          value: "isMedico",
        },
        {
          text: "Especialidade",
          value: "especialidade",
        },
        {
          text: "CRM",
          value: "crm",
        },
      ],
      data: [],
      editedItem: {},
      defaultItem: {
        nome: "",
        email: "",
        telefone: "",
        cep: "",
        logradouro: "",
        bairro: "",
        cidade: "",
        estado: "",
        datacontrato: new Date(Date.now()).toISOString().substr(0, 10),
        salario: "",
        senha: "",
        isMedico: true,
        especialidade: "",
        crm: "",
      },
      especialidades: this.$store.state.especialidades,
    };
  },
};
</script>
