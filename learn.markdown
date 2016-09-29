
# 微信小程序学习小记


### 1.文档结构

使用开发工具新建项目后，开发工具会自动生成一些文件，结构如下

![structure.png](F:/campus/wechatSmallAppMd/image/structure.png "项目结构")

其中app.js,app.json,app.wxss是全局文件，其中，.js后缀的是脚本文件，.json后缀的文件是配置文件，.wxss后缀的是样式表文件。
* app.js是脚本文件，这里可以调用微信的一些api
* app.json 是对整个小程序的全局配置，微信小程序中的每一个页面的【路径+页面名】都需要写在 app.json 的 pages 中，且 pages 中的第一个页面是小程序的首页
* app.wxss 是整个小程序的公共样式表
* pages文件夹存放的是创建的每一个界面**新建的每一个界面都要在app.json中配置**，每一个小程序页面是由同路径下同名的四个不同后缀文件的组成，如：index.js、index.wxml、index.wxss、index.json。.js后缀的文件是脚本文件，.json后缀的文件是配置文件，.wxss后缀的是样式表文件，.wxml后缀的文件是页面结构文件。
* pages文件夹下的每个文件夹是我们创建界面所在的文件夹，你可以看见每个文件下会分别有自己的js、json、wxss、wxml文件；其中，当有页面样式表时，页面的样式表中的样式规则会层叠覆盖 app.wxss 中的样式规则；当有页面的配置文件时，配置项在该页面会覆盖 app.json 的 window 中相同的配置项

###2.新建页面
在pages下面新建一个文件夹，添加wxml文件，js文件，wxss和json文件可选，为了减少配置的麻烦，微信官方规定这三个文件夹必须同名，**一定记得新建界面后在app.json中配置页面路径**
* **增加入口**，在首页中添加一个button按钮，代码如下![enter.png](F:/campus/wechatSmallAppMd/image/enter.png "")其中使用class类名可以定义样式，bindtap绑定事件函数，函数写在welcom.js中 
```
navigateTo: function () {
    wx.navigateTo({ url: '../index/index' })
  }
``` 
即可实现跳转到主页面

*  **写一个导航栏** 在wxml中编写代码，大体上和html文件一样，主要一些标签变化


```
<view class = "container banner">
    <view class="nav-bar">
      <button class="btn" bindtap="navigateToOne">导航一</button>
      <button class="btn" bindtap="navigateToTwo">导航二</button>
      <button class="btn" bindtap="navigateToThree">导航三</button>
      <button class="btn" bindtap="navigateToFour">导航四</button>
    </view>
</view>
```
wxss中定义样式，js中编写事件

> 增加一个轮播图

* 先编写结构

```
<swiper indicator-dots="{{indicatorDots}}"
  autoplay="{{autoplay}}" interval="{{interval}}" duration="{{duration}}">
  <block wx:for="{{imgUrls}}">
    <swiper-item>
      <image src="{{item}}" class="slide-image" width="355" height="150"/>
    </swiper-item>
  </block>
</swiper>

```

```
data: {
    imgUrls: [
      'http://img02.tooopen.com/images/20150928/tooopen_sy_143912755726.jpg',
      'http://img06.tooopen.com/images/20160818/tooopen_sy_175866434296.jpg',
      'http://img06.tooopen.com/images/20160818/tooopen_sy_175833047715.jpg'
    ],
    indicatorDots: false,
    autoplay: false,
    interval: 5000,
    duration: 1000
  },
  changeIndicatorDots: function(e) {
    this.setData({
      indicatorDots: !this.data.indicatorDots
    })
  },
  changeAutoplay: function(e) {
    this.setData({
      autoplay: !this.data.autoplay
    })
  },
  intervalChange: function(e) {
    this.setData({
      interval: e.detail.value
    })
  },
  durationChange: function(e) {
    this.setData({
      duration: e.detail.value
    })
  }
```
swiper中为图片轮播的区域，其中autoplay，interval，duration为轮播图的属性，其中autoplay为设置是否自动播放，interval为设置是否时间间隔即每张图片的显示时间，duration为延迟时间即图片在滑动时所需的时间，wx:for为一个循环语句，循环js中的imgurl值；在js文件中，主要编写，事件函数，比如changeAutoplay()改变是否自动播放，intervalChange()改变间隔时间，durationChange()改变延迟时间

>增加目录列表

* 先编写我们想要的页面结构

```
<view class="news-item-container">
    <block wx:for="{{list}}" wx:for-index="id">
      <text wx:if="{{item.header}}" class="sub-title">{{item.header}}</text>
      <navigator wx:else url="../detail/detail?id={{item.id}}">
        <view class="news-item" >
          <view class="news-item-left">
            <text class="news-item-title">{{item.title}}</text>
          </view>
          <view class="news-item-right">
            <image src="{{item.images[0]}}" class="news-image"/>
          </view>
        </view>
      </navigator>
    </block>
    <button type="primary" class="load-btn" size="mini" loading="{{loading}}" plain="{{plain}}" bindtap="loadMore"> 更多 </button>
  </view>
```
以上为一个循环块，wx:for用来循环list中的内容，即我们目录列表上的每个条目，这里是比较简单的内容很好理解。主要页面逻辑在index.js文件中，我们来看看

```
 onLoad: function () {
    var that = this
    wx.request({
      url: 'http://news-at.xxx.com/api/4/news/latest',
      headers: {
        'Content-Type': 'application/json'
      },
      success: function (res) {
         that.setData({
           banner: res.data.top_stories,
           list: [{ header: '今日热闻' }].concat(res.data.stories)
         })
      }
    })
```
onload为页面加载的时候调用的函数，url为请求目录列表数据的api，headers为请求协议的首部信息（熟悉计算机网络的都知道），此处数据类型为json，success为请求成功后调用的函数，setData内为请求返回的data

**我觉得微信小程序，前端内容主要是页面的编写，并向一些后台写好的api请求数据并解析展示在页面上**

























