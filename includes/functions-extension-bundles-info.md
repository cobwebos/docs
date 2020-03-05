---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201018"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> 在启动过程中，主机会下载并安装[存储绑定扩展](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher)和其他 Microsoft 绑定扩展。 之所以安装这些扩展，是因为默认情况下，已在 *host.json* 文件中使用以下属性启用了绑定扩展：
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> 如果遇到任何与绑定扩展相关的错误，请检查上述属性是否在 *host.json* 中存在。
::: zone-end  