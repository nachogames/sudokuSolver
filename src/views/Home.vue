<template>
  <div class="home">
    <SudukoBoard />
  </div>
</template>

<script>
import SudukoBoard from '@/components/SudukoBoard.vue';

export default {
  name: 'Home',
  components: {
    SudukoBoard,
  },
};
</script>
