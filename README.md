# 小程序tabBar跳转页面并隐藏tabBar
## 前言

在开发小程序过程中，相信有一部分人，遇到过一个问题：当使用tabBar跳转页面时，所跳转的页面下方必定有 tabBar显示，而当你需要把它隐藏时，却束手无策。话不多说，在这里给大家分享如何隐藏tabBar的方法。

### 方法一:自定义tabBar

使用自定义tabBar，新建一个tarBar.wxml模板页，然后引用模板的页面传入数据即可，代码如下：

``` 
<template name="tabBar">  
  <view class="flex-h" style="color: {{tabBar.color}}; background: {{tabBar.backgroundColor}}; {{tabBar.position=='top'? 'top: 0' : 'bottom: 0'}}; {{tabBar.borderStyle? (tabBar.position=='top'? 'border-bottom: solid 1px '+tabBar.borderStyle + ';' : 'border-top: solid 1px '+tabBar.borderStyle + ';') : ''}}">  
  <block wx:for="{{tabBar.list}}" wx:key="pagePath">  
    <navigator url="{{item.pagePath}}" open-type="{{item.pageTum}}" class="menu-item" style="{{item.active? 'color: '+(item.selectedColor? item.selectedColor : tabBar.selectedColor) : ''}}">  
      <image src="{{item.selectedIconPath}}" wx:if="{{item.active}}"></image>  
      <image src="{{item.iconPath}}" wx:if="{{!item.active}}"></image>  
      <text>{{item.text}}</text>  
    </navigator>  
    </block>  
  </view>  
</template>   
```
接下来是在index.js的配置对象：

```
tabBar:{
    "color": "#9E9E9E",
    "selectedColor": "#f00",
    "backgroundColor": "#fff",
    "borderStyle": "#ccc",
    "list":[{
            "pagePath": "/pages/index/index",
            "text": "主页",
            "iconPath": "../../images/index.png",
            "selectedIconPath": "../../images/index_active.png",
            "pageTum": "redirect",
            "selectedColor": "#4EDF80",
            active: true
            },
            {
            "pagePath": "/pages/tum/tum",
            "text": "其他",
            "iconPath": "../../images/pageTum.png",
            "pageTum": "navigate",
            "selectedColor": "#4EDF80",
            active: false
            },
            {
            "pagePath": "/pages/mine/mine",
            "text": "我的",
            "iconPath": "../../images/mine.png",
            "selectedIconPath": "../../images/mine_active.png",
            "pageTum": "redirect",
            "selectedColor": "#4EDF80",
            active: false
            }],
            "position": "bottom"
    }
}
```
在这里要注意的是，active表示该页面是否被选中，pageTum表示点击该页面跳转方式，‘其他’这个页面不用设置tabBar，并且它的pageTum的值是navigate，表示点击‘其他’跳转的页面就不会显示tabBar。

index.wxml引入模板：
```
<import src="../template/tabBar.wxml" />  
<template is="tabBar" data="{{tabBar: tabBar}}" /> 
<text>主页面</text>    //显示内容
```
然后在mine页面也一样配置数据把active的值改为true，引入模板。效果如下：


![](https://user-gold-cdn.xitu.io/2017/12/10/160403c546fba8be?w=280&h=500&f=gif&s=51781)

### 方法二：使用中间页面跳转

使用原生tabBar跳转至一级页面，再利用周期函数onShow的特性直接跳转到我们需要看到的页面，并且在返回时使用wx.swicthTab跳转至程序设计所需的一级页面。下面来看一看实现方法：

首先在app.json中设置tabBar
```
 "tabBar": {
        "color": "#9E9E9E",
        "selectedColor": "#f00",
        "backgroundColor": "#fff",
        "borderStyle": "#ccc",
        "list": [{
                "pagePath": "pages/index/index",
                "text": "主页",
                "iconPath": "images/index.png",
                "selectedIconPath": "images/index_active.png"
            },
            {
                "pagePath": "pages/tum/pageTum",
                "text": "其他",
                "iconPath": "images/pageTum.png"
            },
            {
                "pagePath": "pages/mine/mine",
                "text": "我的",
                "iconPath": "images/mine.png",
                "selectedIconPath": "images/mine_active.png"
            }
        ]
    }
```
    
在‘其他’这个页面中设置跳转页面为一个中间过渡页面pageTum，然后利用pageTum的周期函数onShow跳转至无tabBar的二级页面tum，返回时就能直接返回至主页面，代码如下：

```
    data: {
        num: 0,
    },
    onLoad: function() {},
    onShow: function() {
        this.data.num++;
        if (this.data.num % 2 == 0) {
            wx.switchTab({
                url: '../index/index'
            });
        } else {
            wx.navigateTo({
                url: './tum'
            })
        }
    }
```

实现效果


![](https://user-gold-cdn.xitu.io/2017/12/10/160407d8c025f6b4?w=284&h=507&f=gif&s=33012)


如果有错误或者其他的方法，希望可以指出和交流，谢谢！
    
    



