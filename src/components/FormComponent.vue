<template>
    <div class="form-container">
      <h2>Formulaire d'inscription</h2>
      <form @submit.prevent="submitForm">
        <div>
          <label for="name">Nom :</label>
          <input type="text" id="name" v-model="formData.name" required />
        </div>
        <div>
          <label for="email">Email :</label>
          <input type="email" id="email" v-model="formData.email" required />
        </div>
        <div>
          <label for="age">Âge :</label>
          <input type="number" id="age" v-model="formData.age" required />
        </div>
        <button type="submit">Soumettre</button>
      </form>
    </div>
</template>
  
<script>
export default {
data() {
    return {
    formData: {
        name: "",
        email: "",
        age: "",
    },
    };
},
methods: {
    submitForm() {
    console.log("Données du formulaire :", this.formData);
    },
},
};
</script>
  
<style scoped>
.form-container {
    max-width: 400px;
    margin: auto;
    padding: 20px;
    border: 1px solid #ddd;
    border-radius: 8px;
    box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
}

form div {
    margin-bottom: 10px;
}

label {
    display: block;
    font-weight: bold;
}

input {
    width: 100%;
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
}

button {
    background-color: #42b983;
    color: white;
    border: none;
    padding: 10px;
    cursor: pointer;
    border-radius: 4px;
}

button:hover {
    background-color: #369e77;
}
</style>
  