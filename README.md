# 支付宝小程序分包加载

- 启动页和tabBar所有页面必须放主包
- 每个分包的根目录不能是另一个分包内的子目录
- 分包只能引用主包和自己包的资源, 不能其他分包的资源(图片,js等)
- 大小限制: 所有分包总大小不超过4MB, 单个分包或主包大小不超过2MB

## 分包小程序目录

```bash
├── app.acss
├── app.js
├── app.json
├── packageA
│   └── pages
│       ├── page1
│       └── page2
├── packageB
│   └── pages
│       ├── page3
│       └── page4
└── pages
    ├── common
    └── index
```

## 声明分包结构

```js
// app.json
{
  "pages": [
    "pages/index",
    "pages/common"
  ],
  // subPackages字段声明小程序的分包结构
  // subPackages配置以外的目录默认属于主包
  "subPackages": [
    {
      // 分包根目录
      "root": "packageA",
      // 分包页面路径
      "pages": [
        "pages/page1",
        "pages/page2"
      ]
    },
    {
      "root": "packageB",
      "pages": [
        "pages/page3",
        "pages/page4"
      ]
    }
  ]
}
```

## 分包预下载配置

- 预下载: 当小程序进入某个页面时, 框架自动下载可能需要的分包

```js
// app.json
{
  "pages": ["pages/index"],
  "subPackages": [
    {
      "root": "sub1",
      "pages": ["page1"]
    },
    {
      "root": "sub2",
      "pages": ["page2"]
    },
    {
      "root": "sub3",
      "pages": ["page3"]
    },
    {
      "root": "path/sub4",
      "pages": ["page4"]
    }
  ],
  // 分包预下载配置
  "preloadRule": {
    // 页面路径:进入该页面后预下载的配置
    "pages/index": {
      "network": "all", // 指定网络下进行预下载:all,wifi
      "packages": ["sub1"] // 预下载的分包的root
    },
    "sub1/page1": {
      "packages": ["sub2", "sub3"]
    },
    "sub3/page3": {
      "network": "wifi",
      "packages": ["path/sub4"]
    }
  }
}
```