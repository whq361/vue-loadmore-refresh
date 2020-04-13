<template>
  <div class="vue-pull-refresh" ref="refresh">
    <div class="vue-pull-refresh-msg" :style="{height:height,fontSize:fontSize,borderColor}">
      <div v-if="loading">
      
        <img class="img"  src="~/assets/imgs/loading.gif" style="width: 0.2rem; height: 0.2rem;vertical-align: middle;fill: currentColor;overflow: hidden;marginRight:0.05rem"/>
        正在加载
      </div>
    </div>
    <slot></slot>
    <div v-if="!allLoaded && bottom" class="vue-pull-refresh-msg-down">
     <img class="img"  src="~/assets/imgs/loading.gif" style="width: 0.2rem; height: 0.2rem;vertical-align: middle;fill: currentColor;overflow: hidden;marginRight:0.05rem"/>
      正在加载
    </div>

    <div v-if="allLoaded" class="to-the-bottom">
      <div class="line"></div>我是有底线的
      <div class="line"></div>
    </div>
  </div>
</template>
<script type="text/javascript">
import utils from "~/utils/utils";

export default {
  name: "LoadMore",
  props: {
    allLoaded: {
      type: Boolean,
      value: false
    },
    bottomDistance: {
      type: Number
    },
    loadBottom: {
      type: Function
    },
    loadTop: {
      type: Function
    }
  },
  data() {
    return {
      fontSize: 0,
      height: 0,
      loading: false,
      flag: false,
      borderColor: "#fff",
      isRequest: false,
      bottom:false,
    };
  },

  components: {},

  destroyed() {
    document.removeEventListener("touchstart", this.touchStart, false);
    document.removeEventListener("touchmove", this.touchMove, false);
    document.removeEventListener("touchend", this.touchEnd, false);
    document.body.style.transform = "";
  },
  methods: {
    handleScroll: function() {
      if(utils.isWindowReachBottom(this.bottomDistance+50)){
          this.bottom=true;
      }
      if (this.isRequest) return;
      if (!this.allLoaded && utils.isWindowReachBottom(this.bottomDistance)) {
        this.isRequest = true;
        this.loadBottom();
      }
    },
    onBottomLoaded() {
      this.isRequest = false;
    },
    onTopLoaded() {
      this.loading = false;
      this.height = "0rem";
      this.fontSize = "0rem";
      this.borderColor = "transparent";
    },
    touchstart() {
      // event.preventDefault(); //阻止默认事件（长按的时候出现复制）
      this.startX = event.changedTouches[0].pageX;
      this.startY = event.changedTouches[0].pageY;
  
    },
    touchmove() {
      // event.preventDefault();
      var moveEndX = event.changedTouches[0].pageX;
      var moveEndY = event.changedTouches[0].pageY;
      var X = moveEndX - this.startX;
      var Y = moveEndY - this.startY;
      if (Math.abs(X) > Math.abs(Y) && X > 0) {
        // alert("left 2 right");
      } else if (Math.abs(X) > Math.abs(Y) && X < 0) {
        // alert("right 2 left");
      } else if (Math.abs(Y) > Math.abs(X) && Y > 0) {
        if (this.loadTop&&window.scrollY == 0 && !this.loading) {
          console.log("到顶了");
          this.loading = true;
          this.height = "0.5rem";
          this.fontSize = "0.1rem";
          this.borderColor = "#eee";
          this.loadTop();
          return;
        }
      } else if (Math.abs(Y) > Math.abs(X) && Y < 0) {
        // alert("bottom 2 top");
      } else {
        // alert("just touch");
      }
    },
    touchend() {
      // event.preventDefault();
      this.EndTime = new Date().getTime();
      this.isRecord = false;

      if (this.EndTime - this.StarTime < 500) {
        this.EndTime = 0;
        this.StarTime = 0;
      } 
    }
  },
  mounted() {
    window.addEventListener("scroll", this.handleScroll);
    window.addEventListener("touchstart", this.touchstart);
    window.addEventListener("touchmove", this.touchmove);
    window.addEventListener("touchend", this.touchend);
  }
};
</script>
<style type="text/css">
.vue-pull-refresh {
  /* height: 100%; */
  /* overflow-y: auto; */
  transition: 330ms;
  /* -webkit-overflow-scrolling: touch; */
}
.vue-pull-refresh-msg-down {
  width: 100%;
  height: 0;
    height: 0.5rem;
  font-size: 0.1rem;
  text-align: center;
  color: #666;
  display: flex;
  align-items: center;
  justify-content: center;
}

.vue-pull-refresh-msg {
  /* margin-top: -50px; */
  width: 100%;
   height: 0;
  text-align: center;
  color: #666;
  border-bottom: 1px solid #eee;
  display: flex;
  align-items: center;
  justify-content: center;
}
.icon-reverse {
  transform: rotate(180deg);
  transition: all 0.3s ease;
}
.vue-pull-refresh-loading {
  -webkit-animation: loadRotate 1s linear infinite;
  animation: loadRotate 1s linear infinite;
}
@-webkit-keyframes loadRotate {
  from {
    -webkit-transform: rotate(0deg);
  }
  to {
    -webkit-transform: rotate(360deg);
  }
}
@keyframes loadRotate {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.center {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0.1rem;
}
</style>