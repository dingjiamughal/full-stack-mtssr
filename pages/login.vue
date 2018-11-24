<template lang="pug">
.page-login
    .login-header
        a.logo(href="/")
      
    .login-panel
        .banner
            img(
              src="//s0.meituan.net/bs/file/?f=fe-sso-fs:build/page/static/banner/www.jpg",
              width="480",
              height="370",
              alt="美团网"
            )
        .form
            h4.tips(
                v-if="error"
            )
                i 
                | {{error}}
            p
                span 账号登录
            el-input(v-model="username", prefix-icon="profile")
            el-input(v-model="password", prefix-icon="password", type="password")
            .foot
                el-checkbox(v-model="checked") 7天内自动登录
                b 忘记密码？
            el-button.btn-login(
                type="success",
                size="mini",
                @click="login"
            ) 登录
</template>

<script>
import CryptoJS from 'crypto-js';

export default {
    layout: 'blank',
    data: () => {
        return {
            checked: '',
            username: '',
            password: '',
            error: ''
        };
    },
    methods: {
        login() {
            this.$axios.post('/users/signin', {
                username: encodeURIComponent(this.username),
                password: CryptoJS.MD5(this.password).toString()
            }).then(({status, data}) => {
                if (status === 200) {
                    if (data && data.code === 0) {
                        location.href = '/';
                    }
                    else {
                        this.error = data.msg;
                    }
                }
                else {
                    this.error = `服务器出错`;
                }
            });
        }
    }
};
</script>

<style lang="scss">
  @import "~/assets/css/login/index.scss";
</style>
