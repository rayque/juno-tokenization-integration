<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-name-dark.png"
          width="100"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">Latest Release</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <HelloWorld/>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    cardData: {
      cardNumber: "5272579617351336",
      holderName: "rasmus lerdorf",
      securityCode: "173",
      expirationMonth: "12",
      expirationYear: "2025",
    },
  }),
  mounted() {
    this.generateHash();
  },

  methods: {
    async directCheckout() {
      await this.$loadScript(process.env.VUE_APP_JUNO_PUBLIC_URL_CHECKOUT);
      const junoPublicToken = process.env.VUE_APP_JUNO_PUBLIC_TOKEN;

      if (process.env.VUE_APP_JUNO_ENVIROMENT === "sandbox") {
        // eslint-disable-next-line
        return new DirectCheckout(junoPublicToken, false);
      }
      // eslint-disable-next-line
      return new DirectCheckout(junoPublicToken);
    },

    async generateHash() {
      const checkout = await this.directCheckout();
      // eslint-disable-next-line
      const cardData = this.cardData;
      checkout.getCardHash(
          this.cardData,
          (cardHash) => {
            // eslint-disable-next-line
            console.log(cardHash);
          },
          (error) => {
            console.error(error.message);
          }
      );
    },
  }
};
</script>
