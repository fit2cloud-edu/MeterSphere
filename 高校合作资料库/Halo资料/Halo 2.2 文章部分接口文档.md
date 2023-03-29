# Halo 2.2 文章部分接口文档

## **1.** 接口说明

接口类型：resetful

接口平台：Halo2.2

接口地址：http://halo.edu.metersphere.com

接口描述：接口采用基本认证方式，即请求头设置 Authorization 的方式来验证

## **2.** 登录认证

### **2.1** 基本认证

这是最简单的一种认证方式，通过简单设置 HTTP 请求头 Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz 即可实现认证，访问 Halo API，其中Authorization的值为Basic 拼接【帐号:密码】的base64编码，（注意Basic 后有一个空格）。

```bash
例如：meterspheretest:Fit2cloud@2023 的base64编码为 bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz ，因此在请求头部添加Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz就可以访问API 接口

生成 base64 编码的方法：
1. linux/mac 直接在终端窗口执行下面命令，回返回编码值
输入：echo -n "meterspheretest:Fit2cloud@2023" | base64

输出：bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz

2.windows可以访问下面链接生成
https://www.bejson.com/enc/base64
```

### 2.2 MeterSphere认证

进入环境配置-全局认证配置，选择认证方式为 Basic Auth，填入用户名及密码保存即可。

### 2.3 表单认证

#### 2.3.1获取csrf接口

* 接口描述:

​		使用postman调用一次登录接口，模拟客户端生成 _csrf ,通过 _csrf 去调用登陆用户的接口

* 接口路径：

  http://halo.edu.metersphere.com/login

* 请求方式：

​		`GET`

* 请求头

```json
accept: */*
```

* 响应为html页面，input中存在 _csrf 的值，响应头部Cookie中 XSRF-TOKEN 的值和 _csrf 是相同的

```html
<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
	<meta name="description" content="">
	<meta name="author" content="">
	<title>Please sign in</title>
	<link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/css/bootstrap.min.css" rel="stylesheet"
		integrity="sha384-/Y6pD6FV/Vv2HJnA6t+vslU6fwYXjCFtcEpHbNJ0lyAFsXTsjBbfaDjzALeQsN6M" crossorigin="anonymous">
	<link href="https://getbootstrap.com/docs/4.0/examples/signin/signin.css" rel="stylesheet"
		crossorigin="anonymous" />
</head>

<body>
	<div class="container">
		<form class="form-signin" method="post" action="/login">
			<h2 class="form-signin-heading">Please sign in</h2>
			<p>
				<label for="username" class="sr-only">Username</label>
				<input type="text" id="username" name="username" class="form-control" placeholder="Username" required autofocus>
        </p>
				<p>
					<label for="password" class="sr-only">Password</label>
					<input type="password" id="password" name="password" class="form-control" placeholder="Password" required>
        </p>
      
      
					<input type="hidden" name="_csrf" value="fff63640-029d-4857-9312-29e93229d6e0">
      
      
					<button class="btn btn-lg btn-primary btn-block" type="submit">Sign in</button>
		</form>
	</div>
</body>

</html>
```

#### 2.3.2 登录接口

* 接口描述：

  通过接口可以获取响应头中 Set-Cookie 的值，并存储在Cookie中，后续接口可以通过 Cookie 中的值进行接口访问

* 接口路径：

  http://halo.edu.metersphere.com/login

* 请求方式：

  `POST`

* 请求头：

```sh
Accept: application/json
Cookie: XSRF-TOKEN=fff63640-029d-4857-9312-29e93229d6e0
Content-Type: application/x-www-form-urlencoded
```

* 请求参数：

| 参数名   | 必填项 | 数据类型 | 备注                                               |
| -------- | ------ | -------- | -------------------------------------------------- |
| _csrf    | 是     | form     | `CSRF` 令牌。由客户端随机生成和XSRF-TOKEN 的值一致 |
| username | 是     | form     | 用户名                                             |
| password | 是     | form     | 密码                                               |

* 响应结果

`bash`

```bash
    < HTTP/1.1 200 OK
    < Vary: Origin
    < Vary: Access-Control-Request-Method
    < Vary: Access-Control-Request-Headers
    < Content-Type: application/json
    < Content-Length: 161
    < Cache-Control: no-cache, no-store, max-age=0, must-revalidate
    < Pragma: no-cache
    < Expires: 0
    < X-Content-Type-Options: nosniff
    < X-Frame-Options: DENY
    < X-XSS-Protection: 1 ; mode=block
    < Referrer-Policy: no-referrer
    < Set-Cookie: SESSION=d04db9f7-d2a6-4b7c-9845-ef790eb4a980; Path=/; HttpOnly; SameSite=Lax
```

`json`

```json
{
    "username": "meterspheretest",
    "authorities": [
        {
            "authority": "ROLE_super-role"
        }
    ],
    "accountNonExpired": true,
    "accountNonLocked": true,
    "credentialsNonExpired": true,
    "enabled": true
}
```

