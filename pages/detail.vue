<template lang="pug">
.page-detail
    el-row
        el-col(:span="24")
            crumbs(:keyword="keyword", :type="type")
    el-row
        el-col(:span="24")
            summa(:meta="product")
    el-row(m-title)
        el-col(:span="24")
           h3 商家团购及优惠
    el-row(v-if="canOrder || !login")
        el-col(:span="24")
            list(v-if="login", :list="list")
            .deal-need-login(v-else)
                img(src="//p0.meituan.net/codeman/56a7d5abcb5ce3d90fc91195e5b5856911194.png", alt="登录查看")
                span 请登录后查看详细团购优惠
                el-button(type="primary", round)
                    a(href="/login") 立即登录
</template>

<script>
import Crumbs from '~/components/detail/crumbs.vue';
import Summa from '~/components/detail/summary.vue';
import List from '~/components/detail/list.vue';

export default {
    components: {
        Crumbs,
        Summa,
        List
    },
    computed: {
        // 根据数据是否有效来计算是否能购买
        canOrder() {
            return this.list.filter(item => item.photos.length).length;
        }
    },
    async asyncData(ctx) {
        // 客户端拿不到请求参数，只能在服务器端拿到参数keyword和type
        let {keyword, type} = ctx.query;
        let {
            status,
            data: {
                product,
                more: list,
                login
            }
        } = await ctx.$axios.get('/search/products', {
            params: {
                keyword,
                type,
                city: ctx.store.state.geo.position.city
            }
        });
        if (status === 200) {
            return {
                keyword,
                product,
                type,
                list,
                login
            };
        }
        else {
            return {
                keyword,
                product: {},
                type,
                list: [],
                login: false
            };
        }
    }
};
</script>

<style lang="scss">
  @import "~/assets/css/detail/index.scss";
</style>
