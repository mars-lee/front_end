<template>
  <div class="container" style="margin-top: 80px;">
    <h2 class="panel-title mb-4 text-center">个人信息</h2>
    <div class="row">
      <div class="col-2 order-md-1 m-auto">
        <table class="table table-bordered text-center">
          <thead class="thead-light">
            <tr>
              <th class="thodd">提交</th>
              <th class="thodd">通过</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>
                <a @click.prevent="toSolution(null)" href="#">{{countSubmission}}</a>
              </td>
              <td>
                <a @click="toSolution(0)" href="#">{{countAccepted}}</a>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="card shadow p-3 mb-5 bg-white rounded mx-5">
      <dl class="row text-left px-5">
        <dt class="col-3 font-weight-bold">用户名：</dt>
        <dd class="col-9 md-3">{{user.userName}}</dd>
        <dt class="col-3 font-weight-bold">Email:</dt>
        <dd class="col-9">{{user.email}}</dd>
      </dl>
    </div>
  </div>
</template>

<script>
// import { mapState } from "vuex";
import request from "../../../api/ajax.js";
export default {
  data() {
    return {
      countAccepted: 0,
      countSubmission: 0,
      user: {userName: ''},
    };
  },
  // computed: mapState(["user"]),
  methods: {
    toSolution(result) {
      this.$router.push({
        path: "/solution",
        query: { userName: this.user.userName, result: result }
      });
    }
  },
  created() {
     console.log('in')
    request({
      url: "/user/info/"+this.$route.params.id,
      method: "get"
    })
      .then(res => {
        console.log(res);
        // console.log(result.extend.pageInfo.list)
        this.user = res.data.extend.user;
        // this.countAccepted = res.data.extend.accepted;
        // this.countSubmission = res.data.extend.submission;
      })
      .catch(err => {
        console.log(err);
      });
    request({
      url: "/solution/countAcceptedAndSubmission",
      method: "get"
    })
      .then(res => {
        console.log(res);
        // console.log(result.extend.pageInfo.list)
        this.countAccepted = res.data.extend.accepted;
        this.countSubmission = res.data.extend.submission;
      })
      .catch(err => {
        console.log(err);
      });
  }
};
</script>
<style>
</style>