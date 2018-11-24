<template lang="pug">
.page-changeCity
    el-row
        el-col(:span="24")
            iSelect
    el-row
        el-col(:span="24")
            Hot
    el-row
        el-col(:span="24")
            Categroy
</template>

<script>
import iSelect from '~/components/changeCity/iselect';
import Hot from '~/components/changeCity/hot';
import Categroy from '~/components/changeCity/categroy';

export default {
    components: {
        iSelect,
        Hot,
        Categroy
    }
};
</script>

<style>

</style>
