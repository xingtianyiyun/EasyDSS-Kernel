<template>
  <div class="wrapper">
    <NaviBar :logoText="logoText" :logoMiniText="logoMiniText" :versionText="versionText"></NaviBar>
    <Sider :menus="menus"></Sider>
    <VideoDlg ref="videoDlg"></VideoDlg>
    <div class="content-wrapper">
      <section class="content">
        <router-view @play="play"></router-view>
      </section>
    </div>
    <footer class="main-footer">
      <div class="pull-right hidden-xs hide">
        EasyDSS
      </div>
      <strong>Copyright &copy; {{ thisYear() }} <a href="http://www.easydss.com">{{serverInfo.Copyright}}</a>.</strong> All rights reserved.
    </footer>
  </div>
</template>

<script>
import "font-awesome/css/font-awesome.css"
import "bootstrap/dist/css/bootstrap.css"
import "admin-lte/dist/css/AdminLTE.css"
import "admin-lte/dist/css/skins/_all-skins.css"
import "assets/styles/custom.less"
import "element-ui/lib/theme-default/index.css"

import "bootstrap/dist/js/bootstrap.js"
import "admin-lte/dist/js/adminlte.js"


import { mapState, mapActions } from "vuex"
import Vue from 'vue'
import moment from 'moment'
import Sider from 'components/Sider.vue'
import NaviBar from 'components/NaviBar.vue'
import VideoDlg from 'components/VideoDlg.vue'

import { Message, Loading } from 'element-ui'
Vue.prototype.$message = Message;
Vue.use(Loading.directive);
Vue.prototype.$loading = Loading.service;


export default {
  data() {
    return {
    }
  },
  mounted() {
    $(document).ajaxError((evt, xhr, opts, ex) => {
      if (xhr.status == 401) {
        top.location.href = '/';
        return false;
      }
      let msg = xhr.responseText || "网络请求失败";
      if (xhr.status == 404) {
        msg = "请求服务不存在或已停止";
      }
      this.$message({
        type: 'error',
        message: msg
      })
    }).on("click", ".main-header .sidebar-toggle", function () {
      setTimeout(() => {
        localStorage["sidebar-collapse"] = $("body").hasClass("sidebar-collapse") ? "sidebar-collapse" : "";
      }, 500)
    });
    $("body").addClass(localStorage["sidebar-collapse"]);
    this.getServerInfo();
    this.fixHover();
    $(() => {
        // $.AdminLTE.layout.fix();
    })
  },
  components: {
    NaviBar, Sider, VideoDlg
  },
  computed: {
    ...mapState([
      "logoText",
      "logoMiniText",
      "menus",
      "serverInfo"
      
    ]),
    versionText(){
      let text = "";
      if(this.serverInfo){
        text = this.serverInfo.Version || "";
      }
      return text.substring(text.indexOf(" ") + 1);
    }
  },
  methods: {
    ...mapActions([
      "getServerInfo"
    ]),
    fixHover() {
        if(videojs.browser.IS_IOS||videojs.browser.IS_ANDROID) {
            for(var sheetI = document.styleSheets.length - 1; sheetI >= 0; sheetI--) {
                var sheet = document.styleSheets[sheetI];
                if(sheet.cssRules) {
                    for(var ruleI = sheet.cssRules.length - 1; ruleI >= 0; ruleI--) {
                        var rule = sheet.cssRules[ruleI];
                        if(rule.selectorText) {
                            rule.selectorText = rule.selectorText.replace(":hover", ":active");
                            rule.selectorText = rule.selectorText.replace(":focus", ":active");
                        }
                    }
                }
            }
        }
    },
    play(video){
      this.$refs['videoDlg'].play(video.videoUrl, video.videoTitle, video.snapUrl);
    },
    thisYear() {
      return moment().format('YYYY');
    }
  }
}
</script>

<style lang="less" scoped>
.wrapper {
  position: absolute;
  width: 100%;
  height: 100%;
}
.content-wrapper, .right-side, .main-footer {
  transition: none;
}
</style>