#### 2.3.3 退出接口

* 接口路径：

  http://halo.edu.metersphere.com/logout

* 请求方式：

  `POST`

* 请求参数：

| 参数名 | 必填项 | 数据类型 | 备注                                               |
| ------ | ------ | -------- | -------------------------------------------------- |
| _csrf  | 是     | form     | `CSRF` 令牌。由客户端随机生成和XSRF-TOKEN 的值一致 |

* 响应结果：

```json
HTTP:200
```



## **3.** API说明【以下接口采用基本认证调用】

### 3.1 查看文章列表接口

* 接口描述：

  该接口从Halo服务获取文章列表数据

* 接口路径：

  /apis/api.console.halo.run/v1alpha1/posts

* 请求方式：

  `GET`

* 请求头:

```bash
Accept: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数：

| 参数名       | 必填项 | 数据类型      | 备注                                                 |
| ------------ | ------ | ------------- | ---------------------------------------------------- |
| sort         | 否     | string        | 查询顺序：可用值：PUBLISH_TIME, CREATE_TIME          |
| category     | 否     | array[string] | 分类数据中metadata中的name                           |
| keyword      | 否     | string        | 关键字                                               |
| tag          | 否     | array[string] | 标签数据中metadata中的name                           |
| contributor  | 否     | array[string] | 作者数据中metadata中的name                           |
| visible      | 否     | string        | 是否可见：可用值：PUBLIC, INTERNAL, PRIVATE          |
| sortOrder    | 否     | boolean       | 升序如果为真；否则，它是降序排列                     |
| publishPhase | 否     | string        | 发布状态：DRAFT, PENDING_APPROVAL, PUBLISHED, FAILED |
| size         | 否     | integer       | 每页数据                                             |
| page         | 否     | integer       | 页码                                                 |

* 响应结果【json】：

``` json
{
  "page": 1,
  "size": 1,
  "total": 1,
  "items": [
    {
      "post": {
        "spec": {
          "title": "Hello Halo",
          "slug": "hello-halo",
          "releaseSnapshot": "df062615-ed98-4779-8dea-646a98ba1f67",
          "headSnapshot": "df062615-ed98-4779-8dea-646a98ba1f67",
          "baseSnapshot": "91aab021-33ed-4f10-be97-0f0980f80304",
          "owner": "admin",
          "template": "",
          "cover": "",
          "deleted": false,
          "publish": true,
          "publishTime": "2023-01-12T09:56:45.147724150Z",
          "pinned": false,
          "allowComment": true,
          "visible": "PUBLIC",
          "priority": 0,
          "excerpt": {
            "autoGenerate": false,
            "raw": "如果你看到了这一篇文章，那么证明你已经安装成功了，感谢使用 Halo 进行创作，希望能够使用愉快。"
          },
          "categories": [
            "76514a40-6ef1-4ed9-b58a-e26945bde3ca"
          ],
          "tags": [
            "c33ceabb-d8f1-4711-8991-bb8f5c92ad7c"
          ],
          "htmlMetas": []
        },
        "status": {
          "phase": "PUBLISHED",
          "conditions": [
            {
              "type": "PUBLISHED",
              "status": "TRUE",
              "lastTransitionTime": "2023-01-30T05:37:55.169304006Z",
              "message": "Post published successfully.",
              "reason": "Published"
            },
            {
              "type": "DRAFT",
              "status": "TRUE",
              "lastTransitionTime": "2023-01-12T09:56:44.804972855Z",
              "message": "Drafted post successfully.",
              "reason": "DraftedSuccessfully"
            }
          ],
          "permalink": "http://halo.edu.metersphere.com/archives/hello-halo",
          "excerpt": "如果你看到了这一篇文章，那么证明你已经安装成功了，感谢使用 Halo 进行创作，希望能够使用愉快。",
          "inProgress": false,
          "contributors": [
            "admin",
            "meterspheretest"
          ]
        },
        "apiVersion": "content.halo.run/v1alpha1",
        "kind": "Post",
        "metadata": {
          "finalizers": [
            "post-protection"
          ],
          "name": "5152aea5-c2e8-4717-8bba-2263d46e19d5",
          "labels": {
            "content.halo.run/archive-year": "2023",
            "content.halo.run/archive-month": "01",
            "content.halo.run/published": "true",
            "content.halo.run/deleted": "false",
            "content.halo.run/owner": "admin",
            "content.halo.run/visible": "PUBLIC"
          },
          "annotations": {
            "content.halo.run/last-released-snapshot": "df062615-ed98-4779-8dea-646a98ba1f67",
            "content.halo.run/preferred-editor": "default"
          },
          "version": 38,
          "creationTimestamp": "2023-01-12T09:56:44.557486163Z"
        }
      },
      "categories": [
        {
          "spec": {
            "displayName": "默认分类",
            "slug": "default",
            "description": "这是你的默认分类，如不需要，删除即可。",
            "cover": "",
            "template": "",
            "priority": 0,
            "children": []
          },
          "status": {
            "permalink": "http://halo.edu.metersphere.com/categories/default",
            "postCount": 1,
            "visiblePostCount": 1
          },
          "apiVersion": "content.halo.run/v1alpha1",
          "kind": "Category",
          "metadata": {
            "finalizers": [
              "category-protection"
            ],
            "name": "76514a40-6ef1-4ed9-b58a-e26945bde3ca",
            "version": 5,
            "creationTimestamp": "2023-01-12T09:56:44.100247283Z"
          }
        }
      ],
      "tags": [
        {
          "spec": {
            "displayName": "Halo",
            "slug": "halo",
            "color": "#ffffff",
            "cover": ""
          },
          "status": {
            "permalink": "http://halo.edu.metersphere.com/tags/halo",
            "visiblePostCount": 1,
            "postCount": 1
          },
          "apiVersion": "content.halo.run/v1alpha1",
          "kind": "Tag",
          "metadata": {
            "finalizers": [
              "tag-protection"
            ],
            "name": "c33ceabb-d8f1-4711-8991-bb8f5c92ad7c",
            "version": 5,
            "creationTimestamp": "2023-01-12T09:56:44.342999393Z"
          }
        }
      ],
      "contributors": [
        {
          "displayName": "Administrator",
          "name": "admin"
        },
        {
          "displayName": "ms测试用户",
          "avatar": "",
          "name": "meterspheretest"
        }
      ],
      "owner": {
        "displayName": "Administrator",
        "name": "admin"
      },
      "stats": {
        "visit": 20,
        "upvote": 0,
        "totalComment": 6,
        "approvedComment": 2
      }
    }
  ],
  "first": true,
  "last": true,
  "hasNext": false,
  "hasPrevious": false,
  "totalPages": 1
}
```

### 3.2 查看单篇文章信息接口

* 接口路径：

  /apis/content.halo.run/v1alpha1/posts/{name}

* 请求方式：

  `GET`

* 请求头:

```bash
Accept: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数：

