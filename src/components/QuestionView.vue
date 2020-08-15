<template>
  <div style="margin-bottom:150px">
    <div class="mBlock publish" @click="goToPublish()">
      <img
        class="post-avatar"
        src="http://studentsunionucl.org/sites/uclu.org/files/csc-directory-images/cssa_short.jpg"
        mode="cover"
        :class="{disabled:!userid || userid=='null'}"
      />
      <div class="publish-textarea">
        <div class="publish-text">分享你有关UCL的任何问题...</div>
      </div>
      <img
        style="width:25px;height:25px;margin-right:10px;top:5px;opacity:0.5;margin:0"
        src="https://img.icons8.com/small/48/000000/filled-sent.png"
      />
    </div>
    <div class="publish" style="padding:0">
      <div class="buttons" v-if="!isLive" @click="getPosts">刷新</div>
      <div class="buttons live" v-else>🔴 实时 Live</div>
      <div style="flex-grow:1"></div>
      <div class="buttons" @click="sort('created_at')">按时间排序</div>
      <div class="buttons" @click="sort('likes')">按热度排序</div>
    </div>
    <div class="mBlock" v-if="!posts" style="text-align:center;">载入中，请稍等...</div>
    <div class="mBlock" v-for="post in posts" :key="post.id">
      <div @click="goToPost(post.post_id)">
        <div
          v-if="post.title"
          class="title"
          style="font-weight: bold; font-size: larger; text-align: left; padding-bottom: 3%"
        >{{post.title}}</div>
        <div
          v-if="post.content"
          class="content"
          style="text-align: left; "
        >{{post.content.slice(0, 60)}}{{post.content.length >= 60 ? "..." : ""}}</div>
      </div>
      <div class="ask-wraper">
        <div
          class="askDetails"
          style="font-size: small; flex-grow:1;text-align:left;"
        >{{post.user}} 发布于 {{ moment(moment.unix(post.created_at).format("YYYY-MM-DD HH:mm:ss")).locale('zh-cn').fromNow() }}</div>
        <button
          type="button"
          class="btn"
          @click="voteQuestion(post.post_id)"
          :class="{disabled:!userid || post.liked}"
        >我也想问 ({{post.likes}})</button>
        <br />
      </div>
    </div>

    <div class="fab" @click="goToPublish()">+</div>
  </div>
</template>

<script>
import wx from "weixin-js-sdk";

const moment = require("moment");
const axios = require("axios");

export default {
  name: "QuestionOverview",
  props: {},
  data: function () {
    return {
      userid: null,
      posts: null,
      moment: moment,
      overlay: false,
      isTabActive: true,
      sortBy: "created_at",
      isLive: false
    };
  },
  async mounted() {
    // this.userid = -1;
    this.userid = getQueryParams("user");
    this.requestLoop();

    setInterval(() => {
      this.requestLoop();
    }, 20000);

    window.onfocus = () => {
      this.isTabActive = true;
      this.requestLoop();
    };

    window.onblur = () => {
      this.isTabActive = false;
    };
  },
  methods: {
    async sort(sortBy){
      if(sortBy) this.sortBy = sortBy;
      this.posts.sort((a, b) => b[this.sortBy] - a[this.sortBy]);
    },
    getPosts() {
      console.log("fetching");
      const url =
        "https://uclcssa.cn/post/getPostEndpoint.php?auth=ucl&space=14&count=100&orderBy=created_at" +
        (this.userid ? "&userid=" + this.userid : "");
      fetch(url).then(async (res) => {
        let data = await res.json();
        console.log("fetched", data);
        this.posts = data.posts;
        this.sort()
        this.isLive = true;
        setTimeout(()=>{
          this.isLive = false
        }, 8000)
      });
    },
    voteQuestion(post_id) {
      console.log(post_id, this.userid);
      const params = new URLSearchParams();
      params.append("postid", post_id);
      params.append("userid", this.userid);
      params.append("auth", "uclcssa2020");
      const options = {
        method: "POST",
        data: params,
        url: "https://uclcssa.cn/post/postLikeEndpoint.php",
      };
      axios(options)
        .then((response) => {
          console.log(response.data);
          this.getPosts();
        })
        .catch((error) => console.log(error));
    },
    goToCommunity() {
      wx.miniProgram.switchTab({
        url: "/pages/discover/discover",
      });
      window.ga("send", "event", "mini-app", "go-to-community", "");
    },
    goToPublish() {
      wx.miniProgram.navigateTo({
        url: "/pages/publish/publish?space_id=14",
      });
      window.ga("send", "event", "mini-app", "go-to-publish", "");
    },
    goToPost(id) {
      console.warn(id);
      wx.miniProgram.navigateTo({
        url: "/pages/moments/detail?id=" + id,
      });
      window.ga("send", "event", "mini-app", "go-to-post", id);
    },
    dummy() {
      console.log("+1");
    },
    requestLoop() {
      if (this.isTabActive || !this.userid || this.userid=='null' || !this.posts) {
        if (!this.userid || this.userid=='null') {
          window.location.replace(window.location.origin + window.location.pathname + "?user=##ifanrid##&rand=" + Math.random());
        }
        this.getPosts();
      }
    },
  },
};

// ref https://stackoverflow.com/questions/979975/how-to-get-the-value-from-the-get-parameters
function getQueryParams(name) {
  const url = location.href;
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

.fab {
  position: fixed;
  bottom: 30px;
  width: 60px;
  height: 60px;
  background: rgb(255, 77, 77);
  color: white;
  border-radius: 50%;
  left: calc(50% - 30px);
  line-height: 60px;
  font-size: 30px;
  box-shadow: 0px 5px 20px -2px rgba(255, 0, 0, 0.2);
}

.disabled {
  pointer-events: none;
  opacity: 0.5;
}

.buttons{
  font-size: smaller;
  padding: 6px 10px;
  background: white;
  box-shadow: 0px 5px 10px -2px rgba(0, 0, 0, 0.1);
  margin: 0 10px;
  border-radius: 10px;
}

.live{
  font-weight: bolder;
  color:red;
  padding: 4px 10px;
  animation: blinker 2s linear infinite;
}

@keyframes blinker {
  50% {
    opacity: 0.2;
  }
}
</style>
