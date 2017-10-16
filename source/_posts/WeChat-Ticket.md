---
title: WeChat Ticket
date: 2017-10-15 16:21:10
tags: "软件工程3"
categories: "编程"
---

简介
===
微信抢票开发实战总结
<!-- more -->
初识微信
===
拿到一个项目之后，我们首先要对该项目进行初步的了解。
这一部分最麻烦的地方就是部署环境
部署环境
---
我在部署环境这一步中主要参考了黄超同学的教程[MAGICIAN'S HAT](https://blog.magichc7.com)。一开始的时候我是在本地配置的环境，结果遇到了许多奇奇怪怪的问题，后来才知道是要在服务器端进行。
登录服务器端的命令如下：
```
ssh ubuntu@ip
```
进入服务器端后，只需按照教程上的步骤来做，很容易就可以配置完成。
熟悉代码
---
配置完环境之后，我花了一段时间来熟悉源代码，了解了各部分的架构和功能。
计算器功能
---
添加一个WeChatHandler类
check：用正则表达式判断输入是否只包含0123456789+-*/()这些字符
```
def is_formula(self):
    flag = r'^[0123456789\+\-\*/()]+$'
    return self.is_msg_type('text') and re.search(flag, self.input['Content'])
```
handle：用python的eval函数来进行计算，如果没有捕捉到错误，则弹出结果，否则弹出'表达式不合法，请重新输入...'
```
try:
    eval(self.input['Content'])
except:
    return self.reply_text('表达式不合法，请重新输入...')
else:
    return self.reply_text(eval(self.input['Content']))
```
用户绑定
---
实现/api/u/user/bind接口
实现方法如下：利用对[https://id.tsinghua.edu.cn/security_check](https://id.tsinghua.edu.cn/security_check)发送post请求来查看其返回结果，对结果进行解析，从而判断校园网账号密码是否有效
```
url = "https://id.tsinghua.edu.cn/security_check"
    data = {'username': self.input['student_id'], 'password': self.input['password']}
    response = requests.post(url, data = data)
    if "认证失败" in response.text:
        raise ValidateError('认证失败')
```
实战项目
===
接下来就是真正完成这个大项目了。
十一假期结束后，我才真正开始了自己的工作。
参照所给的两篇文档，我明确了自己的工作。可以分成两个大块，一是完成所要求的前后端接口，二是添加微信接口
前后端接口
---
这一部分可以参照着原有的用户登录接口照猫画虎，添加新类，连接路由。文档中也明确给出了每一个接口所要做的事情，所以并不是很难。
因此我想重点说一下遇到的问题
返回时间参数时必须要利用**time.mktime**函数将数据库中的存储的时间转换为时间戳格式再返回
上传图像：需要在服务器端的特定位置保存管理员上传的图片，并记录图像url
微信接口
---
这一部分主要是新建并添加一些**WeChatHandler**类，定义各类的**check**、**handle**方法。
而在实现的过程中要从用户角度出发，尽量使用户使用起来更加便捷
包括**抢啥**、**抢票**、**取票**、**退票**。
这些处理的限制条件很多，需要考虑周全
测试
===
功能测试主要靠手动完成，考虑各种情形、特别是特殊情况和极端情况。
单元测试是仿照助教给出的例子来写。而具体原理我现在还不是很明白，还有待继续学习。
尾声
===
本次作业是在模拟接手一个项目，然后在前人的基础上继续完成整个项目。
它锻炼了我们的自主学习代码、阅读文档的能力，也为接下来的大作业打下了基础。
测试号二维码
===
![](二维码.jpeg)