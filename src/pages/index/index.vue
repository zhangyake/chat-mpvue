<template>
  <div class="app-chat">
    <scroll-view :scroll-y="motto" :scroll-top="scrollTop" style="height: 1200rpx;">
      <ul class="app-conetnt">
        <li v-for="(item,index) in chatMsgs " class="chat_item" :key="index">

          <img class="head-img-me" :src="userInfo.avatarUrl" v-if="item.isMe">
          <img class="head-img-robot" src="/static/img/robot.jpg" v-else>
          <div class="chat-msg-me" v-text="item.msg" v-if="item.isMe"></div>
          <div class="chat-msg-robot" v-text="item.msg" v-else></div>
          <div class="clear-both"></div>
        </li>

      </ul>
    </scroll-view>
    <div class="app-send">
      <div class="send-icon">
        😁
      </div>
      <input type="text" class="send-content" v-model="userMsg" />
      <div class="send-icon" @click="sendMsg">
        发送
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      motto: true,
      userInfo: {},
      scrollTop: 0,
      userMsg: '',
      chatMsgs: [
        { isMe: false, msg: 'Hello, 你好 !', time: '' },
      ]
    }
  },
  methods: {
    getUserInfo() {
      // 调用登录接口
      wx.login({
        success: () => {
          wx.getUserInfo({
            success: (res) => {
              this.userInfo = res.userInfo
            }
          })
        }
      })
    },
    sendMsg() {
      var that = this;
      var msg = that.userMsg;
      if (msg != '' && msg.toString().trim() != '') {
        that.chatMsgs.push({
          isMe: true,
          msg: msg,
          time: ''
        });
        that.userMsg = '';

        wx.request({
          url: 'https://zhangyake.site/api/robot', //仅为示例，并非真实的接口地址
          data: {
            info: msg,
            id: that.userInfo.nickName
          },
          header: {
            'content-type': 'application/json' // 默认值
          },
          success: function (res) {
            console.log(res.data)
            var data = res.data;
            if (data && data.hasOwnProperty('text')) {
              that.chatMsgs.push({
                isMe: false,
                msg: data.text,
                time: ''
              });
            } else {
              that.chatMsgs.push({
                isMe: false,
                msg: '我累了,需要休息,明天再聊吧!',
                time: ''
              });
            }
            that.scrollTop = that.scrollTop + 10000;
          }
        })

        // axios.get('/api/robot', {
        //   params: {
        //     info: msg
        //   }
        // }).then(function (response) {
        //   var data = response.data;
        //   if (data && data.hasOwnProperty('text')) {
        //     that.chatMsgs.push({
        //       isMe: false,
        //       msg: data.text,
        //       time: ''
        //     });
        //   }
        // })
        //   .catch(function (error) {
        //     console.log(error);
        //   });
      }

    }
  },

  created() {
    // 调用应用实例的方法获取全局数据
    this.getUserInfo()
  }
}
</script>

<style scoped>
.app-chat {
}
.app-conetnt {
  margin: 0 0 110rpx 0;
  padding-top: 20rpx;
}
.chat_item {
  margin-bottom: 20rpx;
}
.head-img-me {
  display: block;
  border-radius: 50%;
  width: 72rpx;
  height: 72rpx;
  float: right;
  border-radius: 50%;
  margin: 0 10rpx;
}
.head-img-robot {
  display: block;
  border-radius: 50%;
  width: 72rpx;
  height: 72rpx;
  float: left;
  border-radius: 50%;
  margin: 0 10rpx;
}

.chat-msg-robot {
  float: left;
  background: #eaebea;
  max-width: 60%;
  line-height: 48rpx;
  font-size: 32rpx;
  padding: 12rpx 18rpx;
  border-radius: 5rpx;
}

.chat-msg-me {
  float: right;
  background: #b0e56d;
  max-width: 60%;
  line-height: 48rpx;
  font-size: 32rpx;
  padding: 12rpx 18rpx;
  border-radius: 5rpx;
}
.clear-both {
  clear: both;
  /* height: 20rpx; */
}
.app-send {
  position: fixed;
  display: flex;
  bottom: 0;
  left: 0;
  right: 0;
  height: 100rpx;
  background: #f3f2f3;
  align-items: center;
}

.send-icon {
  flex: 0 0 100rpx;
  text-align: center;
}

.send-content {
  flex: 1;
  height: 72rpx;
  font-size: 36rpx;
  background: #fff;
  border: none;
  outline: none;
  border-radius: 3rpx;
  padding-left: 10rpx;
}
</style>
