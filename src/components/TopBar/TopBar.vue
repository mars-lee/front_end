<template>
  <header class="navbar navbar-dark navbar-expand-sm fixed-top">
    <a class="navbar-brand" href="#">
      <img
        src="../../assets/images/bootstrap-solid.svg"
        width="30"
        height="30"
        class="d-inline-block align-top"
        alt
      />
      YOJ
    </a>
    <button
      class="navbar-toggler"
      type="button"
      data-toggle="collapse"
      data-target="#navbarSupportedContent"
      aria-controls="navbarSupportedContent"
      aria-expanded="false"
      aria-label="Toggle navigation"
    >
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <!-- =============== 跳转标题 ==================== -->
      <ul class="navbar-nav mr-auto">
        <li class="nav-item" v-for="(item,index) in routes" :key="index">
          <a :class="{'nav-link': true,'active': isCurrent(item.path)}" @click="goto(item.path)">
            {{item.name}}
            <!-- <span class="sr-only">(current)</span> -->
          </a>
        </li>
        <li class="nav-item">
          <a
            class="nav-link p-2"
            href="https://github.com/Li-MingZhong/yoj"
            target="_blank"
            rel="noopener"
            aria-label="GitHub"
          >
            <svg
              class="navbar-nav-svg"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 512 499.36"
              focusable="false"
            >
              <title>GitHub</title>
              <path
                d="M256 0C114.64 0 0 114.61 0 256c0 113.09 73.34 209 175.08 242.9 12.8 2.35 17.47-5.56 17.47-12.34 0-6.08-.22-22.18-.35-43.54-71.2 15.49-86.2-34.34-86.2-34.34-11.64-29.57-28.42-37.45-28.42-37.45-23.27-15.84 1.73-15.55 1.73-15.55 25.69 1.81 39.21 26.38 39.21 26.38 22.84 39.12 59.92 27.82 74.5 21.27 2.33-16.54 8.94-27.82 16.25-34.22-56.84-6.43-116.6-28.43-116.6-126.49 0-27.95 10-50.8 26.35-68.69-2.63-6.48-11.42-32.5 2.51-67.75 0 0 21.49-6.88 70.4 26.24a242.65 242.65 0 0 1 128.18 0c48.87-33.13 70.33-26.24 70.33-26.24 14 35.25 5.18 61.27 2.55 67.75 16.41 17.9 26.31 40.75 26.31 68.69 0 98.35-59.85 120-116.88 126.32 9.19 7.9 17.38 23.53 17.38 47.41 0 34.22-.31 61.83-.31 70.23 0 6.85 4.61 14.81 17.6 12.31C438.72 464.97 512 369.08 512 256.02 512 114.62 397.37 0 256 0z"
                fill="currentColor"
                fill-rule="evenodd"
              />
            </svg>
          </a>
        </li>
        <!--<li class="nav-item dropdown">-->
        <!--<a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">-->
        <!--Dropdown-->
        <!--</a>-->
        <!--<div class="dropdown-menu" aria-labelledby="navbarDropdown">-->
        <!--<a class="dropdown-item" href="#">Action</a>-->
        <!--<a class="dropdown-item" href="#">Another action</a>-->
        <!--<div class="dropdown-divider"></div>-->
        <!--<a class="dropdown-item" href="#">Something else here</a>-->
        <!--</div>-->
        <!--</li>-->
        <!--<li class="nav-item">-->
        <!--<a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">Disabled</a>-->
        <!--</li>-->
      </ul>
      <!--<form class="form-inline my-2 my-lg-0">-->
      <!--<input class="form-control mr-sm-2" type="search" placeholder="Search" aria-label="Search">-->
      <!--<button class="btn btn-outline-success my-2 my-sm-0" type="submit">Search</button>-->

      <!--</form>-->
      <!--<ul class="navbar-nav flex-row ml-sm-auto d-none d-sm-flex">-->
      <!--<li class="nav-item">-->
      <!--<a class="nav-link p-2" href="https://github.com/Li-MingZhong/yoj" target="_blank" rel="noopener"-->
      <!--aria-label="GitHub">-->
      <!--<svg class="navbar-nav-svg" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 499.36"-->
      <!--focusable="false">-->
      <!--<title>GitHub</title>-->
      <!--<path d="M256 0C114.64 0 0 114.61 0 256c0 113.09 73.34 209 175.08 242.9 12.8 2.35 17.47-5.56 17.47-12.34 0-6.08-.22-22.18-.35-43.54-71.2 15.49-86.2-34.34-86.2-34.34-11.64-29.57-28.42-37.45-28.42-37.45-23.27-15.84 1.73-15.55 1.73-15.55 25.69 1.81 39.21 26.38 39.21 26.38 22.84 39.12 59.92 27.82 74.5 21.27 2.33-16.54 8.94-27.82 16.25-34.22-56.84-6.43-116.6-28.43-116.6-126.49 0-27.95 10-50.8 26.35-68.69-2.63-6.48-11.42-32.5 2.51-67.75 0 0 21.49-6.88 70.4 26.24a242.65 242.65 0 0 1 128.18 0c48.87-33.13 70.33-26.24 70.33-26.24 14 35.25 5.18 61.27 2.55 67.75 16.41 17.9 26.31 40.75 26.31 68.69 0 98.35-59.85 120-116.88 126.32 9.19 7.9 17.38 23.53 17.38 47.41 0 34.22-.31 61.83-.31 70.23 0 6.85 4.61 14.81 17.6 12.31C438.72 464.97 512 369.08 512 256.02 512 114.62 397.37 0 256 0z"-->
      <!--fill="currentColor" fill-rule="evenodd"></path>-->
      <!--</svg>-->
      <!--</a>-->
      <!--</li>-->
      <!--</ul>-->
      <!-- ===============个人按钮  ==================== -->
      <ul class="navbar-nav flex-row ml-sm-auto d-none d-sm-flex" v-if="user">
        <li class="nav-item dropdown">
          <a
            class="nav-item nav-link dropdown-toggle mr-sm-2"
            href="#"
            id="bd-versions"
            data-toggle="dropdown"
            aria-haspopup="true"
            aria-expanded="false"
            sec:authentication="name"
          >
            <span class="fa fa-user-circle-o fa-lg text-primary" aria-label="GitHub"></span>
            {{user.userName}}
          </a>
          <div class="dropdown-menu dropdown-menu-right" aria-labelledby="bd-versions">
            <router-link tag="a" :to="'/user/info/'+user.userId" class="dropdown-item" href="#">个人信息</router-link>
            <div class="dropdown-divider"></div>
            <form action="http://localhost:8080/logout" method="post">
              <button class="dropdown-item" type="submit">退出</button>
            </form>
            <!--<a class="dropdown-item" th:href="@{/logout}">退出</a>-->
            <!--<a class="dropdown-item active" href="/docs/4.0/">v4.0.0</a>-->
            <!--<div class="dropdown-divider"></div>-->
            <!--<a class="dropdown-item" href="https://v4-alpha.getbootstrap.com/">v4 Alpha 6</a>-->
            <!--<a class="dropdown-item" href="https://getbootstrap.com/docs/3.3/">v3.3.7</a>-->
            <!--<a class="dropdown-item" href="https://getbootstrap.com/2.3.2/">v2.3.2</a>-->
          </div>
        </li>
      </ul>
    </div>
  </header>
