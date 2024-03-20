## 被测系统Halo已知Bug列表
1. 系统500，导致前端样式错乱
* 编辑文章请求体参数
```
{
  "post": {
    "metadata": {
      "name": "${__UUID}",
      "annotations": {
        "content.halo.run/preferred-editor": "default"
      }
    },
    "apiVersion": "content.halo.run/v1alpha1",
    "kind": "Post",
    "spec": {
      "template": "",
      "publishTime": "",
      "pinned": false,
      "visible": "PUBLIC",
      "allowComment": true,
      "title": "Metersphere测试${__Random(0000000000,9999999999,)}",
      "priority": 0,
      "tags": [],
      "cover": "",
      "deleted": false,
      "publish": false,
      "htmlMetas": [],
      "categories": [],
      "excerpt": {
        "raw": "",
        "autoGenerate": true
      },
      "slug": "167${__Random(0000000000,9999999999,)}"
    }
  },
  "content": {
    "rawType": "HTML",
    "raw": "<p><strong>发布文章测试</strong></p><p><strong>全文如下：</strong></p><p>全文内容到此为止！</p>",
    "content": "<p><strong>发布文章测试</strong></p><p><strong>全文如下：</strong></p><p>全文内容到此为止！</p>"
  }
}
```
* 报错前端截图
![](image/halo/Halo500报错信息.jpg)

* 出现此现象的原因
"slug": "167${__Random(0000000000,9999999999,)}"  此参数编写有误，所以请填写此参数，按照标准填写，请勿随意填写
