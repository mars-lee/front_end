<template>
  <div class="container reset-password">
    <div class="py-2 text-center">
      <img
        class="d-block mx-auto mb-3"
        src="../../../assets/images/astronaut.jpg"
        alt
        width="72"
        height="72"
      />
      <h2>找回密码</h2>
      <div>
        <router-link href="#" to="/login" class="justify-content-center">已有账号，快去登录</router-link>
          &nbsp;
        <router-link href="#" to="/login" class="justify-content-center">没有账号，快去注册</router-link>
      </div>
      <p>请输入你的账号邮箱地址，以便找回密码</p>
    </div>
    <div class="row">
      <div class="col-md-8 order-md-1 offset-md-2">
        <form class="needs-validation" novalidate>
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <label class="input-group-text" for="email">Email:</label>
            </div>
            <input
              type="email"
              :class="{'form-control': true,'is-invalid': !emailJudge,'is-valid': emailJudge}"
              id="email"
              v-model="email"
              placeholder="you@example.com"
            />
            <div class="invalid-feedback">{{emailMsg}}</div>
          </div>

          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <label class="input-group-text" for="emailCheckCode">邮箱验证码:</label>
            </div>
            <input
              type="number"
              :class="{'col-9': true,'form-control': true,'is-invalid': !emailCheckJudge,'is-valid': emailCheckJudge}"
              id="emailCheckCode"
              v-model="emailCheckCode"
            />
            <div class="col-3 md-1">
              <button
                ref="emailBtn"
                @click="sendEmail"
                class="btn btn-outline-info btn-md btn-block"
                type="button"
              >获取验证码</button>
            </div>
            <div class="invalid-feedback">验证码错误.</div>
          </div>
          <JudgePassword v-model="passwordJudge"></JudgePassword>
          <div class="input-group" style="text-align: center;">
            <p style="text-align: center;">邮箱验证码仅在10分钟内有效</p>
          </div>
          <hr class="mb-2 mt-0" />
          <div style="display: flex; justify-content: center;">
            <button
              @click.prevent="resetPassword"
              class="btn btn-primary btn-lg"
              type="bottom"
            >&emsp;重置密码&emsp;</button>
          </div>
        </form>
      </div>
    </div>
    <!--加载图片model-->
    <div class="container">
      <div
        id="myModal"
        class="modal"
        data-keyboard="false"
        data-backdrop="static"
        data-role="dialog"
        aria-labelledby="myModalLabel"
        aria-hidden="true"
      >
        <div id="loading" class="loading">发送邮件中。。。</div>
      </div>
    </div>
  </div>
</template>

<script>
import JudgePassword from "../../../components/user/JudgePassword/JudgePassword.vue";
import request from "../../../api/ajax.js";
import $ from "jquery";
export default {
  components: {
    JudgePassword
  },
  data() {
    return {
      passwordJudge: {
        password: "",
        strength: 0
      },

      email: "",
      emailJudge: true,
      emailMsg: "",

      emailCheckCode: "",
      emailCheckJudge: true,
      emailCheckMsg: "",
      maxTime: 60
    };
  },
  methods: {
    countDown(maxTime, emailBtn) {
      var countDownVar = window.setInterval(() => {
        if (maxTime == 0) {
          emailBtn.disabled = "";
          emailBtn.innerHTML = "获取验证码";
          clearInterval(countDownVar);
        } else {
          emailBtn.disabled = "disabled";
          emailBtn.innerHTML = maxTime + "秒后重新获取";
          maxTime--;
        }
      }, 1000);
    },
    sendEmail() {
      if (!this.emailJudge) {
        return;
      }
      $("#myModal").modal("show");
      request({
        url: "/user/reset/emailCode/" + this.email
      })
        .then(res => {
          $("#myModal").modal("hide");
          // console.log(res)
          this.emailJudge = res.data.success;
          if (res.data.success) {
            this.$options.methods.countDown(5, this.$refs.emailBtn);
          } else {
            this.emailMsg = res.data.msg;
          }
        })
        .catch(err => {
          console.log(err);
          $("#myModal").modal("hide");
        });
    },
    resetPassword() {
      if (!this.emailJudge || this.passwordJudge.strength < 2) {
        return;
      }
      // console.log(this.headers);
      var password = this.passwordJudge.password;
      // console.log(password)
      request({
        url: "/user/reset/password",
        method: "POST",
        data: {
          password: password,
          email: this.email,
          emailCheckCode: this.emailCheckCode
          //  headers: this.headers
        }
      })
        .then(res => {
          // console.log(res);
          if (res.data.success) {
            this.$router.replace("/login");
          } else {
            this.emailCheckJudge = false;
            this.emailCheckMsg = res.data.msg;
            // console.log('error')
          }
        })
        .catch(err => {
          console.log(err);
        });
    }
  },
  watch: {
    email: function(value) {
      var emailReg = /^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/i;
      this.emailJudge = emailReg.test(value);
      if (!this.emailJudge) {
        this.emailMsg = "请输入一个有效的电子邮件地址.";
        return;
      }
    }
  }
};
</script>
<style>
.reset-password {
  margin-top: -3rem;
}
</style>