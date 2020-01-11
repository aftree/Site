---
description: 通常特指API接口的测试
---

# 接口测试

{% hint style="success" %}
* 接口概念
* 接口分类
* 为何要进行接口测试
* 接口文档示例
{% endhint %}

## 接口概念

{% hint style="success" %}
软件不同部分之间的交互接口。通常就是所谓的API――应用程序编程接口，其表现的形式是源代码。
{% endhint %}

我们常说的接口一般指两种： （1）API：应用程序编程接口。程序间的接口 （2）GUI：图形用户界面。人与程序的接口 下文我们所说的接口特指API接口。

API接口定义：对协议进行定义的引用类型。

好多公司开发人员分前后端，他们之间如何配合工作的，就是其中一方定义接口，另一方来调用接口，以实现预期功能。

## 接口分类

{% hint style="success" %}
* HTTP接口 
* WebService接口 
* RESTful接口
{% endhint %}

WebService接口是走soap协议，请求报文和返回报文都是xml格式，通过SoapUI工具进行测试； 

HTTP API接口走HTTP协议，通过路径来区分调用的方法，请求报文入参有多种形式，返回报文一般为json串，最常见的是get和post方法；

```text
GET
POST
PUT
PATCH
DELETE
COPY
HEAD
OPTIONS
LINK
UNLINK
PURGE
LOCK
UNLOCK
PROPFIND
VIEW
```

## 为何要进行接口测试

1、接口测试必要性 当今的系统复杂度不断上升，传统的测试方法成本急剧增加且测试效率大幅下降，所以就要做接口测试。同时，接口测试相对容易实现自动化持续集成，且相对UI自动化也比较稳定，可以减少人工回归测试人力成本与时间，缩短测试周期，支持后端快速发版需求。接口持续集成是为什么能低成本高收益的根源。现在很多系统前后端架构是分离的，从安全层面来说，只依赖前端进行限制已经完全不能满足系统的安全要求（绕过前面实在太容易）， 需要后端同样进行控制，在这种情况下就需要从接口层面进行验证。前后端传输、日志打印等信息是否加密传输也是需要验证的，特别是涉及到用户的隐私信息，如身份证，银行卡等。

2、接口测试原理

模拟客户端向服务器发送请求报文，服务器接收请求报文后对相应的报文做处理并向客户端返回应答，客户端再接收应答的一个过程。

3、接口测试范围

接口的功能、性能、安全性。重点关注数据的交换，传递和控制管理过程，还包括处理的次数。

接口测试对象是接口，但随着系统复杂度越来越高，接口越来越多，完全覆盖是一件很困难的事情。通常情况下主要测试最外层的两类接口：数据进入系统的接口（调用外部系统的参数为本系统使用）、数据流出系统接口（验证系统处理后的数据是否正常）

## 接口文档示例

1、接口文档应该包括哪几部分？

接口说明  
调用的url  
请求方法（get、post）  
请求参数，参数类型、请求参数说明  
返回参数说明  
返回示例

2、示例：APP注册登录

{% api-method method="post" host="https://i.lijun.in" path="/s/login/applogin" %}
{% api-method-summary %}
APP\_User\_Login
{% endapi-method-summary %}

{% api-method-description %}
APP用户注册登录接口
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="deviceid" type="string" required=true %}
 设备号
{% endapi-method-parameter %}

{% api-method-parameter name="channel" type="string" required=true %}
渠道：IOS／安卓
{% endapi-method-parameter %}

{% api-method-parameter name="checkcode" type="string" required=true %}
验证码
{% endapi-method-parameter %}

{% api-method-parameter name="mobile" type="string" required=true %}
手机号
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

![](../../.gitbook/assets/image%20%2815%29.png)

![](../../.gitbook/assets/image%20%281%29.png)

  


