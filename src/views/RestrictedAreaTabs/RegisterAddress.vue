<template>
  <div class="register-address">
    <v-container fluid fill-height justify-center>
      <v-flex xs12 sm4 elevation-6>
        <div class="text-form">
          <v-alert type="success" v-if="success"
            >Endereço cadastrado com sucesso</v-alert
          >
          <v-alert type="error" v-if="error"
            >Erro ao cadastrar o endereço</v-alert
          >
          <v-toolbar flat>
            <v-toolbar-title><h4>Cadastrar Endereço</h4></v-toolbar-title>
          </v-toolbar>
          <v-card flat>
            <v-card-text class="pt-4">
              <div>
                <v-form>
                  <v-text-field
                    v-for="(field, fieldName) in fields"
                    :key="field.name"
                    v-model="fields[fieldName].data"
                    :rules="field.rules"
                    :label="field.name"
                    v-mask="field.mask"
                    shaped
                    outlined
                  ></v-text-field>
                  <v-layout justify-space-between>
                    <v-btn @click="submit" color="primary">Cadastrar</v-btn>
                  </v-layout>
                </v-form>
              </div>
            </v-card-text>
          </v-card>
        </div>
      </v-flex>
    </v-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      fields: {
        cep: {
          name: "CEP",
          rules: [],
          data: "",
          mask: "##-###-###",
        },
        logradouro: {
          name: "Logradouro",
          rules: [],
          data: "",
          mask: "",
        },
        bairro: {
          name: "Bairro",
          rules: [],
          data: "",
          mask: "",
        },
        cidade: {
          name: "Cidade",
          rules: [],
          data: "",
          mask: "",
        },
        estado: {
          name: "Estado",
          rules: [],
          data: "",
          mask: "",
        },
      },
      success: false,
      error: false,
    };
  },
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
      fetch(`${this.$store.state.apiUrl}/api/enderecos`, requestOptions)
        .then(() => {
          this.success = true;
          setTimeout(() => (this.success = false), 3000);
        })
        .catch(() => {
          this.error = true;
          setTimeout(() => (this.error = false), 3000);
        });
    },
  },
};
</script>
