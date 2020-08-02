<template>
  <div>
    <div class="mBlock"  v-for="post in posts" :key="post.id">
      <div class="title" style="font-weight: bold; font-size: larger; text-align: left; padding-bottom: 3%">
        {{post.title}}</div>
      <div class="content" style="text-align: left; font-size: smaller">
        {{post.content.slice(0, 30)}}...
      </div>
      <hr class="solid">
      <div class="ask-wraper">
        <div class="askDetails" style="padding-top:2%; position: absolute; left: 5%; font-size: small"> {{post.user}} 发布于 {{ moment(post.updated_at).locale('zh-cn').fromNow() }} </div>
        <button type="button" class="btn btn-outline-danger" @click="voteQuestion(post.post_id, post.user_id)"  style="position: absolute; right: 5%; padding-left: 9%; padding-right: 9%"> 我也想问:({{post.likes}})</button>
      <br>
      </div>
    </div>
  </div>
<!--    <button>HERE</button>-->
</template>

<script>
  const moment = require('moment');
  const axios = require('axios');
  export default {
  name: 'QuestionOverview',
  props: {
  },
  data: function () {
   return {
     posts: null,
     moment: moment
   }
  },
  async mounted() {
    fetch("https://uclcssa.cn/post/getPostEndpoint.php?auth=ucl&space=14").then(async res => {
      let data = await res.json();
      this.posts = data.posts;
    })
  },
  methods: {
    voteQuestion: function(post_id, user_id) {
      console.log(post_id, user_id)
      axios.post("https://uclcssa.cn/post/postLikeEndPoint.php", {
        'postid': post_id,
        'userid': user_id
      }).then(response => {
        console.log(response)
      }).catch(error => console.log(error))
      // fetch("https://uclcssa.cn/post/postLikeEndpoint.php?")
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
