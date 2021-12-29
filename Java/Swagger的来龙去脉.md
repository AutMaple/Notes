# Swagger 的来龙去脉

目前来说，在 Java 领域使用 Springboot 构建微服务是比较流行的，在构建微服务时，我们大多数会选择暴漏一个 REST API 以供调用。又或者公司采用前后端分离的开发模式，让前端和后端的工作由完全不同的工程师进行开发完成。不管是微服务还是这种前后端分离开发，维持一份完整的及时更新的 REST API 文档，会极大的提高我们的工作效率。而传统的文档更新方式(如手动编写)，很难保证文档的及时性，经常会年久失修，失去应有的意义。因此选择一种新的 API 文档维护方式很有必要，这也是这篇文章要介绍的内容。

## OpenAPI 规范介绍

OpenAPI Specification 简称 OAS，中文也称 OpenAPI 描述规范，使用 OpenAPI 文件可以描述整个 API，它制定了一套的适合通用的与语言无关的 REST API 描述规范，如 API 路径规范、请求方法规范、请求参数规范、返回格式规范等各种相关信息，使人类和计算机都可以不需要访问源代码就可以理解和使用服务的功能。

下面是 OpenAPI 规范中建议的 API 设计规范，基本路径设计规范。
对于传参的设计也有规范，可以像下面这样：

路径参数 , 例如 /users/{id}

查询参数 , 例如 /users?role=未读代码

header 参数 , 例如 X-MyHeader: Value

cookie 参数 , 例如 Cookie: debug=0; csrftoken=BUSe35dohU3O1MZvDCU

OpenAPI 规范的东西远远不止这些，目前 OpenAPI 规范最新版本是 3.0.2，如果你想了解更多的 OpenAPI 规范，可以访问下面的链接。

[OpenAPI Specification](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md)

## Swagger 介绍

很多人都以为 Swagger 只是一个接口文档生成框架，其实并不是。 Swagger 是一个围绕着 OpenAPI Specification (OAS，中文也称 OpenAPI规范)构建的一组开源工具。可以帮助你从 API 的设计到 API 文档的输出再到 API 的测试，直至最后的 API 部署等整个 API 的开发周期提供相应的解决方案，是一个庞大的项目。 Swagger 不仅免费，而且开源，不管你是企业用户还是个人玩家，都可以使用 Swagger 提供的方案构建令人惊艳的 REST API 。

Swagger 有几个主要的产品。

- Swagger Editor – 一个基于浏览器的 Open API 规范编辑器。

- Swagger UI – 一个将 OpenAPI 规范呈现为可交互在线文档的工具。

- Swagger Codegen – 一个根据 OpenAPI 生成调用代码的工具。

如果想了解更多信息，可以访问 Swagger 官方网站 https://swagger.io 。

## Springfox 介绍

由于 Java 中 Spring 框架的流行，让一个叫做 Marrty Pitt 的老外有了为 SpringMVC 添加接口描述的想法，因此他创建了一个遵守 OpenAPI 规范(OAS)的项目，取名为 swagger-springmvc，这个项目可以让 Spring 项目自动生成 JSON 格式的 OpenAPI 文档。这个框架也仿照了 Spring 项目的开发习惯，使用注解来进行信息配置。

后来这个项目发展成为 Springfox ，再后来扩展出 springfox-swagger2 ，为了让 JSON 格式的 API 文档更好的呈现，又出现了 **springfox-swagger-ui** 用来展示和测试生成的 OpenAPI 。这里的 springfox-swagger-ui 其实就是上面介绍的 Swagger-ui，只是它被通过 webjar 的方式打包到 jar 包内，并通过 maven 的方式引入进来。

Springfox-swagger2 也是通过注解进行信息配置的, 具体配置的使用参考官方文档 [Springfox Reference Documentation](http://springfox.github.io/springfox/docs/current/)

