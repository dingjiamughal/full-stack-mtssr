<template lang="pug">
.page-cart
    el-row
        el-col.m-cart(
          v-if="cart.length"
          :span="24"
        )
            list(:cart-data="cart")
            p
                | 应付金额：
                em.money ￥{{total}}
            .post
                el-button(
                    type="primary",
                    @click="submit"
                ) 提交订单
        el-col.empty(v-else) 购物车为空
</template>

<script>
import List from '~/components/cart/list.vue';

export default {
    components: {
        List
    },
    data() {
        return {
            cart: []
        };
    },
    computed: {
        total() {
            let total = 0;
            this.cart.forEach(item => {
                total += item.price * item.count;
            });
            return total;
        }
    },
    methods: {
        submit: async () => {
            this.$message.success('下单成功！');
        }
    },
    async asyncData(ctx) {
        let {status, data: {code, data: {name, price}}} = await ctx.$axios.post('/cart/getCart', {
            id: ctx.query.id
        });
        if (status === 200 && code === 0 && name) {
            return {
                cart: [{
                    name,
                    price,
                    count: 1
                }],
                cartNo: ctx.query.id
            };
        }

    }
};
</script>

<style lang="scss">
  @import "~/assets/css/cart/index.scss";
</style>