| 参数名 | 必填项 | 数据类型 | 备注                       |
| ------ | ------ | -------- | -------------------------- |
| name   | 是     | string   | 文章数据中metadata中的name |

* * 响应结果：

```json
{
    "spec": {
        "title": "Hello Halo",
        "slug": "hello-halo",
        "releaseSnapshot": "4e39bc30-394d-4339-ba46-7c4e36036f1e",
        "headSnapshot": "4e39bc30-394d-4339-ba46-7c4e36036f1e",
        "baseSnapshot": "4e39bc30-394d-4339-ba46-7c4e36036f1e",
        "owner": "admin",
        "template": "",
        "cover": "",
        "deleted": true,
        "publish": true,
        "publishTime": "2023-02-09T10:57:32.536215356Z",
        "pinned": false,
        "allowComment": true,
        "visible": "PUBLIC",
        "priority": 0,
        "excerpt": {
            "autoGenerate": false,
            "raw": "如果你看到了这一篇文章，那么证明你已经安装成功了，感谢使用 Halo 进行创作，希望能够使用愉快。"
        },
        "categories": [
            "76514a40-6ef1-4ed9-b58a-e26945bde3ca"
        ],
        "tags": [
            "c33ceabb-d8f1-4711-8991-bb8f5c92ad7c"
        ],
        "htmlMetas": [
            
        ]
    },
    "status": {
        "phase": "PUBLISHED",
        "conditions": [
            {
                "type": "PUBLISHED",
                "status": "TRUE",
                "lastTransitionTime": "2023-02-09T10:57:32.536185562Z",
                "message": "Post published successfully.",
                "reason": "Published"
            },
            {
                "type": "DRAFT",
                "status": "TRUE",
                "lastTransitionTime": "2023-02-09T10:57:32.355392721Z",
                "message": "Drafted post successfully.",
                "reason": "DraftedSuccessfully"
            }
        ],
        "permalink": "http://halo.edu.metersphere.com:80/archives/hello-halo",
        "excerpt": "如果你看到了这一篇文章，那么证明你已经安装成功了，感谢使用 Halo 进行创作，希望能够使用愉快。",
        "inProgress": false,
        "contributors": [
            "admin"
        ],
        "lastModifyTime": "2023-02-09T10:57:32.326734336Z"
    },
    "apiVersion": "content.halo.run/v1alpha1",
    "kind": "Post",
    "metadata": {
        "finalizers": [
            "post-protection"
        ],
        "name": "5152aea5-c2e8-4717-8bba-2263d46e19d5",
        "labels": {
            "content.halo.run/archive-year": "2023",
            "content.halo.run/archive-month": "02",
            "content.halo.run/published": "true",
            "content.halo.run/deleted": "true",
            "content.halo.run/owner": "admin",
            "content.halo.run/visible": "PUBLIC"
        },
        "annotations": {
            "content.halo.run/last-released-snapshot": "4e39bc30-394d-4339-ba46-7c4e36036f1e"
        },
        "version": 10,
        "creationTimestamp": "2023-02-09T10:57:32.307579198Z"
    }
}
```



