## 晨昏线简介

基于koa2开发的博客管理系统，这是一个纯后端接口的项目，接口遵循RESTful风格。

完整的接口文档: [接口文档](./doc/API.md)

## 使用晨昏线

**! 使用时注意替换 /src/app/key 中的公钥与私钥文件，文件命名保持一致**

配置 **.env** 文件

| 参数           | 说明             |
| -------------- | ---------------- |
| APP_PORT       | 服务器使用的端口 |
| MYSQL_HOST     | MySQL数据库地址  |
| MYSQL_PORT     | MySQL数据库端口  |
| MYSQL_DATABASE | 使用的数据库名   |
| MYSQL_USER     | 数据库登录名     |
| MYSQL_PASSWORD | 数据库登录密码   |

```
APP_PORT = 8080

MYSQL_HOST = 127.0.0.1
MYSQL_PORT = 3306
MYSQL_DATABASE = terminator
MYSQL_USER = root
MYSQL_PASSWORD = root
```

启动服务，在项目根目录使用以下命名
```shell
npm start
```
*晨昏线在第一次使用时会自动创建数据库表。另外不要忘记使用时先创建你的第一个用户，晨昏线会自动将他标记为超级用户，详细机制可查看下面的晨昏线概述*

## 概述
晨昏线主要由用户管理，文章管理，评论管理，标签管理，分类管理，文件管理6部分组成。

#### 数据库结构
用户表:

| 说明         | 字段        | 类型         | 是否允许为空 | 默认值            | 备注                     |
| ------------ | ----------- | ------------ | ------------ | ----------------- | ------------------------ |
| 用户id       | id          | INT          | 否           | 无                | 自增                     |
| 用户昵称     | username    | VARCHAR(30)  | 否           | 无                |                          |
| 用户邮箱     | email       | VARCHAR(30)  | 否           | 无                | 用于登录                 |
| 登录密码     | password    | VARCHAR(50)  | 否           | 无                | 用于登录                 |
| 个人简介     | bio         | VARCHAR(200) | 否           | 无                |                          |
| 权限等级     | authority   | TINYINT      | 否           | 1                 | 0为超级用户，1为普通用户 |
| 创建时间     | create_time | TIMESTAMP    | 否           | CURRENT_TIMESTAMP | 默认数据库自动处理       |
| 上次修改时间 | update_time | TIMESTAMP    | 否           | CURRENT_TIMESTAMP | 默认数据库自动处理       |

文章表:

| 说明           | 字段        | 类型         | 是否允许为空 | 默认值            | 备注               |
| -------------- | ----------- | ------------ | ------------ | ----------------- | ------------------ |
| 文章id         | id          | INT          | 否           | 无                | 自增               |
| 作者id         | user_id     | INT          | 否           | 无                |                    |
| 标题           | title       | VARCHAR(100) | 否           | 无                |                    |
| 摘要           | password    | VARCHAR(50)  | 是           | 无                |                    |
| 正文           | body        | TEXT         | 是           | 无                |                    |
| 优先级         | priority    | INT          | 否           | 10                | 数字越小优先级越高 |
| 是否显示       | is_show     | TINYINT      | 否           | 1                 | 1为true，0为false  |
| 是否在回收站中 | is_delete   | TINYINT      | 否           | 0                 | 1为true，0为false  |
| 创建时间       | create_time | TIMESTAMP    | 否           | CURRENT_TIMESTAMP | 默认数据库自动处理 |
| 上次修改时间   | update_time | TIMESTAMP    | 否           | CURRENT_TIMESTAMP | 默认数据库自动处理 |

评论表:

| 说明       | 字段        | 类型         | 是否允许为空 | 默认值            | 备注               |
| ---------- | ----------- | ------------ | ------------ | ----------------- | ------------------ |
| 评论id     | id          | INT          | 否           | 无                | 自增               |
| 文章id     | article_id  | INT          | 否           | 无                |                    |
| 评论人邮箱 | email       | VARCHAR(30)  | 否           | 无                |                    |
| 评论内容   | body        | VARCHAR(500) | 否           | 无                |                    |
| 创建时间   | create_time | TIMESTAMP    | 否           | CURRENT_TIMESTAMP | 默认数据库自动处理 |

#### 0. 登录与身份校验
晨昏线使用Token进行用户的身份验证，每次登录成功后都会为你返回一个包含token信息的文档，之后在请求一些需要验证身份的接口例如修改文章时只需要在请求头中附带上你的token信息即可。*详情可以参阅 [接口文档](./doc/API.md)*

#### 1. 用户管理
当你第一次使用晨昏线时，默认添加的第一个用户便会成为超级用户，权限标识为0，拥有管理其他所有用户的权限，且只能有一个超级用户，后面再添加的用户权限标识都为1，代表普通用户。另外，晨昏线并没有开放注册用户的接口，正常情况下只能通过超级用户来添加其他成员。

#### 2. 文章管理
存放文章的数据库表结构有几个字段比较特殊，每篇文章都会有一个is_delete字段，他一般表示内容是否在回收站中的，**0**代表正常状态，**1**代表在回收站中。正常情况下只有在回收站中的文章才能被彻底删除，也就是说如果你想要彻底删除某一篇文章那就必须保证is_delete字段的值为1，否则在调用删除接口时会抛出无法删除的错误，这也是出于一种安全的考量。

#### 3. 评论管理
评论对应各自所属的文章，当这篇文章被删除时，相关的评论也会被全部删除。

#### 4. 标签管理

#### 5. 分类管理

#### 6. 文件管理
