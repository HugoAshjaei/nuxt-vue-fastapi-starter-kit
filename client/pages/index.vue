<template>
  <form>
    <v-text-field
      v-model="name"
      :error-messages="nameErrors"
      :counter="10"
      label="Name"
      required
      @input="$v.name.$touch()"
      @blur="$v.name.$touch()"
    ></v-text-field>
    <v-text-field v-model="city" label="City"></v-text-field>

    <v-btn class="mr-4" @click.prevent="submit"> submit </v-btn>
    <br>
    <h3>Note:</h3>
    <p>Your information is stored in the cookies and will be redirected to weather page, until you click the sign out button!</p>
    <p>If you have already logged in, just enter your name.</p>
  </form>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required, maxLength } from "vuelidate/lib/validators";
export default {
  mixins: [validationMixin],
  validations: {
    name: { required, maxLength: maxLength(10) },
  },
  data: () => ({
    name: "",
    city: "",
  }),
  computed: {
    nameErrors() {
      const errors = [];
      if (!this.$v.name.$dirty) return errors;
      !this.$v.name.maxLength &&
        errors.push("Name must be at most 10 characters long");
      !this.$v.name.required && errors.push("Name is required.");
      return errors;
    },
  },
  mounted() {
    const cookieData = this.$cookiz.get("weather-data");
    if (cookieData) {
      this.$router.push("/weather?name=" + cookieData.name);
    }
  },
  methods: {
    submit() {
      let query = "";
      if (this.name) {
        query += "name=" + this.name;
      }
      if (this.city) {
        query += "&city=" + this.city;
      }
      if (query != "" && this.name) {
        // pass user to weather
        this.$router.push("/weather?" + query);
      } else {
        this.$v.$touch();
      }
    },
  },
};
</script>