### 3.3 查看文章内容接口

* 接口路径:

  /apis/api.console.halo.run/v1alpha1/posts/{name}/release-content

* 请求方式：

  `GET`

* 请求头:

```bash
Accept: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数：

| 参数名 | 必填项 | 数据类型 | 备注                       |
| ------ | ------ | -------- | -------------------------- |
| name   | 是     | string   | 文章数据中metadata中的name |

* 响应结果【json】:

```json
{
  "snapshotName": "df062615-ed98-4779-8dea-646a98ba1f67",
  "raw": "<h2 id=\"hello-halo\"><strong>Hello Halo</strong></h2><p>如果你看到了这一篇文章，那么证明你已经安装成功了，感谢使用 <a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://halo.run/\">Halo</a> 进行创作，希望能够使用愉快。</p><h2 id=\"%E7%9B%B8%E5%85%B3%E9%93%BE%E6%8E%A5\"><strong>相关链接</strong></h2><ul><li><p>官网：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://halo.run\">https://halo.run</a></p></li><li><p>文档：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://docs.halo.run\">https://docs.halo.run</a></p></li><li><p>社区：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://bbs.halo.run\">https://bbs.halo.run</a></p></li><li><p>主题仓库：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://halo.run/themes.html\">https://halo.run/themes.html</a></p></li><li><p>开源地址：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://github.com/halo-dev/halo\">https://github.com/halo-dev/halo</a></p></li></ul><p>在使用过程中，有任何问题都可以通过以上链接找寻答案，或者联系我们。</p><blockquote><p>这是一篇自动生成的文章，请删除这篇文章之后开始你的创作吧！</p></blockquote>",
  "content": "<h2 id=\"hello-halo\"><strong>Hello Halo</strong></h2><p>如果你看到了这一篇文章，那么证明你已经安装成功了，感谢使用 <a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://halo.run/\">Halo</a> 进行创作，希望能够使用愉快。</p><h2 id=\"%E7%9B%B8%E5%85%B3%E9%93%BE%E6%8E%A5\"><strong>相关链接</strong></h2><ul><li><p>官网：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://halo.run\">https://halo.run</a></p></li><li><p>文档：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://docs.halo.run\">https://docs.halo.run</a></p></li><li><p>社区：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://bbs.halo.run\">https://bbs.halo.run</a></p></li><li><p>主题仓库：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://halo.run/themes.html\">https://halo.run/themes.html</a></p></li><li><p>开源地址：<a target=\"_blank\" rel=\"noopener noreferrer nofollow\" href=\"https://github.com/halo-dev/halo\">https://github.com/halo-dev/halo</a></p></li></ul><p>在使用过程中，有任何问题都可以通过以上链接找寻答案，或者联系我们。</p><blockquote><p>这是一篇自动生成的文章，请删除这篇文章之后开始你的创作吧！</p></blockquote>",
  "rawType": "HTML"
}
```



### 3.4 查看分类列表接口

* 接口路径：

  /apis/content.halo.run/v1alpha1/categories

* 请求方式：

  `GET`

* 请求头:

```bash
Accept: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数：

| 参数名 | 必填项 | 数据类型 | 备注     |
| ------ | ------ | -------- | -------- |
| size   | 否     | integer  | 每页数据 |
| page   | 否     | integer  | 页码     |

* 响应结果【json】：

```json
{
  "page": 1,
  "size": 1,
  "total": 1,
  "items": [
    {
      "spec": {
        "displayName": "默认分类",
        "slug": "default",
        "description": "这是你的默认分类，如不需要，删除即可。",
        "cover": "",
        "template": "",
        "priority": 0,
        "children": []
      },
      "status": {
        "permalink": "http://halo.edu.metersphere.com/categories/default",
        "postCount": 1,
        "visiblePostCount": 1
      },
      "apiVersion": "content.halo.run/v1alpha1",
      "kind": "Category",
      "metadata": {
        "finalizers": [
          "category-protection"
        ],
        "name": "76514a40-6ef1-4ed9-b58a-e26945bde3ca",
        "version": 5,
        "creationTimestamp": "2023-01-12T09:56:44.100247283Z"
      }
    }
  ],
  "first": true,
  "last": true,
  "hasNext": false,
  "hasPrevious": false,
  "totalPages": 1
}
```



### 3.5 查看标签列表接口

* 接口路径：

  /apis/content.halo.run/v1alpha1/tags

* 请求方式：

  `GET`

* 请求头:

