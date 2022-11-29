# go-micro
Golang 微服务开发。做这个是为了和 Java Spring Cloud 对比下。

这里选用的是Kratos框架。

看好多开源项目喜欢用商城作为DEMO场景，这里也用商城做案例。

项目中业务代码（CRUD）参考自其他开源项目。



服务划分：

+ mall-interface

+ 子业务模块(service)
  + mall-service-user 用户服务 
  + mall-service-goods 商品服务
  + mall-service-order 订单服务
  + mall-service-cart 购物车服务

+ mall-admin

> 微服务中的 app 服务类型主要分为5类：interface、service、job、admin、task。



系统架构：

