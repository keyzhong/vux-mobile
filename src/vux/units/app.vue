<template>
  <app-header></app-header>
  <router-view transition="switch" transition-mode="out-in"></router-view>
  <app-footer></app-footer>
  <i class="hidden" v-el:toast></i>
  <div :class="{'dialog': dialogState, 'hide': !dialogState}" v-el:dialog>
    <div class="dialogBox">
      <div class="title">{{dialogtitle}}</div>
      <div class="content">{{dialogcontent}}</div>
      <div class="btnGrp ui-border-t">
        <button class="btn cancel" v-show="cancelState">取消</button>
        <button class="btn ok ui-border-l">确定</button>
      </div>
    </div>
  </div>
  <div class="wait" :class="{'dialog': loaderState, 'hide': !loaderState}" v-el:dialog-load>
    <div class="wrap">
      <div class="spinner"><div class="spinner-icon"></div></div>
    </div>
  </div>
</template>

<style src="../../assets/css/global.css"></style> <!-- 基础全局样式 -->
<style src="../../assets/css/border.css"></style> <!-- 1px border 解决方案 -->
<style src="../../assets/css/scrollLoad.css"></style>
<style src="../../assets/css/nprogress.css"></style> 
<style src="../../assets/css/swiper.css"></style>
<style src="../../assets/css/scroller.css"></style>
<style src="../../assets/css/datetimepicker.css"></style>

<script>
import AppHeader from './header.vue'
import AppFooter from './footer.vue'

export default {
  data () {
    return {
      dialogState: false,
      dialogtitle: '提示',
      dialogcontent: '暂无提示',
      cancelState: true,
      loaderState: false
    }
  },

  components: {
    AppHeader,
    AppFooter
  },
  
  ready () {
    var view = this;
    var els = this.$els;
    var toastEl = els.toast;
    var dialogEl = els.dialog;
    var dialogLoadEl = els.dialogLoad;
    var cancelBtn = dialogEl.getElementsByClassName('cancel')[0];
    var okBtn = dialogEl.getElementsByClassName('ok')[0];

    function prevDefault(el){
      el.addEventListener('touchmove', function(e){e.preventDefault()}, false);
    }
    prevDefault(dialogEl);
    prevDefault(dialogLoadEl);

    // 全局清除toast
    VUX.clearToast = function(){
      toastEl.textContent = '';
      toastEl.className = 'hide';
      VUX.toastTimer && clearTimeout(VUX.toastTimer);
    }

    /* 
     * ## 定义一个全局toast方法，包含报错提示、操作提示、成功提示三种常用方式
     * text  --> 为必填，为提示文本内容； 
     * delay --> 为显示时间，默认为3000ms，参数不得小于1000ms，选填；
     * type  --> 提示类型('error','success')，默认为操作提示，选填；
     */
    VUX.toast = function(opt){
      var time = 3000;
      if(opt.delay){
        opt.delay < 1000 ? time = 1000 : time = opt.delay;
      }
      var clsName = 'toast ';
      if(opt.text.length > 18) clsName += 'break ';
      if(opt.type) clsName += opt.type;
      toastEl.textContent = opt.text;
      toastEl.className = clsName;
      if(VUX.toastTimer) clearTimeout(VUX.toastTimer);
      VUX.toastTimer = setTimeout(VUX.clearToast, time)
    }

    // 获取dialog状态
    VUX.getDialogState = function(){
      return view.dialogState
    }

    // 清除dialog框
    VUX.clearDialog = function(){
      view.dialogState = false;
    }

    /* 
     * ## 定义一个全局的dialog，包含alert和confirm两个功能
     * type     --> dialog类型 ('alert'、'confirm')
     * title    --> dialog标题
     * content  --> dialog文本内容
     * okFn     --> 确定按钮绑定事件
     * cancelFn --> 取消按钮绑定事件
     */
    VUX.dialogOk = VUX.dialogCancel = function(){};
    VUX.dialog = function(opt){
      view.dialogtitle = opt.title || '提示';
      view.dialogcontent = opt.content || '暂无提示';
      opt.type == 'alert' ? view.cancelState = false : view.cancelState = true;
      view.dialogState = true;
      VUX.dialogOk = function(){
        if (opt.okFn) opt.okFn();
        view.dialogState = false;
        VUX.dialogOk = function(){};
      }
      VUX.dialogCancel = function(){
        if (opt.cancelFn) opt.cancelFn();
        view.dialogState = false;
        VUX.dialogCancel = function(){};
      }

    }

    // dialog确认取消按钮全局绑定事件
    cancelBtn.addEventListener('click', function(){
      VUX.dialogCancel()
    }, false);
    okBtn.addEventListener('click', function(){
      VUX.dialogOk()
    }, false);

    //控制显示和隐藏等待层
    VUX.showWaitPanel = function(){
      view.loaderState = true
    }
    VUX.hideWaitPanel = function(){
      view.loaderState = false
    }

  }
}
</script>