```bash
Accept: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数：

| 参数名 | 必填项 | 数据类型 | 备注     |
| ------ | ------ | -------- | -------- |
| size   | 否     | integer  | 每页数据 |
| page   | 否     | integer  | 页码     |

* 响应结果【json】：

```json
{
  "page": 1,
  "size": 1,
  "total": 1,
  "items": [
    {
      "spec": {
        "displayName": "Halo",
        "slug": "halo",
        "color": "#ffffff",
        "cover": ""
      },
      "status": {
        "permalink": "http://halo.edu.metersphere.com/tags/halo",
        "visiblePostCount": 1,
        "postCount": 1
      },
      "apiVersion": "content.halo.run/v1alpha1",
      "kind": "Tag",
      "metadata": {
        "finalizers": [
          "tag-protection"
        ],
        "name": "c33ceabb-d8f1-4711-8991-bb8f5c92ad7c",
        "version": 5,
        "creationTimestamp": "2023-01-12T09:56:44.342999393Z"
      }
    }
  ],
  "first": true,
  "last": true,
  "hasNext": false,
  "hasPrevious": false,
  "totalPages": 1
}
```



### 3.6 编写一篇文章接口

* 接口路径：

  /apis/api.console.halo.run/v1alpha1/posts

* 请求方式：

  `POST `

* 请求头:

```bash
Accept: */*
Content-Type: application/json
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数【json】：

```json
{
  "post": {
    "spec": {                                              // 必填                      // 文章规格说明
      "title": "string",                                   // 必填                      // 文章标题
      "slug": "string",                                    // 必填                      // 别名，通常用于生成 status.permalink
      "releaseSnapshot": "string",
      "headSnapshot": "string",
      "baseSnapshot": "string",
      "owner": "string",                                                            // 创建者名称，即 ContributorVo 的 metadata.name，非显示名称
      "template": "string",                                                         // 自定义渲染模板
      "cover": "string",                                                            // 封面图
      "deleted": false,                                     // 必填 默认false        // 回收站
      "publish": false,                                     // 必填 默认false         //公开
      "publishTime": "2023-02-03T10:15:29.769Z",                                    // 发布时间
      "pinned": false,                                      // 必填 默认false        // 是否置顶
      "allowComment": true,                                 // 必填 默认true         // 是否允许评论
      "visible": "PUBLIC",                                  // 必填 默认PUBLIC 可用：PUBLIC, INTERNAL, PRIVATE 
      "priority": 0,                                        // 必填 默认0
      "excerpt": {
        "autoGenerate": true,                               // 必填 默认true         // 是否自动生成摘要
        "raw": "string"                                                             // 摘要内容
      },
      "categories": [                                                               // 分类的名称集合，即 Category 的 metadata.name 的集合
        "string"
      ],
      "tags": [                                                                     // 标签的名称集合，即 Tag 的 metadata.name 的集合
        "string"
      ],
      "htmlMetas": [
        {
          "additionalProp1": "string",
          "additionalProp2": "string",
          "additionalProp3": "string"
        }
      ]
    },
    "status": {
      "phase": "string",                                    // 必填                 // 发布状态：DRAFT, PENDING_APPROVAL, PUBLISHED, FAILED
      "conditions": [                                                               // 健康状况
        {
          "type": "S-Kl-AcW2YtKWVU20msS7-Xo1bfubeL8qX7yz_1fX-w9GUmC9-xBkJUUwBSqOjih46KLGieHb8akckQDFZu9o63g62pmA4D5Kt4WW",
          "status": "TRUE",
          "lastTransitionTime": "2023-02-03T10:15:29.771Z",                         // 最后检测时间
          "message": "string",                                                      // 消息
          "reason": "PvmaBG1PCgwB8a6bUz5kJE84Mce_FrB0wJaQH"
        }
      ],
      "permalink": "string",                                                                    // 固定链接
      "excerpt": "string",                                                                      // 最终生成的摘要
      "inProgress": true,                                                                       
      "commentsCount": 0,                                                                       // 评论数
      "contributors": [                                                                         // 贡献者名称，Contributor 的 metadata.name 的集合
        "string"
      ],
      "lastModifyTime": "2023-02-03T10:15:29.772Z"                                              // 最后修改时间
    },
    "apiVersion": "string",                                             // 必填                 // api版本号
    "kind": "string",                                                   // 必填                 //种类
    "metadata": {
      "generateName": "string",
      "finalizers": [
        "string"
      ],
      "name": "string",                                                 // 必填                 // 唯一标识
      "labels": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      },
      "annotations": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      },
      "version": 0,
      "creationTimestamp": "2023-02-03T10:15:29.772Z",
      "deletionTimestamp": "2023-02-03T10:15:29.772Z"
    }
  },
  "content": {
    "raw": "string",														// 原始内容
    "content": "string",												// 内容
    "rawType": "string"													// 内容类型
  }
}
```



* 响应结果【json】：

```json
{
  "spec": {
    "title": "文章标题",
    "slug": "文章别名",
    "headSnapshot": "fd86c014-b2f7-47ef-be3c-99ca89a40bc2",
    "baseSnapshot": "fd86c014-b2f7-47ef-be3c-99ca89a40bc2",
    "owner": "meterspheretest",
    "deleted": false,
    "publish": false,
    "pinned": false,
    "allowComment": true,
    "visible": "PUBLIC",
    "priority": 0,
    "excerpt": {
      "autoGenerate": true
    }
  },
  "status": {
    "phase": "DRAFT",
    "conditions": [
      {
        "type": "DRAFT",
        "status": "TRUE",
        "lastTransitionTime": "2023-02-06T03:08:45.480997169Z",
        "message": "Drafted post successfully.",
        "reason": "DraftedSuccessfully"
      }
    ]
  },
  "apiVersion": "content.halo.run/v1alpha1",
  "kind": "Post",
  "metadata": {
    "name": "这是一篇API测试文章",
    "version": 1,
    "creationTimestamp": "2023-02-06T03:08:45.420500474Z"
  }
}
```

​    

### 3.7 发布一篇文章接口

* 接口路径：

  /apis/api.console.halo.run/v1alpha1/posts/{name}/publish

* 请求方式：

  `PUT`

* 请求头:

```bash
Accept: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数：

| 参数名       | 必填项 | 数据类型 | 备注                       |
| ------------ | ------ | -------- | -------------------------- |
| name         | 是     | string   | 文章数据中metadata中的name |
| headSnapshot | 否     | string   | 文章头部快照内容           |

* 响应结果【json】：

```json
{
  "spec": {
    "title": "文章标题",
    "slug": "3223werwe32424",
    "releaseSnapshot": "5218e2a0-f1ce-4f8b-9d55-de9945a7f556",
    "headSnapshot": "5218e2a0-f1ce-4f8b-9d55-de9945a7f556",
    "baseSnapshot": "5218e2a0-f1ce-4f8b-9d55-de9945a7f556",
    "owner": "admin",
    "template": "",
    "cover": "",
    "deleted": false,
    "publish": true,
    "publishTime": "2023-02-09T10:19:14.874042846Z",
    "pinned": false,
    "allowComment": true,
    "visible": "PUBLIC",
    "priority": 0,
    "excerpt": {
      "autoGenerate": true,
      "raw": ""
    },
    "categories": [],
    "tags": [],
    "htmlMetas": []
  },
  "status": {
    "phase": "PUBLISHED",
    "conditions": [
      {
        "type": "PUBLISHED",
        "status": "TRUE",
        "lastTransitionTime": "2023-02-09T10:19:14.874032158Z",
        "message": "Post published successfully.",
        "reason": "Published"
      },
      {
        "type": "DRAFT",
        "status": "TRUE",
        "lastTransitionTime": "2023-02-09T10:18:55.958229341Z",
        "message": "Drafted post successfully.",
        "reason": "DraftedSuccessfully"
      }
    ],
    "permalink": "http://halo.edu.metersphere.com:80/archives/3223werwe32424",
    "excerpt": "V2.2版本有用户遇到接口自动化调试出现死锁的问题，问题现象：除了接口自动化模块，单接口用例、用例执行都是正常的。此处简单记录下死锁出现及排查过程。",
    "inProgress": false,
    "contributors": [
      "admin"
    ],
    "lastModifyTime": "2023-02-09T10:18:55.952327867Z"
  },
  "apiVersion": "content.halo.run/v1alpha1",
  "kind": "Post",
  "metadata": {
    "finalizers": [
      "post-protection"
    ],
    "name": "6055acc3-6a3b-40e4-9e6f-bd43a1e4r5t6",
    "labels": {
      "content.halo.run/published": "true",
      "content.halo.run/deleted": "false",
      "content.halo.run/owner": "admin",
      "content.halo.run/visible": "PUBLIC",
      "content.halo.run/archive-year": "2023",
      "content.halo.run/archive-month": "02"
    },
    "annotations": {
      "content.halo.run/last-released-snapshot": "5218e2a0-f1ce-4f8b-9d55-de9945a7f556"
    },
    "version": 8,
    "creationTimestamp": "2023-02-09T10:18:55.949851517Z"
  }
}
```



### 3.8 删除/回收文章接口

* 接口路径：

  /apis/api.console.halo.run/v1alpha1/posts/{name}/recycle

* 请求方式：

  `PUT`

* 请求头:

```bash
Accept: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数：

| 参数名 | 必填项 | 数据类型 | 备注                       |
| ------ | ------ | -------- | -------------------------- |
| name   | 是     | string   | 文章数据中metadata中的name |

* 响应结果【json】：

```json
{
  "spec": {
    "title": "文章标题",
    "slug": "3223werwe32424",
    "releaseSnapshot": "5218e2a0-f1ce-4f8b-9d55-de9945a7f556",
    "headSnapshot": "5218e2a0-f1ce-4f8b-9d55-de9945a7f556",
    "baseSnapshot": "5218e2a0-f1ce-4f8b-9d55-de9945a7f556",
    "owner": "admin",
    "template": "",
    "cover": "",
    "deleted": true,
    "publish": true,
    "publishTime": "2023-02-09T10:19:14.874042846Z",
    "pinned": false,
    "allowComment": true,
    "visible": "PUBLIC",
    "priority": 0,
    "excerpt": {
      "autoGenerate": true,
      "raw": ""
    },
    "categories": [],
    "tags": [],
    "htmlMetas": []
  },
  "status": {
    "phase": "PUBLISHED",
    "conditions": [
      {
        "type": "PUBLISHED",
        "status": "TRUE",
        "lastTransitionTime": "2023-02-09T10:19:14.874032158Z",
        "message": "Post published successfully.",
        "reason": "Published"
      },
      {
        "type": "DRAFT",
        "status": "TRUE",
        "lastTransitionTime": "2023-02-09T10:18:55.958229341Z",
        "message": "Drafted post successfully.",
        "reason": "DraftedSuccessfully"
      }
    ],
    "permalink": "http://halo.edu.metersphere.com:80/archives/3223werwe32424",
    "excerpt": "V2.2版本有用户遇到接口自动化调试出现死锁的问题，问题现象：除了接口自动化模块，单接口用例、用例执行都是正常的。此处简单记录下死锁出现及排查过程。",
    "inProgress": false,
    "contributors": [
      "admin"
    ],
    "lastModifyTime": "2023-02-09T10:18:55.952327867Z"
  },
  "apiVersion": "content.halo.run/v1alpha1",
  "kind": "Post",
  "metadata": {
    "finalizers": [
      "post-protection"
    ],
    "name": "6055acc3-6a3b-40e4-9e6f-bd43a1e4r5t6",
    "labels": {
      "content.halo.run/published": "true",
      "content.halo.run/deleted": "false",
      "content.halo.run/owner": "admin",
      "content.halo.run/visible": "PUBLIC",
      "content.halo.run/archive-year": "2023",
      "content.halo.run/archive-month": "02"
    },
    "annotations": {
      "content.halo.run/last-released-snapshot": "5218e2a0-f1ce-4f8b-9d55-de9945a7f556"
    },
    "version": 9,
    "creationTimestamp": "2023-02-09T10:18:55.949851517Z"
  }
}
```



### 3.9 发表评论接口

* 接口路径：

  /apis/api.console.halo.run/v1alpha1/comments

* 请求方式：

  `POST`

* 请求头:

```bash
Accept: */*
Content-Type: application/json
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数：