</template>

<script>
import { mapState } from "vuex";
import request from "../../api/ajax";
export default {
  data() {
    return {
      routes: [
        { path: "/home", name: "首页" },
        { path: "/problem", name: "题库" },
        // { path: "/contest", name: "比赛" },
        { path: "/user", name: "用户" },
        { path: "/solution", name: "评测状态" }
      ]
    };
  },
  computed: mapState({
    user(state) {
      // console.log(state.user)
      return state.user;
    }
  }),
  methods: {
    goto(path) {
      this.$router.push(path);
    },
    isCurrent(path) {
      var regExp = new RegExp(path);
      // console.log(regExp.test(this.$route.path))
      return regExp.test(this.$route.path);
    },
    logout() {
      request({
        url: "/user/logout"
      })
        .then(res => {
          // console.log(res);
          res.data = "";
          this.goto("/login");
        })
        .catch(err => {
          console.log(err);
        });
    }
  }
};
</script>
<style>
/* @charset "UTF-8"; */
/* ====== top bar =========*/
/*图标*/
.fixed-top .navbar-nav-svg {
  display: inline-block;
  width: 1.5rem;
  height: 1.5rem;
  vertical-align: text-top;
}

.navbar.fixed-top {
  min-height: 4rem;
  background-color: #24292e;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.05),
    inset 0 -1px 0 rgba(0, 0, 0, 0.1);
}

.fixed-top .navbar-nav .nav-link.active {
  font-weight: 600;
}
/*登录 注册btn*/
.btn-bd-download {
  font-weight: 600;
  color: #fff;
  border-color: #fff;
}
/* ====== top bar =========*/
</style>