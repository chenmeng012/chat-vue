//主文件，对wxChat的用法做示例

<template>
<wxChat 
  :data="wxChatData"
  :showShade="false"
  :contactNickname="'财天下'"
  :maxHeight = "500"
  :minHeight = "500"
  :getUpperData="getUpperData"
  :getUnderData="getUnderData"
  :ownerAvatarUrl="ownerAvatarUrl"
  :contactAvatarUrl="contactAvatarUrl"
  :width="width">
</wxChat>
</template>

<script>
import wxChat from './components/wxChat.vue'

export default {
  name: 'app',
  data () {
    return {
      upperTimes: 0,
      underTimes: 0,
      upperId: 0,
      underId: 6,
      width: window.screen.width,
      ownerAvatarUrl: './src/assets/avatar1.jpg',
      contactAvatarUrl: './src/assets/avatar2.jpg',
      wxChatData: [{
        direction: 2,
        id: 100,
        type: 1,
        content: '你好!![呲牙]',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 1,
        id: 200,
        type: 1,
        content: '你也好。[害羞]',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 2,
        id: 300,
        type: 1,
        content: '这是我的简历头像：',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 2,
        id: 400,
        type: 2,
        content: './src/assets/wyz.jpg',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 1,
        id: 500,
        type: 1,
        content: '你开心就好。[微笑]',
        ctime: new Date().toLocaleString()
      },]
    }
  },
  components:{wxChat},
  created(){
    this.initWidth();
  },
  methods:{
    initWidth(){
      var ua = navigator.userAgent;
      var ipad = ua.match(/(iPad).*OS\s([\d_]+)/),
      isIphone =!ipad && ua.match(/(iPhone\sOS)\s([\d_]+)/),
      isAndroid = ua.match(/(Android)\s+([\d.]+)/),
      isMobile = isIphone || isAndroid;
      //非移动端设置400px宽度，移动端是100%
      if(!isMobile){
        this.width = 400
      }
    },

    //向上滚动加载数据
    getUpperData(){
      var me = this;
      
      // 这里为模拟异步加载数据
      // 实际上你可能要这么写:
      // return axios.get('xxx').then(function(result){
      //     return result;  //result的格式需要类似下面resolve里面的数组
      // })
      return new Promise(function(resolve){
        setTimeout(function(){
           //模拟加载完毕
          if(me.upperTimes>3){
            return resolve([]);
          }
          
          //加载数据
          resolve([{
              direction: 2,
              id: me.upperId-1,
              type: 1,
              content: '向上滚动加载第 ' + me.upperTimes +' 条！',
              ctime: new Date().toLocaleString()
            },
            {
              direction: 1,
              id: me.upperId-2,
              type: 1,
              content: '向上滚动加载第 ' + me.upperTimes +' 条！',
              ctime: new Date().toLocaleString()
            }]
      
          )
        }, 1000);
        me.upperId= me.upperId+2;
        me.upperTimes++;
      })
    },

    getUnderData(){
      var me = this;

      //意义同getUpperData()
      return new Promise(function(resolve){
        setTimeout(function(){
          //模拟加载完毕
          if(me.underTimes>3){
            resolve([]);
          }else{
              //加载数据
              resolve(
                  [{
                      direction: 1,
                      id: me.underId+1,
                      type: 1,
                      content: '向下滚动加载第 ' + me.underTimes +' 条！',
                      ctime: new Date().toLocaleString()
                  },
                  {
                      direction: 2,
                      id: me.underId+2,
                      type: 1,
                      content: '向下滚动加载第 ' + me.underTimes +' 条！',
                      ctime: new Date().toLocaleString()
                  }]
              )
          }
        }, 1000);

        me.underId = me.underId+2;
        me.underTimes++;
      })
    }

  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
}

</style>