```json
{
	"raw": "Api测试测试",					// 必填  // 评论原内容
	"content": "Api测试测试",			// 必填  // 评论内容
	"allowNotification": true,  // 是否允许通知
	"subjectRef": {
		"group": "content.halo.run",  // 默认
		"kind": "Post",  // 默认
		"name": "6055acc3-6a3b-40e4-9e6f-bd43a1e4r5t6", // 必填 // 文章数据中metadata中的name
		"version": "v1alpha1"  // 默认
	}
}
```

* 响应结果【json】：

```json
{
  "spec": {
    "raw": "Api测试测试",
    "content": "Api测试测试",
    "owner": {
      "kind": "User",
      "name": "admin",
      "displayName": "Administrator"
    },
    "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36",
    "ipAddress": "123.52.11.79",
    "priority": 0,
    "top": false,
    "allowNotification": true,
    "approved": false,
    "hidden": false,
    "subjectRef": {
      "group": "content.halo.run",
      "version": "v1alpha1",
      "kind": "Post",
      "name": "6055acc3-6a3b-40e4-9e6f-bd43a1e4r5t6"
    }
  },
  "apiVersion": "content.halo.run/v1alpha1",
  "kind": "Comment",
  "metadata": {
    "name": "e421a0f4-7fa8-4264-b5cb-b37ccbd82694",
    "version": 0,
    "creationTimestamp": "2023-02-09T11:51:22.671064570Z"
  }
}
```



