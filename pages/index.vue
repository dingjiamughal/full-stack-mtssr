<template lang="pug">
  .page-index
      el-row
          el-col(:span="5")
                Emenu
          el-col(:span="19")
                Life
      el-row
          el-col(:span="24")
              Artistic
</template>

<script>
import Emenu from '~/components/index/menu.vue';
import Life from '~/components/index/life.vue';
import Artistic from '~/components/index/artistic.vue';
export default {
    components: {
        Emenu,
        Life,
        Artistic
    }
};
</script>

<style lang="scss">
  @import "~/assets/css/index/index.scss";
</style>
