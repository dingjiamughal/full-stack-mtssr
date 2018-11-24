<template lang="pug">
.page-register
    .header
        header
            a.site-logo(href="/")
            span.login
                em.bold 已有美团账号？
                a(href="/login")
                    el-button(type="primary", size="small") 登录
    section
        el-form.demo-ruleForm(ref="ruleForm", :model="ruleForm", :rules="rules", label-width="100px")
            el-form-item(label="昵称", prop="name")
                el-input(v-model="ruleForm.name")
            el-form-item(label="邮箱", prop="email")
                el-input(v-model="ruleForm.email")
                el-button(size="mini", round, @click="sendMsg") 发送验证码
                span.status {{msgStatus}}
            el-form-item(label="验证码", prop="code")
                el-input(v-model="ruleForm.code", maxlength="4")
            el-form-item(label="密码", prop="pwd")
                el-input(v-model="ruleForm.pwd", type="password")
            el-form-item(label="确定密码", prop="cpwd")
                el-input(v-model="ruleForm.cpwd", type="password")
            el-form-item
                el-button(type="primary", @click="register") 同意以下协议并注册
                .error {{ error }}
            el-form-item
                a.f1(href="http://www.meituan.com/about/terms", target="_blank") 《美团网用户协议》
</template>

<script>
  import CryptoJS from 'crypto-js';

  export default {
      layout: 'blank',
      data() {
          return {
              msgStatus: '',
              error: '',
              ruleForm: {
                  name: '',
                  email: '',
                  code: '',
                  pwd: '',
                  cpwd: ''
              },
              rules: {
                  name: [{
                      required: true,
                      type: 'string',
                      message: '请输入昵称',
                      trigger: 'blur'
                  }],
                  email: [{
                      required: true,
                      type: 'email',
                      message: '请输入邮箱',
                      trigger: 'blur'
                  }],
                  pwd: [{
                      required: true,
                      message: '创建密码',
                      trigger: 'blur'
                  }],
                  cpwd: [{
                      required: true,
                      message: '确认密码',
                      trigger: 'blur'
                  }, {
                      validator: (rule, value, cb) => {
                          if (value === '') {
                              cb(new Error('请再次输入密码'));
                          }
                          else if (value !== this.ruleForm.pwd) {
                              cb(new Error('两次输入密码不一致'));
                          }
                          else {
                              cb();
                          }
                      },
                      trigger: 'blur'
                  }]

              }
          };
      },
      methods: {
          sendMsg() {
              let namePass;
              let emailPass;
              if (this.timerId) {
                  return false;
              }

              // 验证用户名
              this.$refs.ruleForm.validate('name', (valid) => {
                  namePass = valid;
              });
              this.msgStatus = '';
              if (namePass) {
                  return false;
              }

              // 验证邮箱
              this.$refs.ruleForm.validateField('email', (valid) => {
                  emailPass = valid;
              });
              if (!namePass && !emailPass) {
                  this.$axios.post('/users/verify', {
                      username: encodeURIComponent(this.ruleForm.name),
                      email: this.ruleForm.email
                  }).then(({status, data}) => {
                      if (status === 200 && data && data.code === 0) {
                          let count = 60;
                          this.timerId = setInterval(() => {
                              this.msgStatus = `验证码已发送，剩余${count--}`;
                              if (count === 0) {
                                  clearInterval(this.timerId);
                              }

                          }, 1000);
                      }
                      else {
                          this.msgStatus = data.msg;
                      }
                  });
              }

          },
          register() {
              this.$refs.ruleForm.validate((valid) => {
                  if (valid) {
                      this.$axios.post('/users/signup', {
                          username: encodeURIComponent(this.ruleForm.name),
                          // MD5加密，由于MD5方法返回的是一堆数组，需要通过toString转为字符串
                          password: CryptoJS.MD5(this.ruleForm.pwd).toString(),
                          email: this.ruleForm.email
                      }).then(({status, data}) => {
                          if (status === 200) {
                              if (data && data.code === 0) {
                                  location.href = '/login';
                              }
                              else {
                                  this.error = data.msg;
                              }
                          }
                          else {
                              this.error = `服务器出错，错误码：${status}`;
                          }
                          // 定时清空error信息
                          setTimeout(() => {
                              this.error = '';
                          }, 1500);
                      });
                  }

              });
          }
      }
  }
</script>

<style lang="scss">
  @import "~/assets/css/register/index.scss";
</style>
