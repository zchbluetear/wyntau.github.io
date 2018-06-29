---
layout: post
title: "RestFul API设计指南"
date: 2018-04-07
description: "RestFul API设计指南"
tag: 代码与设计模式
---

网络应用程序，分为前端和后端两个部分。当前的发展趋势，就是前端设备层出不穷（手机、平板、桌面电脑、其他专用设备......）。

因此，必须有一种统一的机制，方便不同的前端设备与后端进行通信。这导致API构架的流行，甚至出现"API First"的设计思想。RESTful API是目前比较成熟的一套互联网应用程序的API设计理论。我以前写过一篇《理解RESTful架构》，探讨如何理解这个概念。

**一、协议**

　　API与用户的通信协议，总是使用HTTPs协议。

**二、域名**

　　应该尽量将API部署在专用域名之下。

　　https://api.example.com

　　如果确定API很简单，不会有进一步扩展，可以考虑放在主域名下。（https://example.org/api/）

**三、版本（Versioning）**

　　应该将API的版本号放入URL。（https://api.example.com/v1/）

 **四、路径（Endpoint）**

　　路径又称"终点"（endpoint），表示API的具体网址。

　　在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。

　　举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样。

　　* https://api.example.com/v1/zoos<br>
　　* https://api.example.com/v1/animals<br>
　　* https://api.example.com/v1/employees<br>

**五、HTTP动词**

　　对于资源的具体操作类型，由HTTP动词表示。

　　常用的HTTP动词有下面五个（括号里是对应的SQL命令）。

　　1. GET（SELECT）：从服务器取出资源（一项或多项）。<br>
　　2. POST（CREATE）：在服务器新建一个资源。<br>
　　3. PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。<br>
　　4. PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。<br>
　　5. DELETE（DELETE）：从服务器删除资源。<br>
　　
##### 还有两个不常用的HTTP动词。　　　
　　* HEAD：获取资源的元数据。<br>
　　* OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。<br>
　　
　　
##### 下面是一些例子。

　　* GET /zoos：列出所有动物园<br>
　　* POST /zoos：新建一个动物园<br>
　　* GET /zoos/ID：获取某个指定动物园的信息<br>
　　* PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）<br>
　　* PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）<br>
　　* DELETE /zoos/ID：删除某个动物园<br>
　　* GET /zoos/ID/animals：列出某个指定动物园的所有动物<br>
　　* DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物<br>

**六、过滤信息（Filtering）**

　　如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。

　　下面是一些常见的参数。

　　* ?limit=10：指定返回记录的数量<br>
　　* ?offset=10：指定返回记录的开始位置<br>
　　* ?page=2&per_page=100：指定第几页，以及每页的记录数<br>
　　* ?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序<br>
　　* ?animal_type_id=1：指定筛选条件<br>

　　参数的设计允许存在冗余，即允许API路径和URL参数偶尔有重复。比如，GET /zoo/ID/animals 与 GET /animals?zoo_id=ID 的含义是相同的。

**七、状态码（Status Codes)**

　　服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。

　　* 200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。<br>
　　* 201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。<br>
　　* 202 Accepted - [＊]：表示一个请求已经进入后台排队（异步任务）<br>
　　* 204 NO CONTENT - [DELETE]：用户删除数据成功。<br>
　　* 400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。<br>
　　* 401 Unauthorized - [＊]：表示用户没有权限（令牌、用户名、密码错误）。<br>
　　* 403 Forbidden - [＊] 表示用户得到授权（与401错误相对），但是访问是被禁止的。<br>
　　* 404 NOT FOUND - [＊]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。<br>
　　* 406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。<br>
　　* 410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。<br>
　　* 422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。<br>
　　* 500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。<br>


 　　转自: [http://www.ruanyifeng.com/blog/2014/05/restful_api]()

