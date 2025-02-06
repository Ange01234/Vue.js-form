<template>
  <div>
    <h1>Bienvenue sur mon app Vue</h1>
    <FormComponent />
  </div>
</template>

<script>
  import FormComponent from "./components/FormComponent.vue";

  export default {
    components: {
      FormComponent,
    },
  };
</script>

<style>
  h1 {
    text-align: center;
    color: #333;
  }
</style>
