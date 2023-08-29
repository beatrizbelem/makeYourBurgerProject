<template>
  <banner></banner>
  <div class="main-container"></div>
  <h1>Monte seu Burger:</h1>
  <BurgerForm></BurgerForm>
</template>

<script>
import Banner from '@/components/Banner.vue';
import BurgerForm from '@/components/BurgerForm.vue';

export default {
  components: {
    Banner,
    BurgerForm
  }
}

</script>
