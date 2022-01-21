<template>
  <v-container>
    <v-row class="text-center">

      <v-col class="mb-4">

        <v-form v-model="valid">
          <v-container>
            <v-row>

              <v-col
                  cols="12"
                  md="4"
              >
                <v-text-field
                    v-model="cardNumber"
                    :rules="required"
                    label="Card Number"
                    required
                ></v-text-field>
              </v-col>

              <v-col
                  cols="12"
                  md="4"
              >
                <v-text-field
                    v-model="holderName"
                    :rules="required"
                    label="Holder Name"
                    required
                ></v-text-field>
              </v-col>

              <v-col
                  cols="12"
                  md="4"
              >
                <v-text-field
                    v-model="securityCode"
                    :rules="required"
                    label="CVV"
                    required
                ></v-text-field>
              </v-col>


              <v-col
                  cols="12"
                  md="6"
              >
                <v-text-field
                    v-model="expirationMonth"
                    :rules="required"
                    label="Expiration month"
                    required
                    :counter="2"
                ></v-text-field>
              </v-col>



              <v-col
                  cols="12"
                  md="6"
              >
                <v-text-field
                    v-model="expirationYear"
                    :rules="required"
                    label="Expiration Year"
                    required
                    :counter="4"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>

          <v-btn
              :disabled="!valid"
              color="success"
              class="mr-4"
              :loading="loading"
              @click="generateHash"
          >
            get hash
          </v-btn>
          <v-btn
              color="primary"
              class="mr-4"
              @click="setFakeData"
          >
            set data fake
          </v-btn>
          <v-btn
              color="orange"
              class="mr-4"
              @click="clearForm"
          >
            Clear form
          </v-btn>
        </v-form>
      </v-col>
    </v-row>

    <v-row class="text-center">
      <v-col
          cols="12"
          md="12"
          style="background: #eeeeee "

      >
         <h3>Result</h3>
        {{ result }}
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  export default {
    name: 'HelloWorld',

    data: () => ({
      cardData: {
        cardNumber: "5272579617351336",
        holderName: "rasmus lerdorf",
        securityCode: "173",
        expirationMonth: "12",
        expirationYear: "2025",
      },

      cardNumber: "",
      holderName: "",
      securityCode: "",
      expirationMonth: "",
      expirationYear: "",

      valid: false,
      loading: false,
      firstname: '',
      lastname: '',
      required: [
        v => !!v || 'Name is required',
      ],

      result: null
    }),

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
        this.loading = true;
        this.result = null;

        const checkout = await this.directCheckout();
        checkout.getCardHash(
            this.cardData,
            (cardHash) => {
              this.loading = false;
              this.result = cardHash;
              // eslint-disable-next-line
              console.log(cardHash);
            },
            (error) => {
              this.loading = false;
              this.result = error;
              console.error(error.message);
            }
        );
      },

      setFakeData() {
        this.cardNumber = "5272579617351336";
        this.holderName = "Rasmus Lerdorf";
        this.securityCode = "173";
        this.expirationMonth = "12";
        this.expirationYear = "2025";
      },

      clearForm() {
        this.cardNumber = "";
        this.holderName = "";
        this.securityCode = "";
        this.expirationMonth = "";
        this.expirationYear = "";
      }

    }
  }
</script>
