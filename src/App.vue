<template>
  <div>
    <Form />
  </div>
</template>

<script>
import Form from '../src/components/Form.vue'
export default {
  name: 'App',
  components: {
    Form
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@200;400&display=swap');

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
button, p, h3, h4{
  margin: 0;
  font-family: "Poppins";
}
</style>
