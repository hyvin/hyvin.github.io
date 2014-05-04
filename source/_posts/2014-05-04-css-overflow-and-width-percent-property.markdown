---
layout: post
title: "CSS overflow and width(%) 如何搭配工作"
date: 2014-05-04 20:46:19 +0300
comments: true
categories: css
---



今天看到一个用css3写的[图片播放器]，之前在面试中被问到过类似问题(不过是问有没有用js写过组建，比如图片播放器)，决定仔细研究一下。不知道是不是很多人写css都有种在“调试”的赶脚，就好像--->

![image](http://weknowmemes.com/wp-content/uploads/2013/03/programmer-problems.jpg)

                                               
<br>
<br>
回正题。
[代码]看完后我的疑问主要在这段：

```
#slider {
  max-width: 300px;  
  text-align: center;
  margin: 0 auto;
}

#overflow {
  width: 100%;
  overflow: hidden;  
}

#slides .inner {
  width: 400%; 
}
#slides article {
   width: 25%; 
  float: left;
}
```


**问题1：**

400% 这个写法什么意思？

**问题2：**

如果把 `overflo:hidden` 去掉，则变成了下图，这个很好理解。

![image](http://i.stack.imgur.com/PngT9.png)


但是如果保留overflow:hidden 去掉两个width属性，则所有图片变成1个column。明明overflow是hidden为什么还是显示了4张图？为什么4张图不再像上图一样是一排？

```
#overflow {
  width: 100%;
  overflow: hidden;
}

#slides .inner {
  /* width: 400%; */
}
#slides article {
  /* width: 25%; */
  float: left;
}
```
![image](http://i.stack.imgur.com/E3x4q.png)

**问题3：**

这个25%有什么用？如果其他不变，这个comment掉好像没什么影响。
```
#slides article {
   width: 25%; 
  float: left;
}
```
<br>

**解答：**

**问题1：**

400%表示inner的宽度是它的parent的4倍，也就是4 × 300px，但是由于overflow是hidden 所以真正active的部分只有300px，所以只显示一张图。

**问题2：**

先回答“明明overflow是hidden为什么还是显示了4张图？”
虽然是overflow为hidden，但是由于``#slider``只设定了宽度，没有设定高度，所以图片在宽度不够的时候会自然往下排。但在这个情况下，即便设置高度也没有意义，因为``#slider``的子元素``#slides article ``设置了``float``，这会导致parent的高度collapse。

comment掉``width:400%``
```
#slides .inner {
  /* width: 400%; */
}
```
那么一个图片实际的container变回``width：300px``而不是之前的``1200px``.
又comment掉图片的``width：25%``
```
#slides article {
  /* width: 25%; */
  float: left;
}
```
那么一个``article``将不会占parent容器的1/4宽度，而会是其包含的图片的实际宽度300px。又由于`float：left`，则后面的图片会被放依次排到图片1的下面。


**问题3：**
这个25%去掉没变化
```
#slides article {
  width: 25%; 
  float: left;
}
```
是因为``#slider``的宽度恰好等于图片的宽度，如果``#slider``变为400px，那么图片2会有一部分显示出来。这个25%的用处就是无论``#slider``的宽度是多少，包含图片的div永远等于slider的宽度，又因为image被center在div，所以看上active部分只会显示一张图片。
[图片播放器]:http://jsbin.com/hivunuga/1
[代码]:http://jsbin.com/hivunuga/1
