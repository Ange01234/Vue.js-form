<template>
  <div class="flex flex-col md:flex-row h-dvh bg-cover md:bg-center bg-left" style="background-image: url('/gradient.svg');">
    <!-- Partie gauche (image) -->
    <div class="w-full md:w-1/2 flex items-center justify-center relative py-10 h-full">
      <div class="absolute w-3/4 h-3/4"></div>
      <img src="/character.svg" alt="Illustration" class="relative w-2/3 md:w-1/2" />
    </div>

    <!-- Partie droite (formulaire) -->
    <div class="w-full md:w-1/2 backdrop-blur-lg shadow-lg flex justify-center py-10 h-full">
      <div class="p-5 md:p-5 w-11/12 md:w-3/4">
        <h1 class="text-2xl md:text-3xl font-medium mb-6 text-black text-center md:text-left">Yellow ! Rejoignez l’aventure !</h1>
        <p v-if="step === 1 || step === 2" class="text-gray-600 mb-6 text-lg md:text-xl text-center md:text-left">Identifiez-vous</p>
        <p v-if="step === 3" class="text-gray-600 mb-6 text-lg md:text-xl text-center md:text-left">Plus qu'une étape</p>

        <hr class="mb-5">

        <form @submit.prevent="submitForm">
          <!-- Étape 1 -->
          <div v-if="step === 1">
            <div class="mb-6 md:mb-10">
              <label class="block text-gray-700">Nom</label>
              <input required type="text" v-model="form.nom" placeholder="LABELLE" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none " />
            </div>
            <div class="mb-6 md:mb-10">
              <label class="block text-gray-700">Prénom</label>
              <input required type="text" v-model="form.prenom" placeholder="MAYA" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-6 md:mb-10">
              <label class="block text-gray-700">Date de naissance</label>
              <input required type="date" v-model="form.date" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
              <p v-if="dateError" class="text-red-500 mt-2 text-center">{{ dateError }}</p>
            </div>
          </div>

          <!-- Étape 2 -->
          <div v-if="step === 2">
            <div class="mb-6 md:mb-10">
              <label class="block text-gray-700">Numéro de téléphone</label>
              <input required type="text" v-model="form.numero" @focus="formatNumero" @input="formatNumero" placeholder="01 XXXXXXXX" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-6 md:mb-10">
              <label class="block text-gray-700">Ville</label>
              <input required type="text" v-model="form.ville" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-6 md:mb-10">
              <label class="block text-gray-700">Quartier</label>
              <input required type="text" v-model="form.quartier" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
            </div>
          </div>

          <!-- Étape 3 -->
          <div v-if="step === 3">
            <label class="block text-gray-700">Vérification OTP</label>
            <p class="text-gray-600 mb-2">Saisir le code envoyé au {{ form.numero }}</p>
            <div class="flex space-x-2">
              <input required
                v-for="(digit, index) in otp"
                :key="index"
                type="text"
                maxlength="1"
                v-model="otp[index]"
                :ref="`otpInput${index}`"
                @input="focusNext(index)"
                :disabled="!canProceedToNextStep()"
                class="w-12 h-12 md:w-15 md:h-15 text-center border rounded-lg focus:outline-none focus:ring-2 focus:ring-yellow-400 bg-white"
              />
            </div>
            <p v-if="otpError" class="text-red-500 mt-2 text-center">{{ otpError }}</p>
          </div>

          <!-- Boutons -->
          <div class="flex flex-col mt-6 space-y-4">
            <button type="button" @click="nextStep" v-if="step < 3" class="bg-yellow-500 text-white py-3 md:py-4 px-4 rounded-lg w-full">Continuer</button>
            <button type="submit" v-if="step === 3" class="bg-yellow-500 text-white py-3 md:py-4 px-4 rounded-lg w-full">Vous êtes prêt !</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

  
<script>
export default {
  data() {
    return {
      step: 1,
      form: {
        nom: "",
        prenom: "",
        date: "",
        numero: "",
        ville: "",
        quartier: ""
      },
      otp: Array(4).fill(""), // Tableau pour stocker les 4 chiffres de l'OTP
      otpError: "",
      dateError: "",
    };
  },
  methods: {
    canProceedToNextStep() {
      if (this.step === 1) {
        return this.form.nom && this.form.prenom && this.form.date;
      } else if (this.step === 2) {
        return this.form.numero && this.form.ville && this.form.quartier;
      }
      return true;
    },
    formatNumero(event) {
      let input = event.target.value.replace(/\D/g, ""); // Supprime tout sauf les chiffres
      if (!input.startsWith("01")) {
        input = "01"; // Force le "01" au début
      }
      this.form.numero = input.slice(0, 10); // Limite à 10 caractères (01 + 8 chiffres)
    },
    calculateAge(date) {
      const birthDate = new Date(date);
      const today = new Date();
      let age = today.getFullYear() - birthDate.getFullYear();
      const monthDiff = today.getMonth() - birthDate.getMonth();

      if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
        age--;
      }
      return age;
    },
    nextStep() {
      if (!this.canProceedToNextStep()) return;
      if (this.step === 1) {
        const age = this.calculateAge(this.form.date);
        if (age < 18) {
          this.dateError="Vous devez avoir au moins 18 ans pour vous inscrire.";
          return;
        }
      }
      if (this.step < 3) this.step++;
    },
    prevStep() {
      if (this.step > 1) this.step--;
    },
    submitForm() {
      console.log("Formulaire soumis :", this.form);
      console.log("otp:", this.otp);
    },
    focusNext(index) {
      if (this.otp[index].length === 1 && index < 3) {
        this.$refs[`otpInput${index + 1}`][0].focus();
      }
    }
  },
};
</script>
  
<style>
@import "tailwindcss";

label {
  font-size: 21px;
  font-weight: bold;
}
</style>
  