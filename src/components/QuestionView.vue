<template>
  <div>
    <div class="mBlock publish">
      <img
              class="post-avatar"
              src="https://vignette.wikia.nocookie.net/itb/images/d/d2/B_avatar.png/revision/latest?cb=20180311210200"
              mode="cover"
      />
      <div class="publish-textarea" @click="goToCommunity()">
        <div class="publish-text">分享活动，问答，交易，新鲜事...</div>
      </div>
      <img
              style="width:25px;height:25px;margin-right:10px;top:5px;opacity:0.5;margin:0"
              src="https://img.icons8.com/small/48/000000/filled-sent.png"
              @click="goToPublish()"
      />
    </div>
    <div class="mBlock" v-for="post in posts" :key="post.likes" @click="goToPost(post.post_id)">
      <div
              v-if="post.title"
              class="title"
              style="font-weight: bold; font-size: larger; text-align: left; padding-bottom: 3%"
      >{{post.title}}</div>
      <div
              v-if="post.content"
              class="content"
              style="text-align: left; font-size: smaller"
      >{{post.content.slice(0, 30)}}...</div>
      <div class="ask-wraper">
        <div
                class="askDetails"
                style="font-size: small; flex-grow:1;text-align:left;"
        >{{post.user}} 发布于 {{ moment(moment.unix(post.created_at).format("YYYY-MM-DD HH:mm:ss")).locale('zh-cn').fromNow() }}</div>
        <button type="button" class="btn" @click="voteQuestion(post.post_id)">我也想问 ({{post.likes}})</button>
        <br />
      </div>
    </div>
  </div>
</template>

<script>

  import wx from "weixin-js-sdk";

  const moment = require("moment");
  const axios = require("axios");
  // $(window).click(function() {
  //   console.log("CLICKED")
  // });
  export default {
    name: "QuestionOverview",
    props: {},
    data: function () {
      return {
        userid: null,
        posts: null,
        moment: moment,
        overlay: false,
      };
    },
    async mounted() {
      // this.userid = -1;
      getQueryParams("user");
      this.getPosts();
    },
    methods: {
      getPosts() {
        fetch("https://uclcssa.cn/post/getPostEndpoint.php?auth=ucl&space=14").then(async (res) => {
          let data = await res.json();
          this.posts = data.posts;
        });
      },
      voteQuestion(post_id) {
        console.log(post_id, this.userid);
        axios
                .post("https://uclcssa.cn/post/postLikeEndpoint.php", {
                  'postid': post_id,
                  'userid': this.userid,
                  'auth': "uclcssa2020"
                })
                .then((response) => {
                  console.log(response.data);
                  this.getPosts();
                })
                .catch((error) => console.log(error));
      },
      goToCommunity(){
        wx.miniProgram.switchTab({
          url:'/pages/discover/discover'
        });
        window.ga('send', 'event', "mini-app", "go-to-community", "");
      },
      goToPublish(){
        wx.miniProgram.navigateTo({
          url:'/pages/publish/publish?space_id=14'
        });
        window.ga('send', 'event', "mini-app", "go-to-publish", "");
      },
      goToPost(id){
        wx.miniProgram.navigateTo({
          url:'/pages/moments/detail?id='+id
        });
        window.ga('send', 'event', "mini-app", "go-to-post", id);
      },
      dummy() {
        console.log("+1")
      }
    },


  };

  // ref https://stackoverflow.com/questions/979975/how-to-get-the-value-from-the-get-parameters
  function getQueryParams(name) {
    const url = location.href;
    name = name.replace(/[[]/, "\\[").replace(/[\]]/, "\\]");
    const regexS = "[\\?&]" + name + "=([^&#]*)";
    const regex = new RegExp(regexS);
    const results = regex.exec(url);
    return results == null ? null : results[1];
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  .mBlock {
    background: white;
    box-shadow: 0px 5px 10px -2px rgba(0, 0, 0, 0.1);
    padding: 15px 30px;
    color: #252525;
    margin: 20px 0;
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
  .ask-wraper {
    display: flex;
    align-items: center;
    border-top: 1px solid #d3d3d3;
    padding-top: 10px;
    margin-top: 10px;
  }
  .btn {
    width: 140px;
    height: 30px;
    background: white;
    color: #ff4b4b;
    border: 1px solid #ff4b4b;
    box-shadow: 0px 5px 10px -2px rgba(255, 0, 0, 0.2);
    border-radius: 5px;
  }

  .post-avatar {
    width: 34px;
    height: 34px;
    margin: 0px;
    border-radius: 50%;
  }
  .publish-textarea {
    border: 1px solid #bebebe;
    border-radius: 30px;
    font-size: 30px;
    text-align: left;
    color: #8f8f8f;
    line-height: 25px;
    align-items: center;
    flex-grow: 1;
    margin: 5px 20px;
  }

  .publish-text {
    display: inline-block;
    margin-left: 14px;
    font-size: 12px;
  }

  .publish {
    display: flex;
    /*flex-direction: horizontal;*/
    justify-content: space-around;
    align-items: center;
    padding: 10px 30px;
  }
</style>
