# RESTful API Legacy

> 此文档主要介绍 TuGrpah 的 Rest API 的调用详情。

## 1.简介

TuGraph 提供遵从 REST 规范的 HTTP API，以供开发者通过 HTTP 请求远程调用 TuGraph 提供的服务。

本文档描述 TuGraph 的 HTTP API 使用方式。

**注意：除"登陆"、"查询"和"存储过程"外，其余接口自 **2023年4月30日** 起将不再提供支持，统一使用Cypher接口提供服务。**

## 2.请求与数据格式

### 2.1请求

TuGraph 支持 HTTP GET/POST/PUT/DELETE 请求。其中：

- GET 请求用于只读请求，如读取点属性，边属性等操作；
- POST 请求用于创建实体，提交 Cypher，以及加载和调用存储过程；
- PUT 请求用于修改已有实体，如修改点属性，边属性等；
- DELETE 请求用于删除已有实体，如删除点，边等。

在高可用模式下，用户可以在请求的报头(request header)中设置 `server_version` 来保证请求的服务器有足够新的数据。
当前的 `server_version` 可以从服务器返回的报头中获取。

### 2.2.数据格式

客户端与服务端数据交互的格式是 JSON。在发送请求时，请将发送数据的请求的报头设置为 `Accept:application/json, Content-Type:application/json`。
例如在创建一个点时，请求报头包含以下内容：

```
    Accept: application/json; charset=UTF-8
    Content-Type: application/json
    server_version: 12
```

### 2.3.返回值

TuGraph 返回的 HTTP 状态码包含以下四种：

- 200 OK: 操作成功
- 307 Temporary Redirect: 操作被重定向，一般用于高可用模式下，把操作重定向到 master 上
- 400 Bad Request: 输入有误，例如 URI 错误，或者请求中的 JSON 参数错误
- 500 Internal Server Error: 服务器端错误

当操作成功时，返回的 JSON 中包含操作的返回值。当操作重定向时，返回的 HTTP 报头中的 `location` 域包含重定向目的地址。
当发生输入错误或者服务器错误时，返回的 JSON 中包含 `error_message` 域，其内容是错误提示。

在高可用模式下，服务器还会在报头中设置 `server_version`，以告知客户端当前服务器的数据版本号。当客户端在不同的服务器之间切换时，该数据版本号可以保证客户端不会读到错误的历史数据。

### 2.4.URI格式

TuGraph REST API 提供以下功能：Service Root, login, info, label, index, node, relationship, cypher, cpp_plugin, 以及 python_plugin。
各功能使用的 URI 格式如下：

| URI     | 说明                 |
| ------- | -------------------- |
| /web    | web 可视化界面       |
| /cypher | cypher 请求          |
| /acl    | 权限控制             |
| /user   | 用户管理             |
| /login  | 用户登录             |
| /info   | 数据库状态及提示信息 |
| /task   | 任务管理             |
| /db     | 子图操作             |
表格内容描述:
该表格包含两列，分别为“URI”和“说明”。

逐行描述表格中的数据内容：
1. URI "/web" 的说明为 "web 可视化界面"，用于展示网站的可视化内容。
2. URI "/cypher" 的说明为 "cypher 请求"，指用于发送 cypher 查询的接口。
3. URI "/acl" 的说明为 "权限控制"，涉及对系统权限进行管理和控制的功能。
4. URI "/user" 的说明为 "用户管理"，用于处理与用户相关的各种操作。
5. URI "/login" 的说明为 "用户登录"，是用户进入系统的登录界面。
6. URI "/info" 的说明为 "数据库状态及提示信息"，用于显示数据库的当前状态和相关提示信息。
7. URI "/task" 的说明为 "任务管理"，涉及任务的创建、查看和管理功能。
8. URI "/db" 的说明为 "子图操作"，用于对数据子图进行相关操作。

概要总结：
该表格汇总了系统中各个功能模块的URI及其对应的说明，涵盖了可视化界面、查询请求、权限控制、用户管理、登录功能、数据库状态提示、任务管理以及子图操作，为用户理解系统功能提供了清晰的指引。

其中子图操作又分为：

| URI                              | 说明                 |
| -------------------------------- | -------------------- |
| /db                              | 子图的创建，删除     |
| /db/_{graph_name}_/node          | 点操作             |
| /db/_{graph_name}_/relationship  | 边操作               |
| /db/_{graph_name}_/label         | Label 相关操作       |
| /db/_{graph_name}_/index         | 索引相关操作         |
| /db/_{graph_name}_/cypher        | 子图相关 cypher 操作 |
| /db/_{graph_name}_/cpp_plugin    | C++存储过程          |
| /db/_{graph_name}_/python_plugin | Python 存储过程      |
| /db/_{graph_name}_/import        | 在线导入             |
| /db/_{graph_name}_/misc          | 其它操作             |
表格内容描述：

该表格包含两列，分别为“URI”和“说明”。 

每一行的数据内容如下：
1. "/db" - 该URI用于图的创建和删除操作。
2. "/db/_{graph_name}_/node" - 该URI用于执行与节点相关的操作。
3. "/db/_{graph_name}_/relationship" - 该URI用于处理边的相关操作。
4. "/db/_{graph_name}_/label" - 该URI用于与标签相关的操作。
5. "/db/_{graph_name}_/index" - 该URI用于执行索引的相关操作。
6. "/db/_{graph_name}_/cypher" - 该URI用于进行子图相关的Cypher操作。
7. "/db/_{graph_name}_/cpp_plugin" - 该URI用于调用C++存储过程。
8. "/db/_{graph_name}_/python_plugin" - 该URI用于调用Python存储过程。
9. "/db/_{graph_name}_/import" - 该URI用于在线导入操作。
10. "/db/_{graph_name}_/misc" - 该URI用于其它操作。

总体而言，该表格概述了与图数据库相关的不同操作及其对应的URI，涵盖创建、删除、节点和边操作、标签与索引管理、Cypher查询、存储过程调用及数据导入等多种功能。

## 3.登录

TuGraph 提供基于 JWT 的用户认证方式，可以使用 HTTP 或 HTTPS 协议进行传输。系统默认使用 HTTP 协议，如果需要使用 HTTPS，需要在 lgraph.json 配置文件中将 ssl_auth 设为 1。

### 3.1.登录

用户通过用户名和密码发送登录请求。登录成功会收到带有签名的令牌(Json Web Token)和判断是否为默认密码的布尔型变量，客户端储存该令牌，并且用于以后的每次发送请求。如果登录失败会收到“Authentication failed”错误。

- **URI**: `/login`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | user | 用户名 | 字符串 |
  | password | 密码 | 字符串 |

- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | jwt | 令牌 | 字符串 |
  | default_password | 是否为默认密码 | 布尔值 |

**Example request.**

```
    • POST http://localhost:7070/login
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
      "user":"admin",
      "password":"73@TuGraph"
    }
```

**Example response.**

```
    • 200: OK
    Output:
    {
        "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek",
        "default_password": true
    }
```

### 3.2.身份刷新
Token失效后，前端发起刷新token接口，后端验证token合法性。初次登录后，1小时内有效，需刷新使用。即使刷新，24小时后也会强制退出，需要重新登陆。
验证通过，生成新的token；验证失败返回状态码401。

- **URI**: `/refresh`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | Authorization | 令牌 | 字符串 |
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | jwt | 令牌 | 字符串 |


**Example request.**

```
    • POST http://localhost:7070/refresh
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
        "Authorization": "Bearer eyJhbGciOiJIUz32NiIsInR5cCI6IkpXVDJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byj3fYVAH4D88dfTD_zYQ_uAvdizTMek"
    }
```

**Example response.**

```
    • 200: OK
    Output:
    {
        "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek"
    }
```

### 3.3.修改Token有效期
修改Token有效期，需要传输jwt，refresh_time和expire_time三个参数，其中jwt用于校验用户身份，refresh_time和expire_time等于0时，有效期为无期限，超过refresh_time时，需要调用refresh接口获取新的Token;超过expire_time时，需要重新登录。

- **URI**: `/update_token_time`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | Authorization | 令牌 | 字符串 |
  | refresh_time | 有效时间（默认设置为0） | Int64 |
  | expire_time | 有效时间（默认设置为0） | Int64 |

