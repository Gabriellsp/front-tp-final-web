<template>
  <div class="home pa-10">
    <v-container>
      <v-img
        :src="require('../assets/logo.png')"
        class="my-3"
        contain
        height="200"
        left
      />
      <span v-for="section in sections" :key="section.title">
        <h1 class="display-2 pl-8">{{ section.title }}</h1>
        <p class="pt-5">{{ section.text }}</p>
      </span>
    </v-container>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      sections: [
        {
          title: "Quem Somos",
          text: "Fundada em 2021. a ClinicaSaúde é uma clínica médica especializada em atendimento de alta qualidade e acessível. Somos um pequeno grupo de pessoas muito dedicadas e atenciosas, dando o nosso melhor para ajudar quem precisa.",
        },
        {
          title: "Nossa missão",
          text: "Nossa missão é trazer atendimento médico de forma acessível para todas as pessoas. Isso só é possível com muita força de vontade e ajuda de diversas pessoas.",
        },
        {
          title: "Nossos valores",
          text: "Acreditamos que a saúde vem antes de tudo, e precisa ser fornecida para todos e todas. Também acreditamos que a honestidade e compaixão são fundamentais em qualquer situação.",
        },
      ],
    };
  },
};
</script>