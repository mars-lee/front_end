<template>
  <div class="container register">
    <div class="py-3 text-center">
      <img
        class="d-block mx-auto mb-3"
        src="../../assets/images/astronaut.jpg"
        alt
        width="72"
        height="72"
      />
      <h2>注册账号</h2>
      <router-link href="#" to="/login" class="d-flex justify-content-center">已有账号，快去登录</router-link>
    </div>
    <div class="row">
      <div class="col-md-8 order-md-1 offset-md-2">
        <form class="needs-validation" novalidate>
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <label class="input-group-text" for="userName">用户名:</label>
            </div>
            <input
              type="text"
              :class="{'form-control': true,'is-invalid': !userNameJudge,'is-valid': userNameJudge}"
              id="userName"
              v-model="userName"
              placeholder
              required="required"
            />
            <div class="invalid-feedback">{{userNameMsg}}</div>
          </div>

          <JudgePassword v-model="passwordJudge"></JudgePassword>

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
              placeholder
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
          <div class="input-group mb-1" style="text-align: center;">
            <p style="text-align: center;">邮箱验证码仅在10分钟内有效</p>
          </div>
          <hr class="mb-3 mt-0" />
          <div style="display: flex; justify-content: center;">
            <button
              @click.prevent="register"
              class="btn btn-primary btn-lg"
              type="bottom"
            >&emsp;注册&emsp;</button>
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
import request from "../../api/ajax.js";
import JudgePassword from "../../components/user/JudgePassword/JudgePassword.vue";
import $ from "jquery";
export default {
  data() {
    return {
      userName: "",
      userNameJudge: true,
      userNameMsg: "",

      passwordJudge: {
        password: '',
        strength: 0,
      },

      email: "",
      emailJudge: false,
      emailMsg: "",

      emailCheckCode: "",
      emailCheckJudge: true,
      emailCheckMsg: "",
      headers: {},
      maxTime: 60
    };
  },
  components: {
    JudgePassword
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
        url: "/user/r/getEmailCheckCode/" + this.email
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
    register() {
      if (
        !this.userNameJudge ||
        this.passwordJudge.strength < 2 ||
        !this.secondPasswordJudge ||
        !this.emailJudge
      ) {
        return;
      }
      console.log(this.passwordJudge);
      // console.log(this.headers);
      request({
        url: "/user/r/register",
        method: "POST",
        data: {
          userName: this.userName,
          password: this.passwordJudge.password,
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
            for (var obj in res.extend) {
              // console.log("form input[id=" + obj + "]");
              // var this.$input = this.$("form input[id=" + obj + "]");
              this[obj] = false;
            }
            // console.log('error')
          }
        })
        .catch(err => {
          console.log(err);
        });
    }
  },
  watch: {
    userName: function(value) {
      // console.log(value)
      if (value === "") {
        // validateWithMsg(this, false, "");
        this.userNameJudge = false;
        this.userNameMsg = "用户名不能为空";
        return;
      }
      request({
        url: "/user/r/validateUserName/" + value
      })
        .then(res => {
          console.log(res);
          res = res.data;
          if (res.success) {
            this.userNameJudge = true;
            this.userNameMsg = "";
          } else {
            this.userNameJudge = false;
            this.userNameMsg = "用户名已存在";
          }
        })
        .catch(err => {
          console.log(err);
        });
    },

    email: function(value) {
      var emailReg = /^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/i;
      this.emailJudge = emailReg.test(value);
      if (!this.emailJudge) {
        this.emailMsg = "请输入一个有效的电子邮件地址.";
        return;
      }
      request({
        url: "/user/r/validateEmail/" + value
      })
        .then(res => {
          // console.log(res);
          res = res.data;
          this.emailJudge = res.success;
          if (this.emailJudge) {
            this.emailMsg = "";
          } else {
            this.emailMsg = res.msg;
          }
        })
        .catch(err => {
          console.log(err);
        });
    }
  },
  mounted() {
    // var csrfHeader = this.$("meta[name='_csrf_header']").attr("content");
    // var csrfToken = this.$("meta[name='_csrf']").attr("content");
    // var headers = {};
    // headers[csrfHeader] = csrfToken;
    // this.headers = headers;
  }
};
</script>
<style>

.register.container {
  margin-top: -40px;
}
.input-group-text {
  background-color: #e9ecef40;
  width: 9rem;
  text-align: center;
  display: inline-block;
}

.invalid-feedback {
  text-align: center;
}

.loading {
  width: 200px;
  height: 56px;
  position: absolute;
  top: 50%;
  left: 50%;
  line-height: 56px;
  color: #fff;
  padding-left: 60px;
  font-size: 15px;
  background: #000 url("../../assets/images/loading.gif") no-repeat 10px 50%;
  opacity: 0.7;
  z-index: 9999;
  -moz-border-radius: 20px;
  -webkit-border-radius: 20px;
  border-radius: 20px;
  filter: progid:DXImageTransform.Microsoft.Alpha(opacity=70);
}
</style>