- **RESPONSE**:  如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/update_token_time
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
        "Authorization" : "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJmbWEuYWkiLCJwYXNzd29yZCI6IjczQFR1R3JhcGgiLCJ1c2VyIjoiYWRtaW4ifQ.o_yb5veSJkuy-ieBp4MqTk-tC1grcKotgVbgNJ0TyTU",
        "refresh_time":0,
        "expire_time":0
    }
```

**Example response.**

```
    • 200: OK
```

### 3.4.查询Token有效期
查询Token有效期，需要传输jwt，用于校验用户身份，返回，refresh_time和expire_time，其中refresh_time表示刷新时间，超过时需要调用refresh接口获取新的Token;expire_time表示过期时间，超过时需要重新登录。
- **URI**: `/get_token_time`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | Authorization | 令牌 | 字符串 |

- **RESPONSE**:  如果成功，返回"refresh_time"和"expire_time"。

**Example request.**

```
    • POST http://localhost:7070/get_token_time
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
        "Authorization" : "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJmbWEuYWkiLCJwYXNzd29yZCI6IjczQFR1R3JhcGgiLCJ1c2VyIjoiYWRtaW4ifQ.o_yb5veSJkuy-ieBp4MqTk-tC1grcKotgVbgNJ0TyTU",
    }
```

**Example response.**

```
    • 200: OK
    Output:
    {
        "refresh_time":600,
        "expire_time":3600
    }
```

### 3.5.登出
用户登出，同时删除token。

- **URI**: `/logout`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | Authorization | 令牌 | 字符串 |
- **RESPONSE**:  如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/logout
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
        "Authorization" : "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJmbWEuYWkiLCJwYXNzd29yZCI6IjczQFR1R3JhcGgiLCJ1c2VyIjoiYWRtaW4ifQ.o_yb5veSJkuy-ieBp4MqTk-tC1grcKotgVbgNJ0TyTU"
    }
```

**Example response.**

```
    • 200: OK
```

## 4.查询

URI 格式为

```
    http://{host}:{port}/cypher
```

### 4.1.调用Cypher

- **URI**: `/cypher`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | graph | 数据库 | 字符串 |
  | cypher | 查询语句 | 字符串 |

- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | result | 运行结果 | 列表 |
  | elapsed | 运行时间（秒） | 浮点数 |
  | header | 返回结果的表头 | 列表 |
  | size | 结果数 | 整型 |

其中 header 是一个列表，每一元素格式如下：

| 域名 | 说明                                        | 类型   |
| ---- | ------------------------------------------- | ------ |
| name | 列名                                        | 字符串 |
| type | 列数据类型，0 为标量，1 为点 id，2 为向量 |        |
表格内容描述: 该表格包含三列，分别为“域名”、“说明”和“类型”。

- 第一行数据：域名为“name”，说明为“列名”，类型为“字符串”。
- 第二行数据：域名为“type”，说明为“列数据类型，0 为标量，1 为点 id，2 为向量”，类型字段留空。

总结：该表格详细描述了两个字段的域名、说明及数据类型，旨在提供有关数据结构的信息。其中，第一行定义了列名的类型，第二行则详细说明了列数据类型的可能性。

**Example request.**

```
    • POST http://localhost:7070/cypher
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
        "graph": "default",
        "script": "MATCH (n) RETURN n,n.name LIMIT 10"
    }
```

**Example response.**

```
    • 200: OK
    Output:
    {
        "elapsed": 0.001224517822265625,
        "header": [
            {
                "name": "n",
                "type": 1
            },
            {
                "name": "n.name",
                "type": 0
            }
        ]
        "result": [
            [
                0,
                "Rachel Kempson"
            ],
            [
                1,
                "Michael Redgrave"
            ],
            [
                2,
                "Vanessa Redgrave"
            ]
        ],
        "size": 3
    }
```

### 4.2.调用带参数的 Cypher

Cypher 支持使用参数进行查询。当调用带参数的 Cypher 查询时，TuGraph 会缓存该查询的
执行计划（execution plan），以加速后续同类查询的速度。

- **URI**: `/cypher`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | graph | 数据库 | 字符串 |
  | cypher | 查询语句 | 字符串 |
  | parameters | 参数 | 列表 |

- **RESPONSE**:

与 [调用 Cypher](#%E8%B0%83%E7%94%A8Cypher) 相同。

**Example request.**

```
    • POST http://localhost:7070/db/graph1/cypher
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
      "graph": "default",
      "script": "MATCH (n:Person {name:$param1}) RETURN n.birthyear",
      "parameters": {
        "$param1": "Lindsay Lohan"
      }
    }
```

**Example response.**

```
    • 200: OK
    Output:
    {
        "elapsed": 0.005886077880859375,
        "header": [
            {
                "name": "n.birthyear",
                "type": 0
            }
        ],
        "result": [
            [
                1986
            ]
        ],
        "size": 1
    }
```


## 5.存储过程

URI 格式为

```
    http://{host}:{port}/db/{graph_name}/cpp_plugin|python_plugin
```

### 5.1.加载存储过程

TuGraph 服务启动时，如果 load_plugins 为真，则会自动加载 plugin 目录下的所有 plugin。否则需要手动加载。此外，如果服务器运行过程中，管理员更新了 plugin 文件，也需要手动重新加载。重新加载 plugin 的调用格式为：

- **URI**: `/db/{graph_name}/cpp_plugin|python_plugin`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | name | 插件名称 | 字符串 |
  | description | 插件说明 | 字符串 |
  | code_base64 | 插件代码 | 字符串，使用 base64 编码 |
  | read_only | 是否为只读存储过程 | 布尔值 |
  | code_type | 上传代码的类型，C++类型可选 zip/so/cpp，Python 为 py | 字符串 |

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/db/graph1/cpp_plugin
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
        "name" : "echo",
        "description" : "A test plugin that returns the input",
        "code_base64" : "{base64 encoded echo.zip}",
        "read_only" : true,
        "code_type" : "zip"
    }
```

**Example response.**

```
    • 200: OK
```

### 5.2.列出所有存储过程

- **URI**: `/db/{graph_name}/cpp_plugin|python_plugin`
- **METHOD**: GET
- **RESPONSE**: 存储过程列表，其中每个元素是一个 plugin 的描述，其格式为：
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | name | 存储过程名 | 字符串 |
  | description | 存储过程描述 | 字符串 |
  | read_only | 存储过程是否只读 | 布尔值 |

**Example request.**

```
    • GET http://localhost:7070/db/graph1/cpp_plugin
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    Output:
    {
        [
            {
                "description":"adds a vertex label to the db",
                "name":"add_label",
                "read_only":false
            },
            {
                "description": "scans graph and get number of edges",
                "name": "scan_graph",
                "read_only": true
            }
        ]
    }
```

### 5.3.获取存储过程的详细信息

- **URI**: `/db/{graph_name}/cpp_plugin|python_plugin/{plugin_name}`
- **METHOD**: GET
- **RESPONSE**: 存储过程信息，包括代码，其格式为：
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | name | 存储过程名 | 字符串 |
  | description | 存储过程描述 | 字符串 |
  | read_only | 存储过程是否只读 | 布尔值 |
  | code_base64 | 存储过程的代码 | 字符串，使用 base64 编码 |
  | code_type | 上传代码的类型，C++类型可选 zip/so/cpp，Python 为 py | 字符串 |

**Example request.**

```
    • GET http://localhost:7070/db/graph1/cpp_plugin/echo
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    Output:
    {
        "name" : "echo",
        "description" : "A test plugin that returns the input",
        "code_base64" : "{base64 encoded echo.zip}",
        "read_only" : true,
        "code_type" : "zip"
    }
```

### 5.4.调用存储过程

- **URI**: `/db/{graph_name}/cpp_plugin|python_plugin/{plugin_name}`
- **METHOD**: POST
- **REQUEST**: 字符串输入
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | data | 输入数据 | 字符串 |
  | timeout | 超时长度（秒，可选，缺省值为 0） | 浮点 |
  | in_process | 是否在本进程调用（可选，缺省值为 false） | 布尔值 |

- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | result | 运行结果 | 字符串 |

**Example request.**

```
    • POST http://localhost:7070/db/graph1/python_plugin/echo
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
        data : "Hello!\n你好！\nKonichiwa!",
        timeout : 0,
        in_process : true
    }
