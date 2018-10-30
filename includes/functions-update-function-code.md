---
title: include 文件
description: include 文件
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987958"
---
## <a name="update-the-function"></a>更新函数

默认情况下，模板会创建一个函数，该函数在进行请求时需要功能键。 为了便于在 Azure 中测试此函数，需更新此函数，使之允许匿名访问。 进行此更改的方式取决于函数项目语言。

### <a name="c"></a>C\#

打开 MyHttpTrigger.cs 代码文件（你的新函数），并将函数定义中的 **AuthorizationLevel** 属性更新为值 `anonymous`，然后保存所做的更改。

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

打开新函数的 function.json 文件（在文本编辑器中打开），将 **bindings.httpTrigger** 中的 **authLevel** 属性更新为 `anonymous`，然后保存所做的更改。

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

现在可以在 Azure 中调用此函数，不需提供功能键。 在本地运行时，从不需要功能键。
