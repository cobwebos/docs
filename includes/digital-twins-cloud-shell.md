---
author: baanders
description: Azure 数字孪生的包含文件 - 设置 Cloud Shell 和 IoT 扩展
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b664303d86f8588fc210b11b363b21d523d63295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507417"
---
要在打开的 [Azure Cloud Shell](https://shell.azure.com) 窗口中开始处理 Azure 数字孪生，首先需要登录，并为此会话将 shell 上下文设置为你的订阅。 在 Cloud Shell 中运行以下命令：

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> 在上面的命令中，还可以使用订阅名称而不使用 ID。 

如果这是你首次将此订阅与 Azure 数字孪生一起使用，请运行该命令以向 Azure 数字孪生命名空间进行注册。 （如果不确定，可以再次运行该命令，即使过去某个时候操作过也是如此。）

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

接下来，将[适用于 Azure CLI 的 Microsoft Azure IoT 扩展](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)添加到 Cloud Shell 以支持用于与 Azure 数字孪生和其他 IoT 服务进行交互的命令。 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

现在 Azure 数字孪生已可在 Cloud Shell 中使用。

若要验证 Azure 数字孪生在 Cloud Shell 中的就绪性，可以随时运行 `az dt -h` 查看可用的顶层 Azure 数字孪生命令列表。