```

**Example response.**

```
    • 200: OK
    Output:
    {
      "result": "Hello!\n你好！\nKonichiwa!"
    }
```

### 5.5.删除存储过程

- **URI**: `/db/{graph_name}/cpp_plugin|python_plugin/{plugin_name}`
- **METHOD**: DELETE
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • DELETE http://localhost:7070/db/graph1/cpp_plugin/example_plugin
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
```

## 6.Deprecated

以下接口将在4/30/2023之后被删除。

### 6.1.用户管理

系统默认创建一个管理员，管理员用户名为 _admin_，密码为 _73@TuGraph_。为了安全起见，请用户在第一次启动服务器后更改密码。


#### 6.1.1.添加用户

添加一个新的用户，并为其设置初始密码。只有管理员有权限进行此操作。其中用户名只能由字母，数字以及下划线构成，密码则可以包含任意字符。用户名和密码长度不能超过 64 字节。添加用户时还可以为用户增加一个描述，用户描述可以包含任意字符，最长不超过 512 字节。

新用户默认拥有同名的角色，不具备任何图的权限。

- **URI**: `/user`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | user | 用户名 | 字符串 |
  | password | 密码 | 字符串 |
  | description | 用户描述 | 字符串 |

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/user
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
    Input:
    {
        "user": "USER1",
        "password": "AN_INITIAL_PASSWORD",
        "description": "This is a user"
    }
```

**Example response.**

```
    • 200: OK
```

#### 6.1.2.列出所有用户

列出数据库的所有用户。只有管理员拥有该操作权限。

- **URI**: `/user/`
- **METHOD**: GET
- **RESPONSE**: 所有用户及其信息。

**Example request.**

```
    • GET http://localhost:7070/user
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
• 200: OK
Output:
{
    "admin": {
        "disabled": false,
        "description": "Builtin admin user",
        "roles": ["admin"]
    },
    "guest1": {
        "disabled": true,
        "description": "",
        "roles": ["guest1", "some_other_role"]
    }
}
```

#### 6.1.3.获取用户信息

列出给定用户的信息。

- **URI**: `/user/{user_name}`
- **METHOD**: GET
- **RESPONSE**: 用户信息。

**Example request.**

```
    • GET http://localhost:7070/user/guest1
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
• 200: OK
Output:
{
    "disabled": true,
    "description": "A guest user"
    "roles": ["guest1", "some_other_role"]
}
```

#### 6.1.4.列出用户权限

列出给定用户有权限访问的所有图及相应权限。

- **URI**: `/user/{user_name}/graph`
- **METHOD**: GET
- **RESPONSE**: 用户信息。

**Example request.**

```
    • GET http://localhost:7070/user/guest1/graph
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
• 200: OK
Output:
{
    "graph1" : "FULL",
    "graph2" : "READ"
}
```

#### 6.1.5.更改用户密码

用户可以更改自己的密码，更改密码时需要同时提供原密码。管理员可以更改所有用户的密码。管理员更改其它用户密码时，可以不提供当前密码。

- **URI**: `/user/{user_name}/password`
- **METHOD**: PUT
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | current_password | 当前密码 | 字符串 |
  | new_password | 新密码 | 字符串 |

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/user/user1/password
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
    Input:
    {
        "current_password": "THE_CURRENT_PASSWORD"
        "new_password": "A_NEW_PASSWORD"
    }
```

**Example response.**

```
    • 200: OK
```

#### 6.1.6.修改用户描述

用户可以修改自己的描述。管理员可以修改任意用户的描述。

- **URI**: `/user/{user_name}/description`
- **METHOD**: PUT
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | description | 用户描述 | 字符串 |

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/user/user1/description
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
    Input:
    {
        "description": "New description for this user."
    }
```

**Example response.**

```
    • 200: OK
```

#### 6.1.7.删除用户

删除用户及其所有相关权限，只有管理员拥有该操作权限。

- **URI**: `/user/{user_name}`
- **METHOD**: DELETE
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • DELETE http://localhost:7070/user/guest1
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
    • 200: OK
```

#### 6.1.8.禁用用户

用户可以被禁用。被禁用的用户将不能登陆，但是其资料仍然保存。被禁用的用户可以被重新启用。

- **URI**: `/user/{user_name}/disable`
- **METHOD**: POST
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/user/guest1/disable
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
    • 200: OK
```

#### 6.1.9.启用用户

启用一个被禁用的用户。

- **URI**: `/user/{user_name}/enable`
- **METHOD**: POST
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/user/guest1/enable
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
    • 200: OK
```

#### 6.1.10.设置用户角色

为指定用户设置角色。只有管理员可以执行此操作。

用户角色列表必须是“全量列表”，即该列表需要包含该用户需要的所有角色。唯一的例外是用户的同名角色，即使列表中不含该角色，它也会被加到用户角色中。

- **URI**: `/user/{user_name}/role`
- **METHOD**: POST
- **REQUEST**: 角色列表
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/user/guest1
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
    Input:
        ["role1", "role2"]
```

**Example response.**

```
    • 200: OK
```

此时用户`guest1`拥有角色`guest1`, `role1`和`role2`。

### 6.2.角色管理

TuGraph 使用基于角色的权限管理。

同一用户可以拥有多个角色。新用户默认拥有与其同名的角色。删除用户时，同名角色也会被删除。如果新建用户时同名角色已经存在，则创建失败。

同一角色可以对多个图有不同的权限。用户对某张图的权限由其所有角色对该图的最高权限决定。

TuGraph 使用四级权限，不用的用户对不同的子图可以有不同的权限，四种权限及其说明如下：

| 权限  | 说明                                                                             |
| ----- | -------------------------------------------------------------------------------- |
| NONE  | 无权限                                                                           |
| READ  | 只读                                                                             |
| WRITE | 可读写子图中的点和边                                                           |
| FULL  | 完全权限，包括更改元数据（label, index），管理存储过程，以及删除子图中的所有数据 |
表格内容描述: 

该表格包含两列，分别是“权限”和“说明”。 

- 第一行：权限为“NONE”，说明为“无权限”，表示用户没有任何访问权限。
- 第二行：权限为“READ”，说明为“只读”，表示用户只能查看数据，无法进行修改。
- 第三行：权限为“WRITE”，说明为“可读写子图中的点和边”，表示用户可以对子图中的点和边进行读写操作。
- 第四行：权限为“FULL”，说明为“完全权限，包括更改元数据（label, index），管理存储过程，以及删除子图中的所有数据”，表示用户具有全面的访问权限，能够进行所有操作。

总体而言，该表格概述了不同权限级别及其对应的功能和限制，从无权限到完全权限，清晰地定义了用户在系统中可以Perform的操作。

管理员对所有子图都有完全权限，新建的用户对所有子图都没有权限。将用户加入管理员角色中可以将用户提升为管理员。

#### 6.2.1.添加角色

添加一个新的角色，并设置其描述。只有管理员有权限进行此操作。

角色名只能由字母，数字以及下划线构成，密码则可以包含任意字符。角色名长度不能超过 64 字节。

角色描述可以是任意字符串，长度不超过 512 字节。

- **URI**: `/role`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | role | 角色名 | 字符串 |
  | description | 角色描述 | 字符串 |

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/role
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
    Input:
    {
        "role": "new_role",
        "description": "This is a new role.",
    }
```

**Example response.**

```
    • 200: OK
```

#### 6.2.2.修改角色描述

修改角色的描述。只有管理员有权限进行此操作。角色描述可以是任意字符串，长度不超过 512 字节。

- **URI**: `/role/{role_name}/description`
- **METHOD**: PUT
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | description | 新描述 | 字符串 |

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/role/role1/description
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
    Input:
    {
        "description": "modified description"
    }
```

**Example response.**

```
    • 200: OK