### 3.10 审核评论接口

* 接口路径：

  /apis/content.halo.run/v1alpha1/comments/{name}

* 请求方式：

  `PUT`

* 请求头:

```bash
Accept: */*
Content-Type: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求url参数：

| 参数名 | 必填项 | 数据类型 | 备注                       |
| ------ | ------ | -------- | -------------------------- |
| name   | 是     | string   | 评论数据中metadata中的name |

* 请求体参数

```json
{
	"spec": {
		"raw": "Api测试测试",
		"content": "Api测试测试",
		"owner": {
			"kind": "User",
			"name": "admin",
			"displayName": "Administrator"
		},
		"userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36",
		"ipAddress": "123.52.11.79",
		"priority": 0,
		"top": false,
		"allowNotification": true,
		"approved": true,
		"hidden": false,
		"subjectRef": {
			"group": "content.halo.run",
			"version": "v1alpha1",
			"kind": "Post",
			"name": "6055acc3-6a3b-40e4-9e6f-bd43a1e4r5t6"
		},
		"approvedTime": "2023-02-09T11:53:38.588Z"
	},
	"status": {
		"unreadReplyCount": 0,
		"hasNewReply": false
	},
	"apiVersion": "content.halo.run/v1alpha1",
	"kind": "Comment",
	"metadata": {
		"finalizers": ["comment-protection"],
		"name": "e421a0f4-7fa8-4264-b5cb-b37ccbd82694",
		"annotations": {},
		"version": 2,
		"creationTimestamp": "2023-02-09T11:48:19.528896102Z"
	}
}
```



* 响应结果【json】：

```json
{
  "spec": {
    "raw": "Api测试测试",
    "content": "Api测试测试",
    "owner": {
      "kind": "User",
      "name": "admin",
      "displayName": "Administrator"
    },
    "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36",
    "ipAddress": "123.52.11.79",
    "priority": 0,
    "top": false,
    "allowNotification": true,
    "approved": true,
    "hidden": false,
    "subjectRef": {
      "group": "content.halo.run",
      "version": "v1alpha1",
      "kind": "Post",
      "name": "6055acc3-6a3b-40e4-9e6f-bd43a1e4r5t6"
    },
    "approvedTime": "2023-02-09T11:53:38.588Z"
  },
  "status": {
    "unreadReplyCount": 0,
    "hasNewReply": false
  },
  "apiVersion": "content.halo.run/v1alpha1",
  "kind": "Comment",
  "metadata": {
    "finalizers": [
      "comment-protection"
    ],
    "name": "e421a0f4-7fa8-4264-b5cb-b37ccbd82694",
    "annotations": {},
    "version": 3,
    "creationTimestamp": "2023-02-09T11:51:22.671064570Z"
  }
}
```



### 3.11 回复评论接口

* 接口路径：

  /apis/content.halo.run/v1alpha1/comments/{name}/reply

* 请求方式：

  `POST`

* 请求头:

```bash
Accept: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求url参数：

