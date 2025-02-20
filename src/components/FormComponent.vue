<template>
  <div class="flex h-screen bg-cover bg-center" style="background-image: url('/gradient.svg');">
    <!-- Partie gauche avec l'image -->
    <div class="w-1/2 flex items-center justify-center relative">
      <div class="absolute w-3/4 h-3/4"></div>
      <img src="/character.svg" alt="Illustration" class="relative w-2/3" />
    </div>

    <!-- Partie droite avec le formulaire -->
    <div class="w-1/2 backdrop-blur-lg shadow-lg">
      <div class="p-10 my-10 w-full">
        <h1 class="text-2xl font-bold mb-4 text-black">Yellow ! Rejoignez l’aventure !</h1>
        <p class="text-gray-600 mb-6">Identifiez-vous</p>
        
        <hr>

        <form @submit.prevent="submitForm">
          <!-- Étape 1 -->
          <div v-if="step === 1">
            <div class="mb-4">
              <label class="block text-gray-700">Nom</label>
              <input type="text" v-model="form.nom" placeholder="" class="w-full px-4 py-2 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-4">
              <label class="block text-gray-700">Prénom</label>
              <input type="text" v-model="form.prenom" placeholder="" class="w-full px-4 py-2 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-4">
              <label class="block text-gray-700">Prénom</label>
              <input type="date" v-model="form.date" class="w-full px-4 py-2 border rounded-lg focus:outline-none" />
            </div>
          </div>

          <!-- Étape 2 -->
          <div v-if="step === 2">
            <div class="mb-4">
              <label class="block text-gray-700">Numéro de téléphone</label>
              <input type="text" v-model="form.numero" class="w-full px-4 py-2 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-4">
              <label class="block text-gray-700">Ville</label>
              <input type="text" v-model="form.ville" class="w-full px-4 py-2 border rounded-lg focus:outline-none" />
            </div>
            <div class="mb-4">
              <label class="block text-gray-700">Quartier</label>
              <input type="text" v-model="form.quartier" class="w-full px-4 py-2 border rounded-lg focus:outline-none" />
            </div>
          </div>

          <!-- Étape 3 -->
          <div v-if="step === 3">
            <div class="mb-4">
              <label class="block text-gray-700">Code OTP</label>
              <input type="text" v-model="form.otp" class="w-full px-4 py-2 border rounded-lg focus:outline-none" />
            </div>
          </div>
          
          <!-- Boutons de navigation -->
          <div class="flex justify-between mt-4">
            <!-- <button type="button" @click="prevStep" v-if="step > 1" class="bg-gray-300 text-black py-2 px-4 rounded-lg hover:bg-gray-400">Précédent</button> -->
            <button type="button" @click="nextStep" v-if="step < 3" class="bg-yellow-500 text-white py-2 px-4 rounded-lg hover:bg-yellow-600">Continuer</button>
            <button type="submit" v-if="step === 3" class="bg-green-500 text-white py-2 px-4 rounded-lg hover:bg-green-600">Continuer</button>
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
        quartier: "",
        otp: "",
      },
    };
  },
  methods: {
    nextStep() {
      if (this.step < 3) this.step++;
    },
    prevStep() {
      if (this.step > 1) this.step--;
    },
    submitForm() {
      console.log("Formulaire soumis :", this.form);
    },
  },
};
</script>
  
<style>
@import "tailwindcss";
</style>
  