```

#### 6.2.3.列出所有角色

列出数据库的所有角色。只有管理员拥有该操作权限。

- **URI**: `/role/`
- **METHOD**: GET
- **RESPONSE**: 所有角色及其信息。

**Example request.**

```
    • GET http://localhost:7070/role
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
• 200: OK
Output:
{
    "admin": {
        "disabled": false,
        "description": "Builtin administrator group.",
        "permissions": {"default":"FULL", "graph1":"FULL"}
    },
    "role1": {
        "disabled": true,
        "description": "Another role",
        "permissions": {"default":"READ"}
    }
}
```

#### 6.2.4.获取角色信息

列出给定角色的信息。

- **URI**: `/role/{role_name}`
- **METHOD**: GET
- **RESPONSE**: 角色信息。

**Example request.**

```
    • GET http://localhost:7070/role/role1
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
• 200: OK
Output:
{
    "disabled": true,
    "description": "Another role",
    "permissions": {"default":"READ"}
}
```

#### 6.2.5.删除角色

删除指定角色，只有管理员拥有该操作权限。

- **URI**: `/role/{role_name}`
- **METHOD**: DELETE
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • DELETE http://localhost:7070/role/role1
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
    • 200: OK
```

#### 6.2.6.禁用角色

角色可以被禁用。角色被禁用后，具有该角色的用户将不再从该角色中获得任何权限。只有管理员可以执行此操作。

- **URI**: `/role/{role_name}/disable`
- **METHOD**: POST
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/role/role1/disable
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
    • 200: OK
```

#### 6.2.7.启用角色

启用一个被禁用的角色。

- **URI**: `/role/{role_name}/enable`
- **METHOD**: POST
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/role/role1/enable
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
```

**Example response.**

```
    • 200: OK
```

#### 6.2.8.设置角色权限

为指定角色设置权限。只有管理员可以执行此操作。

角色权限列表必须是“全量列表”，即该列表需要包含该角色能操作的所有图及其权限。

- **URI**: `/role/{role_name}/permissions`
- **METHOD**: POST
- **REQUEST**: 图名称及相应权限的字典。
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/role/role1/permissions
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    • Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiaXNzIjoiZm1hLmFpIiwidXNlcl9pZCI6ImFkbWluIn0.SHaqrjKLaI4byjbEYVAH4D88dOTD_zYQ_uAvdizTMek
    Input:
    {
        "graph1" : "FULL",
        "graph2" : "READ"
    }
```

**Example response.**

```
    • 200: OK
```


### 6.3.服务器状态

#### 6.3.1.修改服务器配置

修改服务器配置，配置修改后立即生效，并将影响所有服务器。这些配置的优先级高于配置文件以及命令行参数。

- **URI**: `/config`
- **METHOD**: PUT
- **REQUEST**:

请求为一个字典，使用 `{"opt1":v1}` 可以将名为`opt1`的配置修改为`v1`。

| 配置名               | 说明                   | 值类型 |
| -------------------- | ---------------------- | ------ |
| OPT_DB_ASYNC         | 是否启用异步模式       | 布尔值 |
| OPT_TXN_OPTIMISTIC   | 是否默认使用乐观事务锁 | 布尔值 |
| OPT_AUDIT_LOG_ENABLE | 是否启用审计日志       | 布尔值 |
表格内容描述: 

该表格包含三列，分别为“配置名”、“说明”和“值类型”。

- 第一行数据是“OPT_DB_ASYNC”，其说明为“是否启用异步模式”，值类型为“布尔值”。
- 第二行数据是“OPT_TXN_OPTIMISTIC”，其说明为“是否默认使用乐观事务锁”，值类型为“布尔值”。
- 第三行数据是“OPT_AUDIT_LOG_ENABLE”，其说明为“是否启用审计日志”，值类型为“布尔值”。

总体来看，此表格列出了三项配置选项及其相应的说明和数据类型，这些配置主要涉及数据库的异步处理、事务锁的使用方式及审计日志的启用情况，均为布尔值类型的设置。

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • PUT http://localhost:7070/config
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
        "OPT_DB_ASYNC": true,
        "OPT_AUDIT_LOG_ENABLE": false
    }
```

**Example response.**

```
    • 200: OK
```

#### 6.3.2.当前服务器状态

