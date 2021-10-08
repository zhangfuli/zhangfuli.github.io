---
title: Navicat Premium for Mac12.0破解
date: 2019-10-08 16:18:38
tags:
---



> Navicat系列的数据库客户端非常好用，相信大家很多人用过。Navicat premium更是，让很多款主流数据库一种操作体验，是非常非常棒的。but，navicat的产品并不便宜。最新的Navicat premium12非商业版的都要`2799.3`元，当然我们要支持正版购买使用。本文所探讨的`免费激活`方法只是技术探讨用请勿用作商业行为。

## 第一步：明白这是mac版的激活方法

因为Navicat preminum是基于密钥的方式来激活的。所以第一步当然是要有公钥/私钥对了。可以使用OpenSSL工具来生成，在mac中生成当然非常方便的，如果你并不了解公钥私钥，那么没有关系，我为大家准备了一个公钥/私钥对供大家直接使用。也可以打开该网页在线生成自己的公钥私钥对，这样比较安全。http://sikujiaoyu.com/tools/rsagenerate`（推荐）`
**看到评论区很多人替换rpk文件后还是无法弹出手动激活窗口的问题现在特更新了下本文的公钥私钥对。同时如果大家还存在这个问题可以自行生成公钥私钥对来使用**

#### 公钥为

```
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEApWyHZBc4gF3bZL+01/dh
W337sAewsIsealFiAczKVJebs/u6JkauqfGCsPJT3QJKadKlbW1DbqH3FMpr2LZz
wW9uaWZImOsSRTJpEP2HsUdoDAxgVC/BcWwdp/kzDVlT4RGiMehkEhXeM7b8GjSU
zG4LxjOOfyoC/YPUrhM9YzJyOgWydkIN6WIs8jbe9DOHLmLShjzycKcnXM3/B9iz
+G+UieOoiAJaeDNQu30s8cJYGlIktDj3VPpDlgJHC8QAcLAiyo+erwp728dI/MC/
N+LmwxFRmVksQyesVnuOgBKR2W3cMklllJ18FfpDoopjvd0rn9dXYzMsO5dqOtUa
8QIDAQAB
-----END PUBLIC KEY-----
123456789
```

#### 私钥为

```
-----BEGIN PRIVATE KEY-----
MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQClbIdkFziAXdtk
v7TX92FbffuwB7Cwix5qUWIBzMpUl5uz+7omRq6p8YKw8lPdAkpp0qVtbUNuofcU
ymvYtnPBb25pZkiY6xJFMmkQ/YexR2gMDGBUL8FxbB2n+TMNWVPhEaIx6GQSFd4z
tvwaNJTMbgvGM45/KgL9g9SuEz1jMnI6BbJ2Qg3pYizyNt70M4cuYtKGPPJwpydc
zf8H2LP4b5SJ46iIAlp4M1C7fSzxwlgaUiS0OPdU+kOWAkcLxABwsCLKj56vCnvb
x0j8wL834ubDEVGZWSxDJ6xWe46AEpHZbdwySWWUnXwV+kOiimO93Suf11djMyw7
l2o61RrxAgMBAAECggEBAJ9i31ywBu+f/yimka9YmoSV8XBdKIAhuu+DdGP5lqVE
4m/bRjNk/RufKGYnRmh2sY4euOejVEp/Ydq1Yo4O1Z92JzMEq0Qzkon7lUIalahY
/cZGEnZrAP6wxi43LFpbTDdaTyW5HNpUGaFIWMVDDm+eRFf3CwA5UWJEqCIFRauR
vdZYwPCanTrGtMjSc53oEDEaoyMgnoGXr65OruqYr+uY+rEosZ3M2giY0kt4Ik+E
hquIqT2T+X0sVyacpbQFjlVSSw0HHj76zcdVFX5jOlH96WnzwjSDSm6mnK8Q2cZu
qv9bzKnr0szbuqcK0R+hP1BRNmTBOlJRKLPh/92edgECgYEA0nyxSMlirQG5eG2L
Ry91SWBJWsjwT18eMLzU20qcUirW8TeouitAWOUiuXLlJoqapAq/Ab14+9hqU2Rq
1bhCeeVS2Cmh4zu+4ZLCHG8v/Zs4fmQnm/cM93HRhKfgVy+/VgtMBZXtDdeXybOc
B+yB+KieLM7Wv9Kx6+qK2H4TZZECgYEAyTFo/p6h0iHKuKnbr4IcOHT7Sqh7c621
aHUo56KX8XP6uzUwTVAWMQS8aB/X/xtb8cwf1DwyY/lPOn2PK8ELVlQpEYAJWy4L
xmLG39vHipaH4HOVmvTc2t5IA0LCaIWjvUlxwgZe+ZofNBokLU8SaHECh1tMpMyR
w4VwLEV2L2ECgYEAlsSoPCm8G45TqpZUoD23NkLY6EVsFH5eYqyvjxAnXpe+9HNY
0Vkvsz0VnV5WE0BOulfUL0vngAWpS2hvOfzM6QFBUQKpKdnexTbZAYMHDhID6kyV
LptMV2XYnLue7vSNifV3k7yrWzHlUJ3tkqNvCYzGF/RkUGx78y9CGwZboHECgYBq
XQPL8GNOauz4WVw52gg/VKDxJEc3rbMFCUNZyhyX2p/ITuM9TESfH4jXZ1ZSmM0v
9KEzG6vsLIZVPsHs+L6cohugE9dea+ZvuBK5kEBapSAqahDCfgcwcmkRyD5s8ZHR
5T0NvT6CqJcsfVF43p+1tWEH3B2V1kyNWEMoNIS5oQKBgHQF5UlTzTVp3CY4YNmh
589AbKN6sbj+rjKGSUWtdfbIj00nx6WAm+KVdEoaPJDGQjyN1kTgdMufL3aQkcQO
HW2z19Qe1Z6PM+fOmADL6acsIMsN4Aurh27Rw2yGapZYbEfoTy4HYx6CGh31Pskf
2L8MQ1us28VdkytDAAPZnWuW
-----END PRIVATE KEY-----
```