| 参数名 | 必填项 | 数据类型 | 备注                       |
| ------ | ------ | -------- | -------------------------- |
| name   | 是     | string   | 评论数据中metadata中的name |

* 请求体参数

```json
{"raw":"我234","content":"我234","allowNotification":true}
```

* 响应结果【json】：

```json
{
	"spec": {
		"raw": "我234",
		"content": "我234",
		"owner": {
			"kind": "User",
			"name": "admin",
			"displayName": "Administrator"
		},
		"userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36",
		"ipAddress": "123.52.11.79",
		"priority": 0,
		"top": false,
		"allowNotification": true,
		"approved": false,
		"hidden": false,
		"commentName": "e421a0f4-7fa8-4264-b5cb-b37ccbd82694"
	},
	"apiVersion": "content.halo.run/v1alpha1",
	"kind": "Reply",
	"metadata": {
		"name": "c44b1600-17fe-4493-a601-55dc484273cc",
		"version": 0,
		"creationTimestamp": "2023-02-09T12:10:46.556099959Z"
	}
}
```



### 3.12  删除评论接口

* 接口路径：

  /apis/content.halo.run/v1alpha1/comments/{name}

* 请求方式：

  `DELETE`

* 请求头:

```bash
Accept: */*
Authorization: Basic bWV0ZXJzcGhlcmV0ZXN0OkZpdDJjbG91ZEAyMDIz
```

* 请求参数：

| 参数名 | 必填项 | 数据类型 | 备注                       |
| ------ | ------ | -------- | -------------------------- |
| name   | 是     | string   | 评论数据中metadata中的name |

* 响应结果【json】：

```json
{
  "spec": {
    "raw": "Api测试测试",
    "content": "Api测试测试",
    "owner": {
      "kind": "User",
      "name": "admin",
      "displayName": "Administrator"
    },
    "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36",
    "ipAddress": "123.52.11.79",
    "approvedTime": "2023-02-09T11:53:38.588Z",
    "priority": 0,
    "top": false,
    "allowNotification": true,
    "approved": true,
    "hidden": false,
    "subjectRef": {
      "group": "content.halo.run",
      "version": "v1alpha1",
      "kind": "Post",
      "name": "6055acc3-6a3b-40e4-9e6f-bd43a1e4r5t6"
    }
  },
  "status": {
    "unreadReplyCount": 0,
    "hasNewReply": false
  },
  "apiVersion": "content.halo.run/v1alpha1",
  "kind": "Comment",
  "metadata": {
    "finalizers": [
      "comment-protection"
    ],
    "name": "e421a0f4-7fa8-4264-b5cb-b37ccbd82694",
    "annotations": {},
    "version": 4,
    "creationTimestamp": "2023-02-09T11:51:22.671064570Z",
    "deletionTimestamp": "2023-02-09T12:05:26.996098309Z"
  }
}
```