- **URI**: `/info`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | lgraph_version | 服务器版本号 | 字符串 |
  | git_branch | 服务器代码分支 | 字符串 |
  | git_commit | 服务器代码版本 | 字符串 |
  | web_commit | 前端码版本 | 字符串 |
  | cpp_id | CPP 编译器 ID | 字符串 |
  | cpp_version | CPP 编译器版本 | 字符串 |
  | python_version | PYTHON 版本 | 字符串 |
  | node | 点 uri | 字符串 |
  | relationship | 边 uri | 字符串 |
  | cpu | cpu 信息 | 字典，格式参见[服务器 CPU 状态](#%E6%9C%8D%E5%8A%A1%E5%99%A8CPU%E7%8A%B6%E6%80%81) |
  | disk | 硬盘 IO 信息 | 字典，格式参见[服务器硬盘状态](#%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%A1%AC%E7%9B%98%E7%8A%B6%E6%80%81) |
  | memory | 内存信息 | 字典，格式参见[服务器内存状态](#%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%86%85%E5%AD%98%E7%8A%B6%E6%80%81) |
  | db_space | 图数据库占用空间 | 字典，格式参见[图数据库占用空间](#%E5%9B%BE%E6%95%B0%E6%8D%AE%E5%BA%93%E5%8D%A0%E7%94%A8%E7%A9%BA%E9%97%B4) |
  | db_config | 图数据库配置信息 | 字典，格式参见[图数据库配置信息](#%E5%9B%BE%E6%95%B0%E6%8D%AE%E5%BA%93%E9%85%8D%E7%BD%AE%E4%BF%A1%E6%81%AF) |
  | up_time | 数据库在线时长（秒） | 整型 |

**Example request.**

```
    • GET http://localhost:7070/info
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
      "lgraph_version": "1.2.0",
      "git_branch": "master",
      "git_commit": "9e2977d",
      "web_commit": "1e2823d",
      "cpu_id": "GUN",
      "cpu_version": "4.8.5",
      "python_version": "3.2",
      "node": "/node",
      "relationship": "/relationship",
      "cpu": {
        "self": 25,
        "server": 35,
        "unit": "%"
      },
      "disk": {
        "read": 2000,
        "write": 2000,
        "unit": "B/s"
      },
      "memory": {
        "self": 25016,
        "server_avail": 46865636,
        "server_total": 65860552,
        "unit": "KB"
      },
      "db_space": {
        "space": 57344,
        "unit": "B"
      },
      "db_config": {
        "db_async": false,
        "disable_auth": false,
        "enable_ha": false,
        ...
      },
      "up_time": 3235
    }
```

#### 6.3.3.服务器 CPU 状态

- **URI**: `/info/cpu`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | self | 图数据库应用程序 CPU 使用率 | 整型 |
  | server | 服务器 CPU 使用率 | 整型 |
  | unit | 单位 | 字符串 |

**Example request.**

```
    • GET http://localhost:7070/info/cpu
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
      "self": 25,
      "server": 35,
      "unit": "%"
    }
```

#### 6.3.4.服务器硬盘状态

- **URI**: `/info/disk`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | read | 服务器硬盘读速率 | 整型 |
  | write | 服务器硬盘写速率 | 整型 |
  | unit | 单位 | 字符串 |

**Example request.**

```
    • GET http://localhost:7070/info/disk
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
      "read": 2000,
      "write": 2000,
      "unit": "B/s"
    }
```

#### 6.3.5.服务器内存状态

- **URI**: `/info/memory`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | self | 图数据库应用程序内存使用量 | 整型 |
  | server_avail | 服务器可用内存 | 整型 |
  | server_total | 服务器总内存 | 整型 |
  | unit | 单位 | 字符串 |

**Example request.**

```
    • GET http://localhost:7070/info/memory
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
      "self": 25016,
      "server_avail": 46865636,
      "server_total": 65860552,
      "unit": "KB"
    }
```

#### 6.3.6.图数据库占用空间

- **URI**: `/info/db_space`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | space | 图数据库占用空间 | 整型 |
  | disk_avail | 图数据库可用空间 | 整型 |
  | disk_total | 服务器硬盘总空间 | 整型 |
  | unit | 单位 | 字符串 |

**Example request.**

```
    • GET http://localhost:7070/info/db_space
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
      "disk_avail"::360074579968,
      "disk_total"::984373800960,
      "space": 57344,
      "unit": "B"
    }
```

#### 6.3.7.图数据库配置信息

- **URI**: `/info/db_config`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | db_async | 图数据库工作模式（同步或异步） | 布尔值 |
  | disable_auth | 是否禁用身份验证 | 布尔值 |
  | enable_ha | 是否启用高可用模式 | 布尔值 |
  | enable_rpc | 是否启用 RPC 服务器 | 布尔值 |
  | bind_host | REST 服务器的主机 | 字符串 |
  | enable_audit_log | 是否启用日志审计 | 布尔值 |
  | port | REST 服务器的端口 | 整型 |
  | rpc_port | RPC 服务器的端口 | 整型 |
  | optimistic_txn | 是否默认使用乐观事务锁 | 布尔值 |
  | thread_limit | 图数据库应用程序的可用线程数 | 整型 |
  | enable_ssl | 是否使用 SSL 进行身份验证 | 布尔值 |
  | verbose | 输出的详细程度 | 整型 |

**Example request.**

```
    • GET http://localhost:7070/info/db_config
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
      "db_async":false,
      "disable_auth":false,
      "enable_ha":false,
      "enable_rpc":false,
      "bind_host":"127.0.0.1",
      "enable_audit_log":false,
      "port":7070,
      "optimistic_txn":false,
      "rpc_port":9091,
      "thread_limit":0,
      "enable_ssl":false,
      "verbose":2
    }
```

#### 6.3.8.高可用服务器列表

_(仅在高可用模式下有效)_

- **URI**: `/info/peers`
- **METHOD**: GET
- **RESPONSE**: 如果成功，则返回 200 代码，并返回一个服务器信息列表，其中每个服务器信息格式为：
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | rpc_address | 服务器 RPC 地址 | 字符串 |
  | rest_address | 服务器 REST 地址 | 字符串 |
  | state | 服务器状态 | 字符串 |

其中服务器状态可为 `MASTER`,`SLAVE`,`OFFLINE`。

**Example request.**

```
    • GET http://localhost:7070/info/peers
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
      [
          {
              "rest_address":"192.168.1.22:17071",
              "rpc_address":"192.168.1.22:19091",
              "state":"MASTER"
          },
          {
              "rest_address":"192.168.1.22:17072",
              "rpc_address":"192.168.1.22:19092",
              "state":"SLAVE"
          }
      ]
    }
```

#### 6.3.9.当前 Leader 信息

_(仅在高可用模式下有效)_

- **URI**: `/info/leader`
- **METHOD**: GET
- **RESPONSE**: 如果成功，则返回 200 代码，并返回当前 leader 服务器信息，格式为：
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | rpc_address | 服务器 RPC 地址 | 字符串 |
  | rest_address | 服务器 REST 地址 | 字符串 |

**Example request.**

```
    • GET http://localhost:7070/info/leader
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
      "rest_address":"192.168.1.22:17071",
      "rpc_address":"192.168.1.22:19091"
    }
```

#### 6.3.10.服务器统计信息

- **URI**: `/info/statistics`
- **METHOD**: GET
- **RESPONSE**: 如果成功，则返回 200 代码，并返回当前服务器统计信息，格式为：
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | requests/second | 每秒处理的请求数量 | 浮点型 |
  | writes/second | 每秒处理的写请求数量 | 浮点型 |
  | running_tasks | 正在执行的请求数量 | 整型 |
  | failure_rate | 请求失败率 | 浮点型 |

**Example request.**

```
    • GET http://localhost:7070/info/statistics
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "failure_rate": 2.3,
        "requests/second": 122.3,
        "running_tasks": 10,
        "writes/second": 12.4
    }
```

#### 6.3.11.审计日志信息

- **URI**: `/info/log/?begin_time={begin_time}&end_time={end_time}&user={user}&num_log={num_log}&descending_order={descending_order}`
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | begin_time | 查询日志的起始时间(必填，格式为 YYYY-mm-dd HH:MM:SS) | 时间戳 |
  | end_time | 查询日志的结束时间(默认为当前时间，格式为 YYYY-mm-dd HH:MM:SS) | 时间戳 |
  | user | 查询日志的操作者(管理员可查询所有用户的日志，普通用户只能查询本人日志) | 字符串 |
  | num_log | 查询日志的数量(默认为 100) | 整型 |
  | descending_order | 查询结果是否降序输出(默认为 true) | 布尔值 |

- **METHOD**: GET
- **RESPONSE**: 如果成功，则返回 200 代码，并返回审计日志列表，其中每个元素是一条操作日志，其格式为：
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | index | 该操作的索引值 | 整型 |
  | begin_time | 该操作的开始时间 | 字符串 |
  | end_time | 该操作的结束时间 | 字符串 |
  | user | 该操作的发起者 | 字符串 |
  | graph | 该操作的图 | 字符串 |
  | type | 该操作的类型 | 字符串 |
  | read_write | 该操作为读操作或者写操作 | 字符串 |
  | success | 该操作是否成功 | 布尔值 |
  | content | 该操作的简要内容 | 字符串 |

**Example request.**

```
    • GET http://localhost:7070/info/log/?begin_time=2020-02-17%2015:00:00&end_time=2020-02-20%2012:00:00&user=admin&num_log=100&descending_order=false
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        [
            {
                "begin_time": "2020-02-17 15:27:15",
                "content": "post /login    Successful",
                "end_time": "2020-02-17 15:27:15",
                "graph": "",
                "index": 1,
                "read_write": "read",
                "success": true,
                "type": "Security",
                "user":"admin"
            },
            {
                "begin_time": "2020-02-17 15:27:15",
                "content": "Load plugin : `echo`    Successful",
                "end_time": "2020-02-17 15:27:15",
                "graph": "default",
                "index": 2,
                "read_write": "write",
                "success": true,
                "type": "Plugin",
                "user": "admin"
            },
            ...
        ]
    }
```

### 6.4.任务管理

TuGraph 提供长任务的跟踪和中止功能。用户可以通过 REST API 来查询当前正在运行的在 Cypher 和存储过程查询，并选择中止正在执行的查询。

任务管理对应的 URI 格式为

```
    http://{host}:{port}/task/{thread_id}/{task_id}
```

#### 6.4.1.查询正在执行的任务

- **URI**: `/task`
- **METHOD**: GET
- **RESPONSE**:

返回的 JSON 为一个数组，其中每一个元素格式如下：

| 域名         | 说明                         | 类型   |
| ------------ | ---------------------------- | ------ |
| description  | 任务描述                     | 字符串 |
| time_elapsed | 任务已经执行的时间，单位为秒 | 浮点   |
| task_id      | 任务 ID                      | 字符串 |
表格内容描述: 该表格包含三列，分别为“域名”、“说明”和“类型”。

- 第一行: 域名为“description”，其说明是“任务描述”，数据类型为字符串。
- 第二行: 域名为“time_elapsed”，其说明是“任务已经执行的时间，单位为秒”，数据类型为浮点数。
- 第三行: 域名为“task_id”，其说明是“任务 ID”，数据类型为字符串。

总体来看，该表格定义了与任务相关的三个字段，包括对任务的描述、执行时间及其标识符。

**Example request.**

```
    • GET http://localhost:7070/task
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        [
            {
                "description" : "[CPP_PLUGIN] scan_graph",
                "time_elapsed" : 13.987,
                "task_id" : "3_10"
            },
            {
                "description" : "[CYPHER] MATCH(n) return n",
                "time_elapsed" : 30.887,
                "task_id" : "2_6"
            }
        ]
    }
```

#### 6.4.2.中止任务

- **URI**: `/task/{task_id}`
  其中 `{task_id}` 是 `GET /task` 返回结果中的 `task_id`。
- **METHOD**: DELETE
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • DELETE http://localhost:7070/task/3_10
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
```

### 6.5.子图管理

TuGraph 支持多子图，子图之间完全独立，不同的子图可以对不同用户开放不同权限。管理员可以添加和删除子图。

#### 6.5.1.创建新子图

- **URI**: `/db`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | name | 子图名 | 字符串 |
  | config | 配置 | 字典，格式为 { {列名 1}:{列值 1},... } |

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/db
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
        "name":"graph1",
        "config" : {
            "max_size_GB":2048,
            "description": "description of graph1"
        }
    }
```

**Example response.**

```
    • 200: OK
```

#### 6.5.2.删除子图

- **URI**: `/db/{graph_name}`
- **METHOD**: DELETE
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • DELETE http://localhost:7070/db/graph1
```

**Example response.**

```
    • 200: OK
```

#### 6.5.3.列出所有子图

- **URI**: `/db`
- **METHOD**: GET
- **RESPONSE**: 子图列表

**Example request.**

```
    • GET http://localhost:7070/db
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "graph1": {
            "max_size_GB":1024,
            "description":"description of graph1"
        }
    }
```

#### 6.5.4.获取子图信息

- **URI**: `/db/{graph_name}`
- **METHOD**: GET
- **RESPONSE**: 子图列表

**Example request.**

```
    • GET http://localhost:7070/db/graph1
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "max_size_GB":1024,
        "description":"description of graph1"
    }
```

### 6.6.元数据管理

TuGraph 是一个具备多图能力的强模式属性图数据库。在每一张子图中，每种点和边都需要有预定义的数据格式。数据格式由 Label 决定，每种 Label 都有自己的数据格式。用户可以使用 REST API 添加，删除和查询 Label 及其对应的数据格式。

Label 操作对应的 URI 格式为

```
    http://{host}:{port}/db/{graph_name}/label/{type}/{label_name}
```

其中{type}可以是 node 或者 relationship。

#### 6.6.1.创建Label

创建 Label 的过程同时也是定义其数据类型的过程。只有创建了 Label 才能在图中插入相应类型的点或者边。

- **URI**: `/db/{graph_name}/label`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | name | Label 名 | 字符串 |
  | fields | 数据列定义 | 列表 |
  | is_vertex | 是否是点 Label | 布尔值 |
  | primary | 点的主键属性 | 字符串 |
  | edge_constraints | 边的约束 | 列表 |

`primary` 在 `is_vertex` 为 `true` 的时候设置，这个字段只有点才有, 创建点的时候必须设置。

`edge_constraints` 在 `is_vertex` 为 `false` 的时候设置，这个字段只有边有。这个字段限制了该边的起点和终点只能是哪些点的组合，比如：`[["vertex_label1","vertex_label2"],["vertex_label3","vertex_label4"]]`，限制了该边只能是从 `vertex_label1` 到 `vertex_label2` 和 从 `vertex_label3` 到 `vertex_label4`。如果不想有任何限制，不设置该字段即可。

其中`fields`为一个数组，其中每个元素定义数据的一列，内容如下：

| 域名     | 说明                                     | 类型                                                                                                |
| -------- | ---------------------------------------- | --------------------------------------------------------------------------------------------------- |
| name     | 列名                                     | 字符串                                                                                              |
| type     | 列数据类型                               | 字符串，有以下类型： int8, int16, int32, int64, float, double, string, date, datetime, binary, bool |
| optional | 数据是否可以为空（可选，缺省值为 false） | 布尔值                                                                                              |
表格内容描述: 

该表格包含三个列名：域名、说明和类型。 

第一行数据为“name”，其说明为“列名”，数据类型为“字符串”。 
第二行数据为“type”，其说明为“列数据类型”，数据类型为“字符串”，可取的值包括：int8、int16、int32、int64、float、double、string、date、datetime、binary 和 bool。 
第三行数据为“optional”，其说明为“数据是否可以为空（可选，缺省值为 false）”，数据类型为“布尔值”。 

总体来看，这个表格定义了一些数据字段的基本信息，包括字段名称、其说明以及数据类型和可选性的信息。

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/db/{graph_name}/label
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
        "name":"Actor",
        "fields": [
                {"name":"uid", "type":"int64", "optional":false},
                {"name":"name", "type":"string", "optional":true}
        ],
        "is_vertex":true,
        "primary" : "uid"
    }
```

**Example response.**

```
    • 200: OK
```

#### 6.6.2.列出所有 Label

- **URI**: `/db/{graph_name}/label`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | edge | 边 Label 列表 | 列表 |
  | vertex | 点 Label 列表 | 列表 |

**Example request.**

```
    • GET http://localhost:7070/db/{graph_name}/label
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "edge": [
            "HAS_CHILD",
            "MARRIED",
            "BORN_IN",
            "DIRECTED",
            "WROTE_MUSIC_FOR",
            "ACTED_IN"
        ],
        "vertex": [
            "Person",
            "City",
            "Film"
        ]
    }
```

#### 6.6.3.获取 Label 的数据格式定义

- **URI**: `/db/{graph_name}/label/{[node|relationship]}/{label_name}`
- **METHOD**: GET
- **RESPONSE**: 数据列定义表，类型是一个词典，key 为列名，value 为列定义，列定义见如下：
-

| 域名     | 说明             | 类型   |
| -------- | ---------------- | ------ |
| optional | 该列值是否可为空 | 布尔值 |
| type     | 列值类型         | 字符串 |
表格内容描述:
该表格包含三列，分别为：域名、说明和类型。

第一行数据：
- 域名为 "optional"，说明为 "该列值是否可为空"，类型为 "布尔值"。

第二行数据：
- 域名为 "type"，说明为 "列值类型"，类型为 "字符串"。

总体来看，这个表格主要用于定义两个字段的属性信息，其中"optional"字段表示该值是否可以为空，数据类型为布尔值；而"type"字段则描述列值的数据类型，数据类型为字符串。

**Example request.**

```
    • GET http://localhost:7070/db/{graph_name}/label/node/person
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "age":{
            "optional":false,
            "type":"int16"
        },
        "id":{
            "optional":false,
            "type":"int8"
        },
        "name":{
            "optional":false,
            "type":"string"
        }
    }
```

#### 6.6.4.Schema 导入

- **URI**: `/db/{graph_name}/schema/text`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | description | 文件内容描述 | 字符串 |

description 的具体描述方法见《TuGraph 操作手册》中数据导入配置文件的相关内容。

- **RESPONSE**:

Schema 导入会根据 description 比较新的 Schema 和数据库中原有的 Schema 是否兼容，检查的粒度为 Label。如果不一致则出错，如果一致则添加原先 Schema 中不存在的 Label，返回 200。

**Example request.**

```
    • POST http://localhost:7070/db/graph1/schema/text
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
      "description": "{\\"schema\\":[{\\"label\\":\\"actor\\",\\"primary\\":\\"aid\\",\\"properties\\":[{\\"name\\":\\"aid\\",\\"type\\":\\"STRING\\"}],\\"type\\":\\"VERTEX\\"}]}"
    }
```

上述 description 的值是如下 json 序列化后的字符串:

```json
{
  "schema": [
    {
      "label": "actor",
      "type": "VERTEX",
      "properties": [{ "name": "aid", "type": "STRING" }],
      "primary": "aid"
    }
  ]
}
```

**Example response.**

```
    • 200: OK
    Output:
    {
        "log": ""
    }
```

### 6.7.点操作

URI 格式为

```
    http://{host}:{port}/db/{graph_name}/node/{vid}
```

Nodes 提供节点（Vertex）的 CRUD 操作，接受 GET/POST/PUT/DELETE 请求。

#### 6.7.1.列出点数量和label数量

- **URI**: `/db/{graph_name}/node`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | num_label | 点 label 数量 | 整数 |
  | num_vertex | 点数量 | 整数 |

_注意 num_vertex 返回的并不是准确的点数量，只是一个估计值。_

#### 6.7.2.创建一个点

向数据库中插入一个点。

- **URI**: `/db/{graph_name}/node`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | label | Label 名 | 字符串 |
  | property | 点属性 | 字典，其中 key 是列名，value 是相应值。value 必须是与列类型相应的类型，如列为 int32，则 value 只能是整数。 |

- **RESPONSE**: 如果成功，返回代码 200。并在 JSON 内容中返回新点 vid。该 ID 可用于后续的点操作中。

**Example request.**

```
    • POST http://localhost:7070/db/{graph_name}/node
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
        "label" : "Person",
        "property" : {
            "name" : "Passerby A",
            "birthyear" : 1989
        }
    }
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        21
    }
```

#### 6.7.3.批量创建点

TuGraph 允许一次性插入多个点，以减少网络开销。

- **URI**: `/db/{graph_name}/node`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | label | Label 名 | 字符串 |
  | fields | 点属性 | 列表 |
  | values | 点数据 | 列表 |

其中 fields 是一个字符串列表，列出一系列列名；values 是一个列表，其中每个元素是一个列表，列表中每个元素是列数据。

- **RESPONSE**: 如果成功，返回代码 200。并在 JSON 内容中返回新增加的点的 vid 列表，该列表中每一个 vid 按顺序对应请求中的每一个点。

**Example request.**

```
    • POST http://localhost:7070/db/{graph_name}/node
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
        "label" : "Person",
        "fields" : ["name", "birthyear"],
        "values" : [["alex", 2000],
                    ["bob", 1999]]
    }
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        [
            22,
            23
        ]
    }
```

#### 6.7.4.获取点

- **URI**: `/db/{graph_name}/node/{vertex_id}`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | label | Label 名 | 字符串 |
  | property | 属性 | 字典，格式为 { {列名 1}:{列值 1},...} |

**Example request.**

```
    • GET http://localhost:7070/db/{graph_name}/node/5
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "property": {
            "birthyear": 1963,
            "name": "Natasha Richardson"
        },
        "label": "Person"
    }
```

#### 6.7.5.删除点

- **URI**: `/db/{graph_name}/node/{vertex_id}`
- **METHOD**: DELETE
- **RESPONSE**: 如果成功，返回代码 200。
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | in | 被删掉的点的入边数量 | 整数值 |
  | out | 被删掉的点的出边数量 | 整数值 |

**Example request.**

```
    • DELETE http://localhost:7070/db/{graph_name}/node/4
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "in": 0,
        "out": 0
    }
```

#### 6.7.6.获取点所有属性

- **URI**: `/db/{graph_name}/node/{vertex_id}/property`
- **METHOD**: GET
- **RESPONSE**: Node 所有属性（字典）

**Example request.**

```
    • GET http://localhost:7070/db/{graph_name}/node/5/property
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "birthyear": 1963,
        "name": "Natasha Richardson"
    }
```

#### 6.7.7.获取点属性

- **URI**: `/db/{graph_name}/node/{vertex_id}/property/{field}`
- **METHOD**: GET
- **RESPONSE**: Node 某一属性

**Example request.**

```
    • GET http://localhost:7070/db/{graph_name}/node/5/property/name
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "Natasha Richardson"
    }
```

#### 6.7.8.更新点属性

- **URI**: `/db/{graph_name}/node/{vertex_id}`
- **METHOD**: PUT
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | property | 点属性 | 字典 |

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • PUT http://localhost:7070/db/{graph_name}/node/5
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
      "property" : {
        "birthyear" : 1964,
        "mobile" : "********"
      }
    }
```

**Example response.**

```
    • 200: OK
```

### 6.8.边操作

URI 格式为

```
    http://{host}:{port}/db/{graph_name}/relationship/{euid}
```

与 Nodes 功能类似，Relationships 提供边（edge）的 CRUD 操作，接受 GET/POST/PUT/DELETE 请求。每一条边都可以由一个唯一 ID（euid）来标识。这个 ID 可以从在插入边时获得，或者在 [列出所有边](#%E5%88%97%E5%87%BA%E6%89%80%E6%9C%89%E8%BE%B9) 操作中得到。

#### 6.8.1.创建一条边

- **URI**: `/db/{graph_name}/node/{src}/relationship`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | label | 边 Label | 字符串 |
  | destination | 目的点 ID | 整数值 |
  | property | 边属性 | 字典 |

- **RESPONSE**: 如果成功，返回代码 200，同时返回新建立的边的 euid（字符串）。

**Example request.**

```
    • POST http://localhost:7070/db/{graph_name}/node/{src}/relationship
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
      "destination" : 14,
      "label" : "BORN_IN",
      "property" : {}
    }
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "1_14_1_0"
    }
