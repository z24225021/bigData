<template>
  <div class="details" @click.self="closePage">
    <fieldset class="border details-wrap">
      <!-- 右上角 -->
      <div class="hot-tr corner1" ref="hotTr"></div>
      <!-- 左下角 -->
      <div class="hot-lb corner2"></div>
       <!-- tab切换框 -->
        <div class="switch-button detailsButton">
            <div
                class="child-button"
                :class="{'active-father-button': activeButton == 1}"
                @click="switchButton(1)"
            >场馆详情</div>
            <div
                class="child-button"
                :class="{'active-father-button': activeButton == 2}"
                @click="switchButton(2)"
            >智能终端</div>
            <div
                class="child-button"
                :class="{'active-father-button': activeButton == 3}"
                @click="switchButton(3)"
            >实时监控</div>
        </div>
      <legend class="title">
        <div class="inner-title" style="cursor:pointer;" @click="closePage">返回</div>
      </legend>
      <div class="details-content" v-if="activeButton==1">
        <div class="left-content">
          <el-tooltip
            v-if="details.title&&details.title.length>8"
            class="item"
            effect="dark"
            :content="details.title"
            placement="top"
          >
            <div class="content-title" style="cursor:pointer;">
              {{details.title|cutString}}
              <span style="font-weight:bold;">...</span>
            </div>
          </el-tooltip>
          <div v-else class="content-title" style="cursor:pointer;">{{details.title}}</div>
          <div v-if="message.type=='activity'">
            <div class="content-time" style="margin-bottom:8px;">
              <img width="20" height="20" style="vertical-align:text-top;" src="../assets/time.png">
              &nbsp;{{details.time}}
            </div>
            <div class="content-time" style="margin-bottom:24px;">
              <img width="20" height="20" style="vertical-align:text-top;" src="../assets/icon.png">
              &nbsp;{{details.place}}
            </div>
          </div>
          <div v-else>
            <div class="content-time" style="margin-bottom:8px;">
              <img width="20" height="20" style="vertical-align:text-top;" src="../assets/icon.png">
              &nbsp;地址:&nbsp;{{details.place}}
            </div>
            <div class="content-time" style="margin-bottom:24px;">
              <img width="20" height="20" style="vertical-align:text-top;" src="../assets/time.png">
              &nbsp;开放时间:&nbsp;{{details.time}}
            </div>
          </div>
          <div class="jianbian" style="height:1px;width:100%;"></div>
          <div v-if="message.type=='activity'">
            <div class="content-info" style="margin-top:15px;">主办方:&nbsp;{{details.sponsor}}</div>
            <div class="content-info" style="margin:8px 0;">发布方:&nbsp;{{details.issuer}}</div>
            <div class="content-info" style="margin-bottom:15px;">联系电话:&nbsp;{{details.phone}}</div>
          </div>
          <div v-else>
            <div
              class="content-info"
              style="margin-top:15px;margin-bottom:8px;"
            >联系电话:&nbsp;{{details.phone}}</div>
            <div class="content-info" style="margin-bottom:15px;">官方链接:&nbsp;{{details.officalLink}}</div>
          </div>
          <div class="jianbian" style="height:1px;width:100%;"></div>
          <div v-if="message.type=='activity'">
            <div
              class="content-info"
              style="margin-top:24px;margin-bottom:8px;"
            >报名日期:&nbsp;{{details.enterTime}}</div>
            <div class="content-info" style="margin-bottom:40px;">报名人数:&nbsp;{{details.enterPeople}}</div>
            <div class="content-video">
              <video
                id="myPlayer"
                height="216"
                style="width:100%;"
                poster
                controls
                playsinline
                webkit-playsinline
                autoplay
              >
                <source
                  v-if="details.video_data && details.video_data.length"
                  :src="details.video_data[0].param.url"
                  type
                >
                <source ref="source" :src="src1" type="application/x-mpegURL">
              </video>
            </div>
          </div>
          <div v-else>
            <div class="content-time" style="margin:16px 0;">微信公众号</div>
            <img v-if="details.img" :src="details.img" height="120" width="120" alt>
            <div v-else class="content-time">暂无</div>
          </div>
        </div>
        <div v-if="message.type=='activity'" class="right-content" v-html="details.h5"></div>
        <div v-else class="right-content">
          <div
            style="font-size: 24px;color: #D6ECFF;margin-bottom: 24px;font-family: PingFangSC-Medium;"
          >场馆介绍</div>
          <img :src="details.img" alt style="margin-bottom:40px;">
          <div
            style="font-family: PingFangSC-Regular;font-size: 18px;color: #ADD9FF;line-height: 30px;"
          >{{details.introduce}}</div>
        </div>
      </div>
      <div v-else-if="activeButton==2" class="details-content">
          <div class="wrap-module">
              <div class="statisticsModule">
                <div v-for="(item,index) in statisticsData" :key="index" class="statistic-item">
                    <div class="statistic-item-key">{{item.key}}</div>
                    <div class="statistic-item-value">{{item.value}}</div>
                </div>
            </div>
          </div>
      </div>
      <div v-else class="details-content">实时监控</div>
    </fieldset>
  </div>
