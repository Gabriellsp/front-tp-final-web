<template>
  <div class="register-schedule">
    <v-container fluid fill-height justify-center>
      <v-flex xs12 sm4 elevation-6>
        <v-alert type="success" v-if="success"
          >Consulta agendada com sucesso</v-alert
        >
        <v-alert type="error" v-if="error">Erro ao agendar a consulta</v-alert>
        <v-toolbar flat>
          <v-toolbar-title><h4>Agendamento de Consultas</h4></v-toolbar-title>
        </v-toolbar>
        <v-card flat>
          <v-card-text class="pt-4">
            <div>
              <v-form>
                <v-text-field
                  label="Nome"
                  v-model="fields.nome.data"
                  prepend-icon="mdi-account"
                  shaped
                  outlined
                ></v-text-field>
                <v-text-field
                  label="E-mail"
                  v-model="fields.email.data"
                  prepend-icon="mdi-at"
                  shaped
                  outlined
                ></v-text-field>
                <v-text-field
                  label="Telefone"
                  v-model="fields.telefone.data"
                  prepend-icon="mdi-phone"
                  shaped
                  outlined
                ></v-text-field>
                <v-autocomplete
                  v-model="fields.especialidade.data"
                  :items="fields.especialidade.items"
                  prepend-icon="mdi-format-list-bulleted-type"
                  label="Especialidade"
                  @change="specialtyChanged()"
                ></v-autocomplete>
                <v-autocomplete
                  v-model="fields.codigomedico.data"
                  :items="fields.codigomedico.items"
                  prepend-icon="mdi-doctor"
                  label="Médico"
                  item-text="nome"
                  item-value="codigo"
                  :disabled="fields.codigomedico.disabled"
                  @change="doctorChanged()"
                ></v-autocomplete>
                <v-menu
                  ref="menu"
                  v-model="fields.data.menu"
                  :close-on-content-click="true"
                  transition="scale-transition"
                  offset-y
                  min-width="auto"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-model="fields.data.data"
                      label="Data da Consulta"
                      prepend-icon="mdi-calendar"
                      readonly
                      v-bind="attrs"
                      v-on="on"
                      :disabled="fields.data.disabled"
                    ></v-text-field>
                  </template>
                  <v-date-picker
                    v-model="fields.data.data"
                    :active-picker.sync="fields.data.activePicker"
                    :min="new Date(Date.now()).toISOString().substr(0, 10)"
                    @change="dateChanged()"
                  ></v-date-picker>
                </v-menu>
                <v-select
                  v-model="fields.horario.data"
                  :items="fields.horario.items"
                  prepend-icon="mdi-clock"
                  label="Horário"
                  :disabled="fields.horario.disabled"
                ></v-select>
                <v-layout justify-space-between>
                  <v-btn @click="submit" color="primary"
                    >Agendar Consulta</v-btn
                  >
                </v-layout>
              </v-form>
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
    validate() {
      this.$refs.form.validate();
    },
    reset() {
      this.$refs.form.reset();
    },
    resetValidation() {
      this.$refs.form.resetValidation();
    },
    submit() {
      let formData = {};
      for (let field in this.fields) {
        formData[field] = this.fields[field].data;
      }
      const requestOptions = {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(formData),
        credentials: "include",
      };
      fetch(`${this.$store.state.apiUrl}/api/agenda`, requestOptions)
        .then(() => {
          this.success = true;
          setTimeout(() => (this.success = false), 3000);
        })
        .catch(() => {
          this.error = true;
          setTimeout(() => (this.error = false), 3000);
        });
    },
    clearAndDisableFields(fields) {
      for (const field of fields) {
        this.fields[field].data = "";
        this.fields[field].disabled = true;
      }
      this.fields[fields[0]].disabled = false;
    },
    specialtyChanged() {
      if (this.fields.especialidade.data) {
        this.fields.codigomedico.disabled = false;
        this.fetchDoctors();
      }

      this.clearAndDisableFields(["codigomedico", "data", "horario"]);
    },
    doctorChanged() {
      if (this.fields.codigomedico.data) {
        this.fields.data.disabled = false;
      }

      this.clearAndDisableFields(["data", "horario"]);
    },
    dateChanged() {
      if (this.fields.data.data) {
        this.fields.horario.disabled = false;
        this.fetchTime();
      }

      this.clearAndDisableFields(["horario"]);
    },
    fetchDoctors() {
      const requestOptions = { method: "GET", credentials: "include" };
      fetch(
        `${this.$store.state.apiUrl}/api/medico/${this.fields.especialidade.data}`,
        requestOptions
      ).then((response) => {
        if (response.status == 201) {
          response
            .json()
            .then((data) => (this.fields.codigomedico.items = data));
        }
      });
    },
    fetchTime() {
      const requestOptions = {
        method: "PATCH",
        credentials: "include",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          medicoId: this.fields.codigomedico.data,
          data: this.fields.data.data,
        }),
      };

      fetch(`${this.$store.state.apiUrl}/api/agenda`, requestOptions).then(
        (response) => {
          if (response.status == 201) {
            response.json().then((data) => (this.fields.horario.items = data));
          }
        }
      );
    },
  },
  data() {
    return {
      fields: {
        especialidade: {
          data: "",
          disabled: false,
          items: this.$store.state.especialidades,
        },
        codigomedico: {
          data: "",
          disabled: true,
          items: [],
        },
        data: {
          data: "",
          menu: false,
          activePicker: null,
          disabled: true,
        },
        horario: {
          data: "",
          disabled: true,
          items: [],
        },
        nome: {
          data: "",
        },
        email: {
          data: "",
        },
        telefone: {
          data: "",
        },
      },
      success: false,
      error: false,
    };
  },
};
</script>
