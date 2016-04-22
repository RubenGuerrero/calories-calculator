<template>
  <div class="container">
    <hello></hello>
  </div>
</template>

<script>
import Hello from './components/Hello'

export default {
  components: {
    Hello
  }
}
</script>

<style lang="scss">

  @import '~normalize.css';

  .container{
    padding: 0 5%;
    width: 100%;
    max-width: 480px;
    margin: 0 auto;
  }

  *{
    box-sizing: border-box;
  }

  body {
    font-family: 'Titillium Web', sans-serif;
    background: #f7f7f7;
  }

</style>
