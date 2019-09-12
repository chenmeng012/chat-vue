// scrollLoader.vue
// 滚动加载组件

<style scoped>
   .container-main {margin: 0 auto; overflow: auto; overflow-x: hidden; padding: 0;}
   .loading{ width: 100%; height: 40px; position: relative; overflow: hidden; text-align: center; margin: 5px 0 ; color: #999; font-size: 13px;}
   .loading-icon{color: #707070;}
   .loader {
        font-size: 10px;
        margin: 8px auto;
        text-indent: -9999em;
        width: 24px;
        height: 24px;
        border-radius: 50%;
        background: #999;
        background: -moz-linear-gradient(left, #999 10%, rgba(255, 255, 255, 0) 42%);
        background: -webkit-linear-gradient(left, #999 10%, rgba(255, 255, 255, 0) 42%);
        background: -o-linear-gradient(left, #999 10%, rgba(255, 255, 255, 0) 42%);
        background: -ms-linear-gradient(left, #999 10%, rgba(255, 255, 255, 0) 42%);
        background: linear-gradient(to right, #999 10%, rgba(255, 255, 255, 0) 42%);
        position: relative;
        -webkit-animation: load3 1s infinite linear;
        animation: load3 1s infinite linear;
    }
    .loader:before {
        width: 50%;
        height: 50%;
        background: #999;
        border-radius: 100% 0 0 0;
        position: absolute;
        top: 0;
        left: 0;
        content: '';
    }
    .loader:after {
        background: #f5f5f5;
        width: 72%;
        height: 75%;
        border-radius: 68%;
        content: '';
        margin: auto;
        position: absolute;
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
    }
    @-webkit-keyframes load3 {
    0% {
        -webkit-transform: rotate(0deg);
        transform: rotate(0deg);
    }
    100% {
        -webkit-transform: rotate(360deg);
        transform: rotate(360deg);
    }
    }
    @keyframes load3 {
    0% {
        -webkit-transform: rotate(0deg);
        transform: rotate(0deg);
    }
    100% {
        -webkit-transform: rotate(360deg);
        transform: rotate(360deg);
    }
    }

</style>

<template>
    <div id="scrollLoader-container" class="container-main">
        <div class="loading" v-if="topLoading">
            <div class="loader">加载中...</div>
        </div>

        <div :style="'min-height:' + realMinHeight + 'px; overflow-x:hidden'">
            <slot></slot>
        </div>

        <div class="loading" v-if="bottonLoading">
            <div class="loader">加载中...</div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "scroll-loader",
        
        props: {
            //给slot传一个最小值，保证一开始能出现滚动条
            'minHeight': {
                type: Number,
                default: 700
            }, 
                
        },

        created(){
        },
        computed: {
            realMinHeight(){
                return this.minHeight
            }
        },
        data() {
            return {
               topLoading: false,
               bottonLoading: false,

               stopTopLoading: false, //是否停止传播滚动到顶部事件
               stopBottonLoading: false,  //是否停止传播滚动到底部事件
            }
        },
        mounted(){
            this.listenScroll();
        },

        methods: {
            listenScroll(){
                var me = this;
                var topDone = (stopTopLoading) => {
                    me.topLoading = false;
                    if(stopTopLoading) me.stopTopLoading = true;
                };

                var bottonDone = (stopBottonLoading) => {
                    me.bottonLoading = false;
                    if(stopBottonLoading) me.stopBottonLoading = true;
                };
                setTimeout(function(){
                    var scrollContainer = document.getElementById('scrollLoader-container');
                    addEvent(scrollContainer,'DOMMouseScroll', scrollFunc);
                    addEvent(scrollContainer,'mousewheel', scrollFunc);
                    function scrollFunc(e){
                        e = e || window.event;
                        let upStatus = false, downStatus = false;
                        if (e.wheelDelta){  //判断浏览器IE，谷歌滑轮事件
                            if (e.wheelDelta > 0) { //当滑轮向上滚动时
                                upStatus = true;
                            }
                            if (e.wheelDelta < 0) { //当滑轮向下滚动时
                                downStatus = true;
                            }
                        }else if (e.detail) {  //Firefox滑轮事件
                            if (e.detail> 0) { //当滑轮向上滚动时
                                upStatus = true;
                            }
                            if (e.detail< 0) { //当滑轮向下滚动时
                                downStatus = true;
                            }
                        }


                        if(scrollContainer.scrollTop<=0 && upStatus){
                            if(me.topLoading) return;

                            if (!me.stopTopLoading){
                                me.topLoading = true;
                                me.$emit('scroll-to-top', topDone);
                            }else{
                                me.stopTopLoading = false;
                            }
                        }
                        if((scrollContainer.offsetHeight + scrollContainer.scrollTop+1 >= scrollContainer.scrollHeight) && downStatus){
                            if(me.bottonLoading) return;

                            if (!me.stopBottonLoading){
                                me.bottonLoading = true;
                                setTimeout(() => {
                                    scrollContainer.scrollTop += 50;
                                },10);
                                me.$emit('scroll-to-botton', bottonDone);
                            }else{
                                me.stopBottonLoading = false;
                            }
                        }
                    };
                    function addEvent(obj, xEvent, fn) {
                        if(obj.attachEvent) {
                            obj.attachEvent('on'+xEvent, fn)
                        }else{
                            obj.addEventListener(xEvent, fn, false)
                        }
                    };

                }, 50)
            },

        }
    }
</script>