```

#### 6.8.2.批量创建边

- **URI**: `/db/{graph_name}/relationship`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | label | 边 Label | 字符串 |
  | fields | 数据列名 | 列表 |
  | edge | 边数据 | 列表 |

其中 edge 是一个数据列表，其中每个元素都是一条边，其定义如下：

| 域名        | 说明     | 类型                                                   |
| ----------- | -------- | ------------------------------------------------------ |
| source      | 起点 id  | 整数                                                   |
| destination | 终点 id  | 整数                                                   |
| values      | 数据列表 | 列表，每列对应 fields 中的一个列，类型是该列对应的类型 |
表格内容描述：该表格包含三个列名，分别是“域名”、“说明”和“类型”。 

- 第一行数据为“source”，说明为“起点 id”，其类型为“整数”。
- 第二行数据为“destination”，说明为“终点 id”，同样类型为“整数”。
- 第三行数据为“values”，说明为“数据列表”，其类型为“列表”，并且每列数据对应于“fields”中的一个列，类型为该列对应的类型。

总体而言，该表格概述了源和目的地的标识符（均为整数）以及与之相关的数据列表的结构，这些数据列表中每列的具体类型取决于所涉及的字段。

- **RESPONSE**: 如果成功，返回代码 200，同时返回新建立的边的 euid 列表。

**Example request.**

```
    • POST http://localhost:7070/db/{graph_name}/relationship
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
      "label" : "knows",
      "fields" : ["from_year", "weight"],
      "edge" : [
          {"source":0, "destination":1, "values":[2011, 0.8]},
          {"source":1, "destination":2, "values":[2008, 0.9]}
      ]
    }
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        [
            "0_1_0_0",
            "1_2_0_0"
        ]
    }
