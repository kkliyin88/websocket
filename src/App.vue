<template>
  <div>
    <p>{{time}}</p>
  </div>
</template>

<script>
  import {post,get} from './axios/axios.js';
  export default {
    components: {
    },
    data() {
      return {
        loading: false,
        time:'',
        websock:null
      }
    },
    mounted(){
      this.websocketTest();
    },
     created() {    
//     this.initWebSocket();
    },
     destroyed() {     
       this.websock.close() //离开路由之后断开websocket连接
    },
    methods: {
      websocketTest(){
        let  socket = new WebSocket('ws://localhost:8888');
        //客户端连接成功执行的回调函数
        socket.onopen =function(){
          console.log('客户端连接成功');
           if (socket.readyState===1) {
             console.log('1111')
             socket.send('发送数据');
          }
      };
      socket.onerror=function(error){
        console.log('error',error);
      }
//      socket.send('你好,服务器')
         //客户端收到服务器的消息执行的回调函数
         socket.onmessage =function(event){
          console.log('event',event);
        };
        
      },
      
      initWebSocket(){ //初始化weosocket
        const wsuri = "ws://localhost:8888";      
        this.websock = new WebSocket(wsuri);       
        this.websock.onmessage = this.websocketonmessage;    
        this.websock.onopen = this.websocketonopen;      
        this.websock.onerror = this.websocketonerror;        
        this.websock.onclose = this.websocketclose;
      },
      websocketonopen(){ //连接建立之后执行send方法发送数据
        let actions = {"test":"12345"};       
        this.websocketsend(JSON.stringify(actions));
      },
      websocketonerror(){//连接建立失败重连
        this.initWebSocket();
      },
      websocketonmessage(e){ //数据接收
        const redata = JSON.parse(e.data);
      },
      websocketsend(Data){//数据发送
        this.websock.send(Data);
      },
      websocketclose(e){  //关闭
        console.log('断开连接',e);
      },
    }
  }
</script>
<style scoped>
</style>
