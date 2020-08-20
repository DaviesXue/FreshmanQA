<template>
  <div class="bg">
    <div class="modal">
      <iframe src="./sponsors/sponsors.html"></iframe>
      <div style="padding:0px 30px; color: black;font-size: 12px; opacity: 0.5;">感谢以上赞助商对UCLCSSA学联的支持，使得本次临行会成为现实！</div>
      <div class="back" @click="$emit('hide')">返回</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Sponsor",
};
</script>

<style scoped>
.modal {
  background: white;
  position: fixed;
  top: 30px;
  bottom: 30px;
  left: 20px;
  right: 20px;
  border-radius: 10px;
}
.bg {
  background: rgba(0, 0, 0, 0.274);
  position: fixed;
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  z-index: 9999;
}

iframe {
  border: none;
  height: 85%;
}
.back {
  background: white;
  color: rgb(255, 77, 77);
  border-radius: 10px;
  line-height: 40px;
  margin: 10px 20px;
  border: 1px solid rgb(255, 77, 77);
  box-shadow: 0px 5px 20px -2px rgba(255, 0, 0, 0.2);
}
</style>