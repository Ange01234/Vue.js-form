<template>
  <div class="flex flex-col md:flex-row h-dvh bg-cover md:bg-center bg-left" style="background-image: url('/gradient.svg');">
    <!-- Partie gauche (image) -->
    <div class="w-full md:w-1/2 flex items-center justify-center relative md:py-10 h-2/5 md:h-full">
      <img src="/character.svg" alt="Illustration" class="relative w-2/3 md:w-1/2" />
    </div>

    <!-- Partie droite (formulaire) -->
    <div class="w-full md:w-1/2 bg-yellow-100/30 backdrop-blur-lg md:shadow-lg flex justify-center md:py-10 h-3/5 md:h-full">
      <div class="p-0 md:p-5 w-11/12 md:w-3/4 max-h-80 flex flex-col justify-between">
        <h1 class="text-2xl md:text-3xl font-medium mt-2 mb-2 md:mb-6 text-black text-center md:text-left">Yellow ! Rejoignez l’aventure !</h1>

        <div class="flex items-center space-x-2 mb-1">
          <template v-for="index in steps" :key="index">
            <div
              class="w-6 h-6 flex items-center justify-center rounded-full border-2"
              :class="index <= currentStep ? 'bg-yellow-400 border-yellow-400' : 'bg-white border-gray-300'"
            >
              <svg v-if="index < currentStep" class="w-4 h-4 text-white" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
            <div v-if="index < steps" class="flex-1 h-1" :class="index < currentStep ? 'bg-yellow-400' : 'bg-gray-300'">
            </div>
          </template>
        </div>
        <div class="flex justify-between text-sm text-gray-700 mb-7">
          <span v-for="index in steps" :key="index">Étape {{ index }}</span>
        </div>

        <form @submit.prevent="submitForm" class="flex flex-col justify-between">

          <div v-if="currentStep === 1">
            <div class="mb-3 md:mb-8">
              <label class="block text-gray-700">Nom *</label>
              <input required="true" type="text" v-model="form.nom" placeholder="LABELLE" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none " />
              <p v-if="error.nom" class="text-red-500 text-center">{{ error.nom }}</p>
            </div>
            <div class="mb-3 md:mb-8">
              <label class="block text-gray-700">Prénom *</label>
              <input required type="text" v-model="form.prenom" placeholder="MAYA" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
              <p v-if="error.prenom" class="text-red-500 text-center">{{ error.prenom }}</p>
            </div>
            <div class="mb-3 md:mb-8">
              <label class="block text-gray-700">Date de naissance *</label>
              <input required type="date" v-model="form.date" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
              <p v-if="error.date" class="text-red-500 text-center">{{ error.date }}</p>
            </div>
          </div>

          <div v-if="currentStep === 2">
            <div class="mb-3 md:mb-8">
              <label class="block text-gray-700">Numéro de téléphone *</label>
              <input required type="text" v-model="form.numero" @focus="formatNumero" @input="formatNumero" placeholder="01 XXXXXXXX" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
              <p v-if="error.number" class="text-red-500 text-center">{{ error.number }}</p>
            </div>
            <div class="mb-3 md:mb-8">
              <label class="block text-gray-700">Ville *</label>
              <input required type="text" v-model="form.town" @input="filterTowns" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
              <ul v-if="Towns.length">
                <li v-for="(town, index) in suggestions" :key="index" @click="selectTown(town)">
                  {{ town }}
                </li>
              </ul>
              <p v-if="error.town" class="text-red-500 text-center">{{ error.town }}</p>
            </div>
            <div class="mb-3 md:mb-8">
              <label class="block text-gray-700">Quartier *</label>
              <input required type="text" v-model="form.district" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
              
              <p v-if="error.district" class="text-red-500 text-center">{{ error.district }}</p>
            </div>
          </div>


          <div v-if="currentStep === 3">
            <label class="block text-gray-700">Vérification OTP</label>
            <p class="text-gray-600 mb-2">Saisir le code envoyé au {{ form.numero }}</p>
            <div class="flex space-x-4 justify-center mb-6">
              <input required
                v-for="(digit, index) in otp"
                :key="index"
                type="text"
                maxlength="1"
                v-model="otp[index]"
                :ref="`otpInput${index}`"
                @input="focusNext(index)"
                class="w-12 h-12 md:w-15 md:h-15 text-center border rounded-lg focus:outline-none focus:ring-2 focus:ring-yellow-400 bg-white"
              />
            </div>
            <p v-if="otpError" class="text-red-500 mt-2 text-center">{{ otpError }}</p>
          </div>

          <div v-if="currentStep === 4">
            <div class="mb-3 md:mb-8">
              <label class="block text-gray-700">Mot de passe *</label>
              <input required type="password" v-model="form.password" placeholder="********" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
              <p v-if="error.password" class="text-red-500 text-center">{{ error.password }}</p>
            </div>
            <div class="mb-3 md:mb-8">
              <label class="block text-gray-700">Confirmer le mot de passe *</label>
              <input required type="password" v-model="form.confirmPassword" placeholder="********" class="w-full px-4 py-3 md:py-4 border rounded-lg focus:outline-none" />
              <p v-if="error.confirmPassword" class="text-red-500 text-center">{{ error.confirmPassword }}</p>
            </div>
          </div>

          <div class="flex justify-between mt-4">
            <button @click="prevStep" :disabled="currentStep === 1" class="px-7 py-4 bg-gray-300 rounded disabled:opacity-50 flex items-center">
              <img width="20" height="20" src="https://img.icons8.com/fluency-systems-regular/48/left.png" alt="left"/>
              <span class="ml-1">Précédent</span>
            </button>
            <button @click="nextStep" class="px-7 py-4 bg-yellow-500 text-white rounded disabled:opacity-50 flex items-center">
              <span class="mr-1">Confirmer</span>
              <img width="20" height="20" src="https://img.icons8.com/ios/50/right--v1.png" alt="right--v1"/>
            </button>
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
      steps:4,
      currentStep: 1,
      form: {
        nom: "",
        prenom: "",
        date: "",
        numero: "",
        town: "",
        district: ""
      },
      otp: Array(4).fill(""), // Tableau pour stocker les 4 chiffres de l'OTP
      otpError: "",
      error: {},
      suggestions: [],
      Towns: ['BANIKOARA', 'GOGOUNOU', 'KANDI', 'KARIMAMA', 'MALANVILLE', 'SEGBANA', 'BOUKOUMBE', 'COBLY', 'KEROU', 'KOUANDE', 'MATERI', 'NATITINGOU', 'OUASSA-PEHUNCO', 'TANGUIETA', 'TOUKOUNTOUNA', 'ABOMEY-CALAVI', 'ALLADA', 'KPOMASSE', 'OUIDAH', 'SO-AVA', 'TOFFO', 'TORI-BOSSITO', 'ZE', 'BEMBEREKE', 'KALALE', 'N\'DALI', 'NIKKI', 'PARAKOU', 'PERERE', 'SINENDE', 'TCHAOUROU', 'BANTE', 'DASSA-ZOUME', 'GLAZOUE', 'OUESSE', 'SAVALOU', 'SAVE', 'APLAHOUE', 'DJAKOTOMEY', 'DOGBO', 'KLOUEKANMEY', 'LALO', 'TOVIKLIN', 'BASSILA', 'COPARGO', 'DJOUGOU', 'OUAKE', 'COTONOU', 'ATHIEME', 'BOPA', 'COME', 'GRAND-POPO', 'HOUEYOGBE', 'LOKOSSA', 'ADJARRA', 'ADJOHOUN', 'AGUEGUES', 'AKPRO-MISSERETE', 'AVRANKOU', 'BONOU', 'DANGBO', 'PORTO-NOVO', 'SEME-PODJI', 'ADJA-OUERE', 'IFANGNI', 'KETOU', 'POBE', 'SAKETE', 'ABOMEY', 'AGBANGNIZOUN', 'BOHICON', 'COVE', 'DJIDJA', 'OUINHI', 'ZAGNANADO', 'ZA-KPOTA', 'ZOGBODOMEY'],
      District:[]
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
    validateStep() {
      this.error = {};
      if (this.currentStep === 1) {
        if (!this.form.nom) {
          this.error.nom = "Le nom est requis.";
        } else if (this.form.nom.length < 3) {
          this.error.nom = "Le nom doit contenir au moins 3 caractères.";
        }

        if (!this.form.prenom) {
          this.error.prenom = "Le prénom est requis.";
        } else if (this.form.prenom.length < 3) {
          this.error.prenom = "Le prénom doit contenir au moins 3 caractères.";
        }

        if (!this.form.date) {
          this.error.date = "La date de naissance est requise.";
        } else if (this.calculateAge(this.form.date) < 18) {
          this.error.date = "Vous devez avoir au moins 18 ans pour vous inscrire.";
        }
      } else if (this.currentStep === 2) {
        if (!this.form.numero || this.form.numero.length !== 10) {
          this.error.numero = "Numéro de téléphone invalide.";
        }
        if (!this.form.town) {
          this.error.town = "La ville est requise.";
        }
        if (!this.form.district) {
          this.error.district = "Le quartier est requis.";
        }
      }
      return Object.keys(this.error).length === 0;
    },
    nextStep() {
      if (this.validateStep()) {
        this.currentStep++;
      }
    },
    prevStep() {
      if (this.currentStep > 1) this.currentStep--;
    },
    submitForm() {
      console.log("Formulaire soumis :", this.form);
      console.log("otp:", this.otp);
    },
    focusNext(index) {
      if (this.otp[index].length === 1 && index < 3) {
        this.$refs[`otpInput${index + 1}`][0].focus();
      }
    },
    filterTowns() {
      this.suggestions = this.form.town ? 
        this.Towns.filter(town => town.toLowerCase().includes(this.form.town.toLowerCase())) : [];
    },
    selectTown(town) {
      this.form.town = town;
      this.suggestions = [];
    },
    filterDistricts() {
      this.suggestions = this.form.district ? 
        this.Districts.filter(district => district.toLowerCase().includes(this.form.district.toLowerCase())) : [];
    },
    selectDistrict(district) {
      this.form.district = district;
      this.suggestions = [];
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

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
  border: 1px solid #ccc;
  border-radius: 4px;
  max-height: 150px;
  overflow-y: auto;
}
li {
  padding: 8px;
  cursor: pointer;
}
li:hover {
  background-color: #f0f0f0;
}

</style>
  