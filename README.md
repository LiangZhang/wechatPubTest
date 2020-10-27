# wechatPubTest
这是一个连接微信公众号的前后端项目，深入浅出。为了演示其功能而做的开发


上周计划把微信公众号的基础知识梳理一遍，查阅相关的文章和资料。纵然如此，还是没能够让我理解整个微信公众号闭环是怎么走的。

所以，我希望能有一篇通俗易懂、深入浅出的文章把这些基础内容都讲清，最好是能够附带一个完整的例子，把所阐述的功能通过例子展示出来（这一点我要实名反对微信公众号的高赞回答者，都没有给个实际的案例出来，这让我们水平不高的开发怎么活，呜呜呜......）。

既然市场上没有这样的，那么我使用了小时候在查理芒格身上学到的方法——那我就自己来吧。

于是，我花了一整天的时间，写了一个[完整的demo](https://github.com/cute1baby/wechatPubTest)。

因为文字比较多，以免划走看不见，建议先点赞收藏，再继续看。

## 准备工作
接下来，按照我的学习习惯，我会在我的前端思维脑图中，给【微信公众号】画一个勾。像下面这样，有时间我把其他部分也按照这样的形式展开来讲。
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d1218f032ed54d699c9e0dd25f183a60~tplv-k3u1fbpfcp-watermark.image)

然后进入到【微信公众号】模块，展示我给你们准备的案例。我会在这个项目中给你演示获取access_token，自定义菜单、网页授权、模板推动这四个功能。它是长这样的：
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99eb63fd3b234c5b9dae77748cac20b5~tplv-k3u1fbpfcp-watermark.image)

我把前面4个部分完成了，留下了消息回复（我不是因为太懒了，而是留给你们的作业啊）。然后上面看到的测试号二维码就是我给大家写的案例，如果看不清楚，我在下面放了图片，可以直接扫码。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3dd32e9013b74c2990af7f9d7d942fd7~tplv-k3u1fbpfcp-watermark.image)


下面直奔主题了。


## 开发前准备
如果你是前端纯小白，那么这一部分你就要看看了；
如果你对微信公众号开发有一些基础了，那这部分可以先跳过。

预习一下openId，access_token是什么，有什么用（小白也不能太白，这个基础内容还是要继续看）。

### 微信开发工具
https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Web_Developer_Tools.html#4

