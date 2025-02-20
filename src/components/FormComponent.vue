<template>
  <div class="flex h-screen bg-cover bg-center" style="background-image: url('/gradient.svg');">
    <!-- Partie gauche avec l'image -->
    <div class="w-1/2 flex items-center justify-center relative">
      <div class="absolute w-3/4 h-3/4"></div>
      <img src="/character.svg" alt="Illustration" class="relative w-2/3" />
    </div>

    <!-- Partie droite avec le formulaire -->
    <div class="w-1/2 backdrop-blur-lg shadow-lg">
      <div class="p-10 my-10 w-3/4">
        <h1 class="text-3xl font-medium mb-10 text-black">Yellow ! Rejoignez l’aventure !</h1>
        <p v-if="step === 1 || step === 2" class="text-gray-600 mb-6 text-xl">Identifiez-vous</p>
        <p v-if="step === 3" class="text-gray-600 mb-6 text-xl">Plus qu'une étape</p>
        
        <hr class="mb-5">

        <form @submit.prevent="submitForm">
          <!-- Étape 1 -->
          <div v-if="step === 1">
            <div class="mb-10">
              <label class="block text-gray-700">Nom</label>
              <input type="text" v-model="form.nom" placeholder="LABELLE" class="w-full px-4 py-6 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-10">
              <label class="block text-gray-700">Prénom</label>
              <input type="text" v-model="form.prenom" placeholder="MAYA" class="w-full px-4 py-6 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-10">
              <label class="block text-gray-700">Date de naissance</label>
              <input type="date" v-model="form.date" class="w-full px-4 py-6 border rounded-lg focus:outline-none" />
            </div>
          </div>

          <!-- Étape 2 -->
          <div v-if="step === 2">
            <div class="mb-10">
              <label class="block text-gray-700">Numéro de téléphone</label>
              <input type="text" v-model="form.numero" @focus="formatNumero" @input="formatNumero" placeholder="01 XXXXXXXX" class="w-full px-4 py-6 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-10">
              <label class="block text-gray-700">Ville</label>
              <input type="text" v-model="form.ville" class="w-full px-4 py-6 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-10">
              <label class="block text-gray-700">Quartier</label>
              <input type="text" v-model="form.quartier" class="w-full px-4 py-6 border rounded-lg focus:outline-none" />
            </div>
          </div>

          <!-- Étape 3 -->
          <div v-if="step === 3">
            <label class="block text-gray-700">Vérification OTP</label>
            <p class="text-gray-600 mb-2">Saisir le code envoyé au {{ form.numero }}</p>
            <div class="flex space-x-2">
              <input
                v-for="(digit, index) in otp"
                :key="index"
                type="text"
                maxlength="1"
                v-model="otp[index]"
                :ref="`otpInput${index}`"
                @input="focusNext(index)"
                class="w-15 h-15 text-center border rounded-lg focus:outline-none focus:ring-2 focus:ring-yellow-400 bg-white mr-5"
              />
              <p v-if="otpError" class="text-red-500 mt-2">{{ otpError }}</p>
            </div>
          </div>
          
          <!-- Boutons de navigation -->
          <div class="flex justify-between mt-4">
            <!-- <button type="button" @click="prevStep" v-if="step > 1" class="bg-gray-300 text-black py-6 px-4 rounded-lg hover:bg-gray-400">Précédent</button> -->
            <button type="button" @click="nextStep" v-if="step < 3" class="bg-yellow-500 text-white py-6 px-4 rounded-lg w-full">Continuer</button>
            <button type="submit" v-if="step === 3" class="bg-yellow-500 text-white py-6 px-4 rounded-lg w-full self-end">Vous êtes prêt ! </button>
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
    };
  },
  methods: {
    formatNumero(event) {
      let input = event.target.value.replace(/\D/g, ""); // Supprime tout sauf les chiffres
      if (!input.startsWith("01")) {
        input = "01"; // Force le "01" au début
      }
      this.form.numero = input.slice(0, 10); // Limite à 10 caractères (01 + 8 chiffres)
    },
    nextStep() {
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
  font-weight: semi-bold;
}
</style>
  