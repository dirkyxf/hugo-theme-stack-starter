---
title: 我的域名
description: ""
date: 2026-10-10T10:56:39.140Z
preview: ""
draft: false
tags: [Domain]
categories: [Blog]
---

哈，今天太高兴了，买了个域名：`dirkyxf.de`

之前都是用的免费的二级域名，能用是能用，但是总归不大正式。
而且有些DNS会认为是垃圾网站，直接封禁。思来想去，还是搞个自己的一级域名比较好。

现在万事不决问AI，直接找Muse问。

Muse果然强大，小几分钟就把全网价格最低的几个域名都抓了出来。
后缀有.top的，有.link的。

后来我问为啥.us, .uk, .de的那么便宜，啰里八嗦一大堆，不过Muse顺手告诉我找到了全网最低价格的`dirkyxf.de` ，还有优惠码，什么时候转域名商，再续费的时候会更低啥的。

还没确定，又问这几个域名后缀评价如何，Muse答说 .top的便宜但是风评不好，国家结尾的都还可以的。

最后，终于敲定在[Spaceship](https://www.spaceship.com)买, 这家是[Namecheap](https://www.namecheap.com)旗下的域名商。全网价格最低。最后13.25CNY拿下第一年。就算以后默认续费，也只要26块多，还是可以的。

期间吐槽下支付宝，本来想用支付宝直接支付的，结果验证了3次都说有安全问题，支付失败，只能用MasterCard的信用卡直接支付才成功。

域名好了之后，赶紧到Cloudflare上加好域名，回过头来在Spaceship中改nameserver，套一层Cloudflare，安心不少，也方便多了。

把Cloudflare上面的几个Pages挂上新的域名，很快就解析到了。

下一步是把其他server上的服务也通过这个域名解析出来。