```

#### 6.8.3.列出所有出边（outgoing relationships）

- **URI**: `/db/{graph_name}/node/{src}/relationship/out`
- **METHOD**: GET
- **RESPONSE**: 点 src 的所有出边 euid 列表

**Example request.**

```
    • GET http://localhost:7070/db/{graph_name}/node/4/relationship/out
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        [
            "4_5_0_0",
            "4_7_1_2"
        ]
    }
```

#### 6.8.4.列出所有入边（incoming relationships）

- **URI**: `/db/{graph_name}/node/{dst}/relationship/in`
- **METHOD**: GET
- **RESPONSE**: 点 dst 的所有入边 euid 列表

**Example request.**

```
    • GET http://localhost:7070/db/{graph_name}/node/4/relationship/in
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        [
            "0_4_0_0",
            "3_4_3_1"
        ]
    }
```

#### 6.8.5.列出所有边

- **URI**: `/db/{graph_name}/node/{src}/relationship/all`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | in | 入边 | 列表 |
  | out | 出边 | 列表 |

**Example request.**

```
    • GET http://localhost:7070/db/{graph_name}/node/4/relationships/all
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "out": [
            "4_5_0_0",
            "4_7_1_2"
        ],
        "in": [
            "0_4_0_0",
            "3_4_3_1"
        ]
    }
```

#### 6.8.6.获取边

- **URI**: `/db/{graph_name}/relationship/{euid}`
- **METHOD**: GET
- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | label | 边 Label | 字符串 |
  | property | 边属性 | 字典 |

**Example request.**

```
    • GET http://localhost:7070/db/graph1/relationship/0_4_0_0
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "property": {
        },
        "label": "MARRIED"
    }
```

#### 6.8.7.删除边

- **URI**: `/db/{graph_name}/relationship/{euid}`
- **METHOD**: DELETE
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • DELETE http://localhost:7070/db/graph1/relationship/14_0_1_0
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
```

#### 6.8.8.获取边的所有属性

- **URI**: `/db/{graph_name}/relationship/{euid}/property`
- **METHOD**: GET
- **RESPONSE**: 边属性字典

**Example request.**

```
    • GET http://localhost:7070/db/graph1/relationship/14_0_2_0/property
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        {
            "weight": 0.8,
            "begin": 20180922
        }
    }
```

#### 6.8.9.获取边的属性

- **URI**: `/db/{graph_name}/relationship/{euid}/property/{field}`
- **METHOD**: GET
- **RESPONSE**: 如果成功,返回代码 200,同时返回边的属性。如果失败,返回代码 400,同时返回 "Illegal field."。

**Example request.**

```
    • GET http://localhost:7070/db/graph1/relationship/17_0_2_2/property/charactername
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        "Henri Ducard"
    }
```

#### 6.8.10.更新边的属性

- **URI**: `/db/{graph_name}/relationship/{euid}`
- **METHOD**: PUT
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | property | 边属性 | 字典 |

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • PUT http://localhost:7070/db/graph1/relationship/17_0_2_2
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
      "property" : {
        "charactername" : "Henri Ducard/passer a"
      }
    }
```

**Example response.**

```
    • 200: OK
```

### 6.9.索引

URI 格式为

```
    http://{host}:{port}/db/{graph_name}/index/{label}/{field}