### 域名和服务器的准备
我用的是自己在腾讯云购买的域名和服务器，域名是在网页授权时进行绑定。如果大家没有域名，可以用一个叫做ngrok的工具，不了解的可以参照[这篇文章](https://www.jianshu.com/p/571fdbc98d25)。

### 技术栈
前端的展示很简单，一共三个页面：活动页，静默授权页，主动授权页，用的是jquery + axios；

后端服务用了一个框架egg，不熟悉egg的你可以跳转[这里](https://eggjs.org/zh-cn/intro/quickstart.html)先补补课。因为我要作为例子讲解，所以代码非常简单，没有基础的胖友也能看懂。

<font style="color:red;">如果有egg框架的小白，我想请你帮个忙。先花费你一秒钟点赞和收藏，然后在评论区提出疑问，我看到了给你解答。</font>

### 注册测试号
下面跟我一起按照流程注册测试账号，一定要按照流程来哦。

- 1、进入[微信开发平台](https://developers.weixin.qq.com/doc/offiaccount/Getting_Started/Overview.html)
- 2、找到【开始开发】，点击【接口测试号申请】，点击【进入测试公众账号测试号申请系统】
- 3、微信扫码，进入页面
- 4、按照要求填写【接口配置信息】的token，填写【js接口安全域名】。

备注：因为【接口配置信息】/url的填写需要验证服务器有效性，所以此时配置会失败。这个填空先留下，下面讲到验证服务器有效性的时候就可以配置成功。

这里我假定大家都配置好了，如果有疑问请到评论区，看到了我会一一给大家解答（谁不是从小白走过来的呢，我最喜欢小白了）

现在测试号管理页面，除了【接口配置信息】/URL这一项没有填写，其他的都填好了，我假定代价都做好了这一步（完成了100%空手接白刃的动作）。

下面，是我配置的截图。
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5d246f167f174f5d8d8a4231b88cc1d7~tplv-k3u1fbpfcp-watermark.image)

然后，我要进入硬核部分。


## 验证服务器有效性
进入[这个链接](https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Access_Overview.html)，这里讲了验证服务器有效性的步骤。

上面配置部分不是还留了一个【接口配置信息】/url没有配置嘛，这一步就是为了解决这个问题的。首先，你肯定知道这个服务要部署到哪个地方，提供给前端的接口是什么。我用自己的给你们参考吧。

<font style="color:red;">如果有nginx配置的小白，我想请你帮个忙。先花费你一秒钟点赞和收藏，然后在评论区提出疑问，我看到了给你解答。</font>

通过nginx转发，我把自己的服务指向到`http://server.familyli.cn`。比如说你可以输入`http://server.familyli.cn/getMenuList`，这就是我获取自定义菜单的接口。

我自己定义`http://server.familyli.cn`设定为验证服务器有效性的接口，所以我把`http://server.familyli.cn`配置到【接口配置信息】/url中。

ok，现在我假定你看完了这个文档的流程，但我还是要苦口婆心的跟你总结一遍：
- 我们在【接口配置信息】/url配置的是一个接口，这个接口会由微信服务器发送get请求。这个请求会带有`signature,echostr,timestamp,nonce`这些参数。
- 验证有效性的原理是：将[timestamp, nonce, token]通过sha1加密后得到的值跟signature进行对比。如果相等，验证通过，返回echostr给微信服务器；如果不相等，验证失败。

如果配置正确，接口跑通，加密的值一定会等于signature，这是前提。

所以，接下来我的目标是如何写出`http://server.familyli.cn`这个接口，并且能够跑通。


### 编写验证服务器有效性接口
我把主要代码贴出来，并给了详细的注释。
```
/* router.js */
module.exports = app => {
    const { router, controller } = app;
    // 验证服务器的有效性
    router.get('/', controller.home.index);
}


/* controller/home.js */
class HomeController extends Controller {
  // 校验服务器配置的有效性
  async index() {
    const { ctx } = this;
    const {
        signature,
        echostr,
        timestamp,
        nonce
    } = ctx.query
    const {token} = config
    // 1、将参与微信加密签名的3个参数（timestamp、nonce、token），组合在一起，按照字典序排序并组合在一起形成一个数组
    const arr = [timestamp, nonce, token]
    const arrSort = arr.sort()
    // 2、将数组里所有参数拼接成一个字符串，进行sha1加密
    const str = arr.join('')
    const sha1Str = sha1(str)
    // 3、加密完成就生成了一个signature，和微信发送过来的进行对比。
    if(sha1Str === signature){
        // 如果判断和服务器上的signature相同，则输出echostr；并且进行下一步
        ctx.body = echostr
    } else {
        // 如果验证不一样，则返回error给前端
        ctx.body = ''
    }
  }
}
```
将上面的代码部署到自己服务器上，跑起来。然后将`http://server.familyli.cn`配置到【接口配置信息】/url，点击【提交】按钮，则配置成功。

做完上面这一步之后，就可以正式的开发微信公众号的功能了。


## 获取access_token
进入刚刚配置的[测试号管理页面](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)，将页面翻到【体验接口权限表】，查找类目【对话服务】，找到【基础支持后面】，找到[获取access_token](https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Get_access_token.html)这一项。

点进去，看下他的文档怎么写的。这一步很重要，我给大家简要的总结以下几点：
- access_token是公众号的全局唯一接口调用凭据，很多接口都要用access_token。
- access_token的有效期为2个小时。
- 每日的调用次数有限（测试号是2000次）。

所以，我要对access_token做一个保存和读取的功能。逻辑大概如下：
```
 *    读取本地文件(readAccessToken)
 *      - 本地有文件，判断是否过期(isValidAccessToken)
 *          - 过期了，重新获取access_token(getAccessToken)，保存下来覆盖之前的文件（保证文件是唯一的）saveAccessToken
 *          - 没有过期，直接使用
 *      - 本地没有文件
 *          - 发送请求获取access_token(getAccessToken)，保存下来（保存成本地文件）
 
 /* tool/accessToken.js */
 class Wechat {
 	/* 获取access_token */
 	getAccessToken(){},
    /* 保存access_token */
    saveAccessToken(accessToken){},
    /* 读取access_token */
    readAccessToken(){}
    /* 用来检查access_token是否有效 */
    isValidAccessToken(data){}
 }
```
具体逻辑移步[代码](https://github.com/cute1baby/wechatPubTest/blob/main/init/app/tool/accessToken.js)。

备注：我把secret直接虚掉了，大家换成自己的即可。
```
module.exports = {
    token: 'laoli0629shezhimogen10011',
    appID: 'wxd9f5805ea8e18784',
    appsecret: '*****'  // 我把appsecret值虚掉了，大家换成自己的即可
}
```


## 自定义菜单
进入刚刚配置的[测试号管理页面](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)，将页面翻到【体验接口权限表】，查找类目【对话服务】，找到【界面丰富】，找到[自定义菜单](https://developers.weixin.qq.com/doc/offiaccount/Custom_Menus/Creating_Custom-Defined_Menu.html)这一项。

文档很简单，简要总结一下：
自定义菜单接口有多种类型按钮，分别有click、view等类型，需要向微信服务器提交特定格式的一个json对象，即可设置成功。

同理，官方也提供了获取自定义菜单的接口，[点击这里可以查看](https://developers.weixin.qq.com/doc/offiaccount/Custom_Menus/Querying_Custom_Menus.html)

主要代码如下：
```
/* controller/home.js */
const menuList = {
    "button":[
        {	
            "type":"view",
            "name":"免费送书",
            "url":"http://wechat.familyli.cn/activity.html"
        },
        {	
            "name":"授权",
            "sub_button": [
                {	
                    "type":"view",
                    "name":"静默授权",
                    "url":"http://wechat.familyli.cn/authBySilent.html"
                },
                {	
                    "type":"view",
                    "name":"用户主动授权",
                    "url":"http://wechat.familyli.cn/authByUser.html"
                },
            ]
        }
    ]
}

class HomeController extends Controller {
    // 设置自定义菜单列表
    async setMenuList(){
      const { ctx } = this;
      const tokenRes = await w.fetchAccessToken()
      const {access_token} = tokenRes
      const url = ` https://api.weixin.qq.com/cgi-bin/menu/create?access_token=${access_token}`
      const menuListRes = await axios({
          method: 'POST',
          url,
          data: menuList
      })
      if(menuListRes){
          ctx.body = successRes('自定义菜单创建成功')
      }else{
          ctx.body = failRes('自定义菜单创建失败')
      }
    }
     // 获取自定义菜单列表
     async getMenuList(){
      const { ctx } = this;
      const tokenRes = await w.fetchAccessToken()
      const {access_token} = tokenRes
      console.log('access_token===', access_token)
      const url = `https://api.weixin.qq.com/cgi-bin/get_current_selfmenu_info?access_token=${access_token}`
      const menuListRes = await rp({
          method: 'GET',
          url,
          json: true
      })
      if(menuListRes){
          ctx.body = successRes(menuListRes)
      }else{
          ctx.body = failRes('自定义菜单获取失败')
      }
    }
}
```
这一步很简单，可直接[点击代码](https://github.com/cute1baby/wechatPubTest/blob/main/init/app/controller/home.js)，进行查看。


## 网页授权
进入刚刚配置的[测试号管理页面](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)，将页面翻到【体验接口权限表】，查找类目【网页服务】，找到【网页账户】，找到[网页授权获取用户基本信息](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html)这一项。

在[测试号管理页面](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)的[网页授权获取用户基本信息]后面有一个【修改】按钮，点击它，配置授权回调页面域名。这个域名就是你放前端页面的地方，截图如下：
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8a943f35fa0146ccb3b98c2d89e450f1~tplv-k3u1fbpfcp-watermark.image)


网页授权有两种。一种是静默授权，可以直接获取用户的openid；一种是用户主动授权，需要用户主动点击同意授权，可获取用户的基本信息。

下面分开来讲。

### 静默授权
静默授权一共有下面步骤：
- 1、重定向到链接`https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx520c15f417810387&redirect_uri=###&response_type=code&scope=snsapi_base&state=123#wechat_redirect`
- 2、获取用户openId

第一步是在前端做的，可以[跳转这里](https://github.com/cute1baby/wechatPubTest/blob/main/authBySilent.html)进行查看

第二步在后端，主体代码如下
```
/* controller/home.js */

// 静默授权，获取openId
  async getOpenId(){
    const { ctx } = this;
    const {appID, appsecret} = config
    const {code} = ctx.query
    // 授权第二步,获取网页授权access_token
    const url = `https://api.weixin.qq.com/sns/oauth2/access_token?appid=${appID}&secret=${appsecret}&code=${code}&grant_type=authorization_code`
    const authRes = await rp({
        method: 'GET',
        url,
        json: true
    })
    if(authRes){
        ctx.body = successRes(authRes)
    }else{
        ctx.body = failRes('获取网页授权access_token失败')
    }
  }
```
详情，请[移步这里](https://github.com/cute1baby/wechatPubTest/blob/main/init/app/controller/home.js)


### 用户主动授权
用户主动授权一共有下面步骤：
- 1、重定向到链接`https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx520c15f417810387&redirect_uri=###&response_type=code&scope=snsapi_userinfo&state=123#wechat_redirect`
- 2、获取用户openId和网页access_token
- 3、通过openId和网页access_token获取用户信息

第一步是在前端做的，可以[跳转这里](https://github.com/cute1baby/wechatPubTest/blob/main/authByUser.html)进行查看

第二步在后端，主体代码如下：
```
/* controller/home.js */

  // 用户主动授权，获取用户信息
  async getAuthorization(){
    const { ctx } = this;
    const {appID, appsecret} = config
    const {code} = ctx.query
    // 授权第二步,获取网页授权access_token
    const url = `https://api.weixin.qq.com/sns/oauth2/access_token?appid=${appID}&secret=${appsecret}&code=${code}&grant_type=authorization_code`
    const authRes = await rp({
        method: 'GET',
        url,
        json: true
    })
    if(authRes){
        const {
            access_token,
            openid
        } = authRes;
        // 授权第四步，获取用户信息
        const authUrl = `https://api.weixin.qq.com/sns/userinfo?access_token=${access_token}&openid=${openid}&lang=zh_CN`
        const userRes = await rp({
            method: 'GET',
            url: authUrl,
            json: true
        })
        if(userRes){
            ctx.body = successRes(userRes)
        }else{
            ctx.body = failRes('网页授权获取用户信息失败')
        }
    }else{
        ctx.body = failRes('获取网页授权access_token失败')
    }
 }
```
详情，请[移步这里](https://github.com/cute1baby/wechatPubTest/blob/main/init/app/controller/home.js)


## 发送模板消息
进入刚刚配置的[测试号管理页面](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)，将页面翻到【体验接口权限表】，查找类目【对话服务】，找到【发送消息】，找到[模板消息（业务通知）](https://mp.weixin.qq.com/debug/cgi-bin/readtmpl?t=tmplmsg/faq_tmpl)这一项。

实现模板消息分两步。
- 第一步：获取template_id
- 第二步：请求接口

要完成第一步，我们先回到[测试号管理页面](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)，找到【模板消息接口】，然后点击【新增测试模板】按钮，按照要求填写。

我把我填写的分享出来：
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8033a23359044ea3ac87ec1f8f763177~tplv-k3u1fbpfcp-watermark.image)

这里的【模板ID】就是第一步说的template_id，第二步的接口请求要要用到。

为了演示项目的完整性，我在公众号菜单上添加了【免费送书】模块，点击填写后就会发送模板消息。可关注公众号点击【免费送书】查看，前端代码可[点击这里](https://github.com/cute1baby/wechatPubTest/blob/main/activity.html)查看。

公众号二维码如下：  
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3dd32e9013b74c2990af7f9d7d942fd7~tplv-k3u1fbpfcp-watermark.image)


### 请求接口的代码
```
/* controller/home.js */

  // 获取模板信息并发送
  async getTemplateInfo(){
    const { ctx } = this;
    const {
        openid,
        user,
        tel,
        address
    } = ctx.query
    const tokenRes = await w.fetchAccessToken()
    const {access_token} = tokenRes
    const url = `https://api.weixin.qq.com/cgi-bin/message/template/send?access_token=${access_token}`
    const dataObj = {
        'touser': openid,
        'template_id': 'L47xpv5RWcz9lV1pg6mCD2e2rdNBe1_f3WE6tXXgsrs',
        'data': {
            'user': {
                'value': user
            },
            'tel': {
                'value': tel
            },
            'address': {
                'value': address
            },
            'remark': {
                'value': '若有问题请联系客服咨询，谢谢！'
            }
        }
    }
    console.log(JSON.stringify(dataObj))
    const {data} = await axios({
        method: 'POST',
        url,
        data: JSON.stringify(dataObj)
    })
    console.log('authRes>>>>>>', data)
    if(data){
        ctx.body = successRes(data)
    }else{
        ctx.body = failRes('获取模板信息失败')
    }
 }
```
详情，请[移步这里](https://github.com/cute1baby/wechatPubTest/blob/main/init/app/controller/home.js)

最后，还有一个发送消息的功能没有做（因为工作太忙了，写这篇文章和写demo都花了我整一天的时间，呜呜呜...）。

## 总结
因为市场上没有讲的比较透彻的文章，所以我自己从0到1写了一个demo，希望帮助到有需要的胖友。

这个demo做了获取access_token、自定义菜单、网页授权、推送模板消息四个功能。从创建账号到每个模块的功能实现，目的是让胖友们把整个微信公众号的流程都搞透。

而这些功能，仅仅起到抛砖引玉的作用，其他功能参照文档、按照这个模式来做即可。因此达到掌握这几个，搞定90%微信公众号功能开发的目的。

如果有帮助，请胖友们点个赞给我最大的支持。

## 备注
如果哪个部分没有讲清楚或者有疑惑的地方，欢迎在评论区留言，我都会回复的啊！