## 第二步：下载安装程序，按照自己的需要选择版本

当前有两个版本的。分别为：

[64位中文版：https://pan.baidu.com/s/1GaV5pbvHLoOjulNCsGexoA 密码:ads2](https://pan.baidu.com/s/1GaV5pbvHLoOjulNCsGexoA)

[64位英文版：https://pan.baidu.com/s/1zK3BLogC-Ey_PqWHSqN-Dg 密码:a5l4](https://pan.baidu.com/s/1zK3BLogC-Ey_PqWHSqN-Dg)



## 第三步：安装程序

安装比较简单，直接打开.dmg文件然后选择同意，最后将程序拖到Application中如下操作
![同意使用协议](https://img-blog.csdn.net/20180403193111348?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hheWl4aWE2MDY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
`必须选择Agree不然就没有下文了哦`

![按照提示拖动到Applications](https://img-blog.csdn.net/20180403193230241?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hheWl4aWE2MDY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
`这个过程其实就是mac下常用的安装了没什么可说的`

#### `说明下：安装完后别急着打开软件`

## 第四步：替换公钥，之前的公钥开始要用了

![这里写图片描述](https://img-blog.csdn.net/20180403193724228?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hheWl4aWE2MDY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
找到电脑的->应用程序。在Navicat premium上右键，选择“显示包内容”。
![这里写图片描述](https://img-blog.csdn.net/20180403194109858?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hheWl4aWE2MDY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
然后找到如下路径"/Applications/Navicat Premium.app/Contents/Resources"在其中有一名为`rpk`的文件。用你本地的编辑工具打开。用你刚刚生成的或者我在第一步中提供的公钥替换rpk中的公钥。

## `第五步：断网!!!!非常重要`

## 第六步：打开软件，手动激活获取请求码

打开软件并选择“注册”会有一个输入序列号激活的界面
![这里写图片描述](https://img-blog.csdn.net/20180403194528612?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hheWl4aWE2MDY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

输入序列号。序列号为：

`中文版64位密钥序列号： NAVH-T4PX-WT8W-QBL5`
`英文版64位密钥序列号： NAVG-UJ8Z-EVAP-JAUW`
输入后点击“激活"因为我们之前断了网所以会有一个手动激活的提示。
![这里写图片描述](https://img-blog.csdn.net/20180403194719598?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hheWl4aWE2MDY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

点击手动激活会出现如下的界面：
![这里写图片描述](https://img-blog.csdn.net/20180403194758735?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hheWl4aWE2MDY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
在这儿激活码是需要我们填写的。

## 第七步：获取激活码之解密请求码

解密请求码需要我们上边所提到的私钥。[解密的地址为：http://sikujiaoyu.com/tools/rsaprivatedecrypt](http://sikujiaoyu.com/tools/rsaprivatedecrypt)使用该工具出现任何问题[请点击去反馈，通常很快会有回复。http://sikujiaoyu.com/tools/sendmail](http://sikujiaoyu.com/tools/sendmail)
![http://sikujiaoyu.com/tools/rsaprivatedecrypt](https://img-blog.csdnimg.cn/20190523001452736.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9tYXJzd2lsbC5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)
**如果解密后内容为空请注意密钥和请求码是否复制正确，注意不要手动更改请求码和密钥的格式。（最好是用我提供的地址自己生成公钥密钥对 ）**

解密后的内容为:

```
{
  "K" : "NAVHT4PXWT8WQBL5",
  "P" : "Mac 10.13",
  "DI" : "ODQ2Yjg2ZDBjMTEzMjhh"
}
```

将你的私钥和软件界面的请求码分别复制后填写到相应的位置并且点击”RAS私钥解密“就会得到如下的请求码的明文。

## 第八步：获取激活码之自己加密激活码

通过第七步已经获取了请求码的明文，然后需要自己加密一个激活码。也就是使用私钥将激活码的明文加密成密文。[在线加密地址为：http://sikujiaoyu.com/tools/rsaencryptbyprivatekey](http://sikujiaoyu.com/tools/rsaencryptbyprivatekey)
![http://sikujiaoyu.com/tools/rsaencryptbyprivatekey](https://img-blog.csdnimg.cn/20190523001640167.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9tYXJzd2lsbC5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)

`关于要加密的明文这儿坑很多`,首先明文的格式为

```
{"K":"NAVHT4PXWT8WQBL5", "N":"marswill", "O":"weiyongqiang.com", "DI":"ODQ2Yjg2ZDBjMTEzMjhh", "T":1516939200}
其中的K DI为我们第七步解密的内容，而N和O可以自己随便填。但是特别注意的一点是T，T时一个Linux时间戳，必须小于第六步提醒的请在xxxx年xx月xx日之前激活中的这个时间点。例如提醒你必须在2018年04月20日前激活那么你生成的T就不能是4月20日之后的时间戳。不然或激活失败。
```

## 第九步：界面欣赏

经历了以上的八步后相信按照我的步骤的同学大部分已经激活了那让我们看看他的界面。
![这里写图片描述](https://img-blog.csdn.net/20180403200153825?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hheWl4aWE2MDY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看出12比11漂亮多了，重点是有了很多易用而且实用的功能。

**为了防止丢失，这里作为保存，转载于https://blog.csdn.net/marswill/article/details/79808416**