</template>
<script>
export default {
  name: "detailPage",
  props: ["message"],
  data() {
    return {
      details: "",
      src1:
        "http://hls.open.ys7.com/openlive/4692d42076d842c485d7fd6da42546ec.m3u8",
      src: "rtmp://rtmp.open.ys7.com/openlive/4692d42076d842c485d7fd6da42546ec",
      // src: 'rtmp://rtmp.open.ys7.com/openlive/f01018a141094b7fa138b9d0b856507b',
      player: "",
      activeButton: "",
      statisticsData: [
          {key: '今日流量',value: 37},
          {key: '昨日人流量',value: 287},
          {key: '今年人流量',value: 4320}
      ]
    };
  },
  created() {
    this.getDetailData();
    //   this.$axios
    // .post("https://open.ys7.com/api/lapp/live/address/get",this.$qs.stringify({
    //     accessToken: 'at.4v752uhj7oew8cpkaw4j655h617nmo5t-1hpv7s0z9j-0hqdr3w-cnbxsym69'
    //  }))
    // .then((res)=>{
    //     console.log(res);
    //   }).catch((res)=>{
    //       console.log(res)
    //   })
  },
  mounted() {
    this.$nextTick(function() {
      if (this.message.type == "activity") {
        this.player = new EZUIPlayer("myPlayer");
      }
      var brower = navigator.userAgent;
      let safari = false;
      if (!(brower.indexOf("Chrome") > -1) && brower.indexOf("Safari") > -1) {
        safari = true;
      }
      if (brower.indexOf("Firefox") > -1 || safari) {
        //判断是否为火狐浏览器或safari浏览器
        this.$refs.hotTr.style.top = -32 + "px";
      }
      let h = document.body.clientHeight;
      if (h < 720) {
        document.getElementById("myPlayer").style.height = 140 + "px";
      }
    });
  },
  methods: {
    closePage: function() {
      this.$emit("gotoParent", "closePage");
      var brower = navigator.userAgent;
      let safari = true;
      if (!(brower.indexOf("Chrome") > -1) && brower.indexOf("Safari") > -1) {
        safari = false;
      }
      if (this.message.type == "activity" && safari) {
        this.player.stop();
      }
    },
    getDetailData: function() {
      this.$axios
        .post(
          "/BigScreen/Index/mapDetail",
          this.$qs.stringify({
            id: this.message.id,
            type: this.message.type
          })
        )
        .then(res => {
          if (res.data.CODE == "ok") {
            this.details = res.data.DATA.details;
            console.log('details',details);
          } else {
            this.$message({
              message: res.data.MESSAGE,
              type: "error"
            });
          }
        })
        .catch(res => {
          console.log(res);
        });
    },
    switchButton: function(val){
       this.activeButton = val;
    }
  },
  filters: {
    cutString: function(val) {
      if (val && val.length > 8) {
        return val.substr(0, 8);
      }
    }
  }
};
</script>
<style>
@import "../assets/page.css";
.details {
  width: 100vw;
  height: 100vh;
  padding-top: 28px;
  box-sizing: border-box;
  background: rgba(0, 0, 0, 0.7);
}
.details-wrap {
  width: 62.5%;
  height: 92%;
  margin: auto;
  padding: 0 40px;
  text-align: left;
  position: relative;
}
.details-content {
  width: 100%;
  height: 85%;
  display: flex;
  justify-content: space-between;
  margin-top: 39px;
  overflow-y: auto;
}
.left-content {
  width: 30.1%;
  height: 100%;
}
.right-content {
  width: 64.2%;
  height: 100%;
  overflow-y: auto;
}
.content-title {
  font-family: PingFangSC-Medium;
  font-size: 28px;
  color: #d6ecff;
  margin-bottom: 32px;
}
.content-time {
  font-family: HelveticaNeue;
  font-size: 16px;
  color: #add9ff;
}
.content-info {
  font-family: PingFangSC-Regular;
  font-size: 14px;
  color: #add9ff;
}
.right-content {
  color: #d6ecff;
}
.right-content img {
  max-width: 100% !important;
}
.detailsButton{
    top: 50px;
    text-align: center;
}
.statisticsModule{
    display: flex;
    justify-content: space-between;
    width: 100%;
    padding-top: 108px;
}
.statistic-item{
    width: 19.4%;
    height: 233px;
    background-image: linear-gradient(-180deg, rgba(0,135,255,0.24) 0%, rgba(0,135,255,0.00) 100%);
    border: 1px solid #ADD9FF;
    box-shadow: 0 0 8px 0 rgba(0,135,255,0.50);
    border-radius: 2px;
    text-align: center;
}
.statistic-item-key{
    font-family: PingFangSC-Medium;
    font-size: 30px;
    color: #ADD9FF;
    margin-top: 68px;
    margin-bottom: 32px;
}
.statistic-item-value{
    font-family: Helvetica;
    font-size: 35px;
    color: #FFCC00;
    text-shadow: 0 0 4px rgba(255,238,0,0.50);
}
.wrap-module{
    width: 100%;
    padding: 0 5%;
}
</style>


