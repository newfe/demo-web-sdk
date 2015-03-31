demo-web-sdk
============

Demostration of Rong Web SDK.

## 融云 web SDK 如何使用

[文档参考](http://docs.rongcloud.cn/api/js/index.html " SDK 文档")


使用融云 `web SDK` 发消息之前必须利用申请的`appkey`进行初始化，只有在初始化之后才能使用RongIMClient.getInstance()方法得到实例.如只想知晓如何使用 web SDK 请参考 `SDK_Demo.html`

## 指定版本号引用
`http://res.websdk.rong.io/RongIMClient{-版本号}-min.js` 如不添加则默认为最新版本SDK，添加版本号则加载指定版本SDK

### 初始化web sdk ，此项必须设置
```js
RongIMClient.init("appkey");
```
### 设置链接状态监听器，此项必须设置
```js
RongIMClient.setConnectionStatusListener({  
     onChanged: function (status) {  
         window.console.log(status.getValue(), status.getMessage(), new Date()) 
     }  
}); 
```
### 链接融云服务器，此项必须设置

此方法为异步方法，请确定链接成功之后再执行其他操作。成功返回登录人员id失败则返回失败枚举对象
```js
RongIMClient.connect("token", {
     onSuccess: function (userid) {
         window.console.log("connected，userid＝" + userid)
     },
     onError: function (x) {
         window.console.log(x.getMessage())
     }
});
```
### 设置消息监听器，此项必须设置

所有接收的消息都通过此监听器进行处理，可以通过message.getMessageType()和RongIMClient.MessageType枚举对象来判断消息类型
```js
RongIMClient.getInstance().setOnReceiveMessageListener({
     onReceived: function (message) {
         //message为RongIMMessage子类实例
         console.log(message.getContent());
     }
});
```
### 得到RongIMClient实例对象,只有执行init()之后才能使用getInstance()方法
```js
var ins = RongIMClient.getInstance();
```
### 设置私人会话类型
```js
var contype = RongIMClient.ConversationType.PRIVATE;
```
### 例如注册某个元素点击事件(举例)
```js
element.onclick = function () {
//调用实例的发送消息方法
     ins.sendMessage(contype, "targetId", RongIMClient.TextMessage.obtain("发送消息内容"), null, {
           onSuccess: function () {
                //发送成功逻辑处理
           },
           onError: function (data) {
                //发送失败逻辑处理
                console.log(data.getValue(),data.getMessage())
           }
       });
};
```
### 使用指定链接通道链接服务器 
web SDK 通道才用层层降级的方式进行兼容处理。连接通道首先默认使用websocket，如环境不支持websocket则自动降级至flash socket，不支持flash则自定降级至xhr-polling，以此来达到全兼容的目的。
<br/>
如果想强制使用长链接连接服务器则必须设置`window.WEB_XHR_POLLING = true;`
#### 通道选项设置[使用此项必须为0.9.6版本,使用前请确定SDK版本号为0.9.6及以上版本]
```js
     //强制使用长链接进行通讯 设置此项，并保证此项优先级最高并且最先被执行，否则设置无效
     window.WEB_XHR_POLLING = true;
  ```
  ```js
     //强制使用flash进行通讯 设置此项，并保证此项优先级最高并且最先被执行，否则设置无效
     window.WEB_SOCKET_FORCE_FLASH = true;
```
##通道选项优先级比较
`window.WEB_SOCKET_FORCE_FLASH > window.WEB_XHR_POLLING`

#### 注意:
`web SDK` 是全异步的，所以发送消息之前确保链接成功。
本demo仅做演示使用，页面不做兼容性考虑。
本`web SDK`为强兼容性，demo的弱兼容性与SDK无关。
使用本示例的页面在商业上使用而引发的处理不当与本人以及本人所属组织无关。
本示例仅做演示，仅仅只做演示。未考虑低版本及部分版本浏览器兼容性。

<hr>
## 表情帮助库如何使用？
`http://res.websdk.rongcloud.cn/RongIMClient.emoji-0.9.2.min.js` 融云官方表情帮助库引用地址。本表情库使用的是标准的 `emoji` 表情。本表情库一共有128个表情,为32＊32大小。表情库是基于 `web SDK` 的，使用之前请务必提前载入 `web SDK`.表情库中的方法均为`静态方法`.由于部分浏览器显示本表情的 `tag` 为一个小方块。无法得到内容。此处可用escape()方法得到表情 `tag` 
```js
     console.log('\ud83d\ude00');
     //例如chrome中显示 \ud83d\ude00 为一个小方块，可用escape()方法
     var str = escape('\ud83d\ude00');
     console.log(str);
     //返回％ud83d％ude00，将％替换为\既是表情的 tag 属性
     
     //表情对象的格式为
     var emojiObject={
          englishName:'表情英文名称',
          chineseName:'表情中文名称',
          img:'一个nodeName为B的HTMLElement元素，背景为表情图片',
          tag:'表情的标签，为一组unicode码'
     }
```

#### RongIMClient.Expression.getAllExpression
得到指定数量的表情
```js 
 var emojiObjectList = RongIMClient.Expression.getAllExpression(64,0);
  //从下标为0的位置检索64个表情对象,startIndex与count只和最大为128，因为表情对象最多为128个。
  for(var i=0,item;item=emojiObjectList[i++];){
     console.log(item.englishName,item.chineseName,item.img,item.tag);
     //依次打印表情元素
  }
```

#### RongIMClient.Expression.getEmojiByContent
根据表情的 `content` ( `content` 为一个UTF16码，可根据表情对象 `tag` 属性计算得到此UTF16码)来得到表情对象
```js
     var emojiObject = RongIMClient.Expression.getEmojiByContent('\uF600');
     //如果传入的不是合法UTF16码或者表情名单中不存在此UTF16码则返回 undeined 
     console.log(emojiObject);
```

#### RongIMClient.Expression.calcUTF(emojiContent)
根据表情的 `content` 计算得到表情的 `tag` 属性
```js
     var tag = RongIMClient.Expression.calcUTF('\uF600');
     console.log(tag);
     //返回\ud83d\ude00 即将 \uF600 计算为 \ud83d\ude00
```

#### RongIMClient.Expression.getEmojiObjByEnglishNameOrChineseName
根据表情的中文名称或者英文名称得到表情对象
```js
     var emojiObject = RongIMClient.Expression.getEmojiObjByEnglishNameOrChineseName("足球");
     console.log(emojiObject);
```

#### RongIMClient.Expression.retrievalEmoji(string,callback)
检索传入的字符串中是否含有表情 `tag`，如有则根据传入的callback函数执行制定操作,callback函数中 `务必带返回值` 
```js
     var str = RongIMClient.Expression.retrievalEmoji('这是一个表情\ud83d\ude00',function(emojiObject){
          console.log(emojiObject);
          return emojiObject.chineseName;
     });
     console.log(str);
     //打印为 这是一个表情狞笑
     
```
### 浏览器兼容性
```js
 // IE 6+ 、chrome 3+ 、firefox 3.5+ 、safari 4+ 、 opera 12+
```
<hr>
## 音频播放帮助库如何使用？
`http://res.websdk.rongcloud.cn/RongIMClient.voice-0.9.1.min.js` 融云官方音频播放帮助库引用地址.音频播放帮助库是基于 `web SDK` 的，使用之前请务必提前载入 `web SDK`.音频播放帮助库中的方法均为`静态方法`.与`IE`以及`opera`内核类型浏览器不兼容。

#### RongIMClient.voice.init
初始化音频播放帮助库,使用本库之前务必进行初始化操作，返回为 `boolean` 类型用来说明次音频库是否已经初始化完成
```js
     var isInit = RongIMClient.voice.init();
     console.log(isInit);
     //可以根据isInit来判断是否已经初始化完成
```

#### RongIMClient.voice.play
播放传入的格式为AMR的音频BASE64码
```js
     RongIMClient.voice.play('音频BASE64码','音频持续时间(秒)');
     //如果不知道音频的持续时间，可通过音频base64长度除以1024得到大概秒数
```
#### RongIMClient.voice.onprogress
音频播放过程中执行的进度方法,需自行注册.
```js
     RongIMClient.voice.onprogress ＝ function(){
          console.log("正在执行");
     };
     //执行单位时间为一秒执行一次
```
### 浏览器兼容性
```js
 //pc :     Firefox (Gecko) 3.6 (1.9.2)+     Chrome 7+	 Safari 6.0.2+
 //mobile :  Firefox (Gecko) 32+     Android 3+	 Safari 6.1+
```