```

提供索引操作，接受 GET/POST 请求。

#### 6.9.1.创建索引

该操作会启动一个创建索引的后台任务，用户可以通过列出该 Label 相关的所有索引来检查新建索引的状态。

- **URI**: `/db/{graph_name}/index`
- **METHOD**: POST
- **REQUEST**:

| 域名    | 说明     | 类型                                  |
|-------|--------|-------------------------------------|
| label | Label 名 | 字符串                                 |
| field | 域名     | 字符串                                 |
| type  | 索引类型   | int类型，0表示非唯一索引，1表示全局唯一索引，2表示两点间唯一索引 |
表格内容描述:
该表格包含三列，分别为“域名”、“说明”和“类型”。 

- 第一行数据：域名为“label”，说明是“Label 名”，类型为“字符串”。
- 第二行数据：域名为“field”，说明为“域名”，类型为“字符串”。
- 第三行数据：域名为“type”，说明为“索引类型”，类型为“int类型，0表示非唯一索引，1表示全局唯一索引，2表示两点间唯一索引”。

总体而言，该表格提供了关于不同域名的定义和数据类型的详细说明，其中“label”和“field”是字符串类型，而“type”提供了一种索引标识，用于区分索引的不同特性。

- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • POST http://localhost:7070/db/graph1/index
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json
    Input:
    {
      "label": "Person",
      "field": "birthyear",
      "is_unique" : false
    }
```

**Example response.**

```
    • 200: OK
```

#### 6.9.2.列出所有索引

- **URI**: `/db/{graph_name}/index`
- **METHOD**: GET
- **RESPONSE**: 索引列表，其中每一个元素是一个索引描述，格式与[创建索引](#indexspec)时使用格式相同。

**Example request.**

```
    • GET http://localhost:7070/db/graph1/index
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        [
            {
                "field": "name",
                "label": "City",
                "is_unique": false
            },
            {
                "field": "title",
                "label": "Film",
                "is_unique": false
            },
            {
                "field": "name",
                "label": "Person",
                "is_unique": true
            },
            {
                "label": "Person",
                "field": "age",
                "is_unique": false
            }
        ]
    }
```

#### 6.9.3.列出所有与某个 Label 相关的索引

- **URI**: `/db/{graph_name}/index/{label}`
- **METHOD**: GET
- **RESPONSE**: 索引列表，其中每一个元素是一个索引描述，格式与[创建索引](#indexspec)时使用格式相同。

**Example request.**

```
    • GET http://localhost:7070/db/graph1/index/Person
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    • Content-Type: application/json; charset=UTF-8
    Output:
    {
        [
            {
                "label": "Person",
                "field": "name",
                "is_unique": true
            },
            {
                "label": "Person",
                "field": "age",
                "is_unique": false
            }
        ]
    }
```

#### 6.9.4.删除索引

- **URI**: `/db/{graph_name}/index/{label}/{field}`
- **METHOD**: DELETE
- **RESPONSE**: 如果成功，返回代码 200。

**Example request.**

```
    • DELETE http://localhost:7070/db/graph1/index/Person/name
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
```

#### 6.9.5.根据索引获取点

- **URI**: `/db/{graph_name}/index/{label}/?field={field}&value={value}`
- **METHOD**: GET
- **RESPONSE**: 点 vid 列表

**Example request.**

```
    • GET http://localhost:7070/db/graph1/index/Person/?field=birthyear&value=1986
    • Accept: application/json; charset=UTF-8
```

**Example response.**

```
    • 200: OK
    Output:
    {
        [
            1,
            8
        ]
    }
```


### 6.10.在线增量导入

#### 6.10.1.指定文件内容导入

- **URI**: `/db/{graph_name}/import/text`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | description | 文件内容描述 | 字符串 |
  | data | 要导入的文件内容（建议最大在 16MB 左右，最长不超过 17MB） | 字符串 / 数组 / 对象 |
  | continue_on_error | 出错后是否继续导入（可选，默认为`false`
  ） | 布尔值 |
  | delimiter | 分隔符（可选，默认为`“,”`
  ） | 字符串 |

description 的具体描述方法见《TuGraph 操作手册》中数据导入配置文件的相关内容。

分隔符可以是单字符，也可以是字符串，但不能包含`\r`或者`\n`。

data 可以是如下形式之一：

- 字符串如 `"1,2\n3,4\n"`
- ASCII 码组成的数组如 `[49,44,50,10,51,44,52,10]`
- 形如上述数组的字典如 `{"0":49,"1":44,"2":50,"3":10,"4":51,"5":44,"6":52,"7":10}`

- **RESPONSE**:

系统**不会**自动执行新建 label、添加索引等操作。在此操作之前需要保证涉及的 label 已经存在并具有适当的索引。

如果成功导入完毕，返回代码 200，并在 `log` 字段返回一些日志信息（可能为空）；否则，保证所有的数据均未被导入，并在 `error_message` 字段返回错误信息。

**Example request.**

```
    • POST http://localhost:7070/db/graph1/import/text
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
      "description": "{\\"files\\":[{\\"columns\\":[\\"SRC_ID\\",\\"role\\",\\"DST_ID\\"],\\"format\\":\\"CSV\\",\\"label\\":\\"role\\",\\"SRC_ID\\":\\"actor\\",\\"DST_ID\\":\\"movie\\"}]}"}",
      "data": "1,Role1,2\n3,Role2,4\n",
      "continue_on_error": true,
      "delimiter": ","
    }
```

上述 description 的值是如下 json 序列化后的字符串

```json
{
  "files": [
    {
      "format": "CSV",
      "label": "role",
      "SRC_ID": "actor",
      "DST_ID": "movie",
      "columns": ["SRC_ID", "role", "DST_ID"]
    }
  ]
}
```

**Example response.**

```
    • 200: OK
    Output:
    {
        "log": "Missing src uid 1\n"
    }
```

由于请求中指定了在出错时继续，该返回信息说明 SRC_ID 为 1 的边没有被导入，而其他信息导入成功。

### 6.11.其他

URI 格式为

```
    http://{host}:{port}/db/{graph_name}/misc
```

#### 6.11.1.提取子图

给出点 id 集合，返回包含该集合的最小子图。

- **URI**: `/db/{graph_name}/misc/sub_graph`
- **METHOD**: POST
- **REQUEST**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | vertex_ids | 点 id 集合 | 列表 |

- **RESPONSE**:
  | 域名 | 说明 | 类型 |
  | --- | --- | --- |
  | nodes | 点数据 | 列表，每元素包含 vid, label, 以及属性 |
  | relationships | 边数据 | 列表，每元素包含 src, dst, euid, label, 以及属性 |

**Example request.**

```
    • POST http://localhost:7070/db/graph1/misc/sub_graph
    • Accept: application/json; charset=UTF-8
    • Content-Type: application/json; charset=UTF-8
    Input:
    {
      "vertex_ids": [2, 5, 14, 20]
    }
```

**Example response.**

```
• 200: OK
    Output:
    {
        "nodes": [
            {
                "label": "Person",
                "properties": {
                    "birthyear": 1937,
                    "name": "Vanessa Redgrave"
                },
                "vid": 2
            },
            {
                "label": "Person",
                "properties": {
                    "birthyear": 1963,
                    "name": "Natasha Richardson"
                },
                "vid": 5
            },
            {
                "label": "City",
                "properties": {
                    "name": "London"
                },
                "vid": 14
            },
            {
                "label": "Film",
                "properties": {
                    "title": "Camelot"
                },
                "vid": 20
            }
        ],
        "relationships": [
            {
                "destination": 5,
                "label": "HAS_CHILD",
                "properties": {
                    "birthyear": 1937,
                    "name": "Vanessa Redgrave"
                },
                "source": 2
            },
            {
                "destination": 14,
                "label": "BORN_IN",
                "properties": {
                    "birthyear": 1937,
                    "name": "Vanessa Redgrave"
                },
                "source": 2
            },
            {
                "destination": 20,
                "label": "ACTED_IN",
                "properties": {
                    "birthyear": 1937,
                    "charactername": "Guenevere",
                    "name": "Vanessa Redgrave"
                },
                "source": 2
            },
            {
                "destination": 14,
                "label": "BORN_IN",
                "properties": {
                    "birthyear": 1963,
                    "name": "Natasha Richardson"
                },
                "source": 5
            }
        ]
    }
```
