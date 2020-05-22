---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594396"
---
#### <a name="local-proxy"></a>本地代理

你可以为实时服务器 Visual Studio Code 扩展配置代理，以便将对 `/api` 的所有请求路由到 `http://127.0.0.1:7071/api` 上正在运行的 API 终结点。

1. 打开 _.vscode/settings.json_ 文件。

1. 添加以下设置以指定用于实时服务器的代理。

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   此配置最好保存在项目设置文件中，而不是在用户设置文件中。

   使用项目设置可确保代理不会应用于 Visual Studio Code 中打开的所有其他项目。
