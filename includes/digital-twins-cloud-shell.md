---
author: baanders
description: Azure 数字孪生的包含文件 - 设置 Cloud Shell 和 IoT 扩展
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296961"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>设置 Cloud Shell 会话

在打开 Cloud Shell 窗口后，要做的第一件事是登录，并将 shell 上下文设置为此会话的订阅。 在 Cloud Shell 中运行以下命令：

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

如果这是你首次将此订阅与 Azure 数字孪生一起使用，请运行该命令以向 Azure 数字孪生命名空间进行注册。 （如果不确定，可以再次运行该命令，即使过去某个时候操作过也是如此。）

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

接下来，将[适用于 Azure CLI 的 Microsoft Azure IoT 扩展](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)添加到 Cloud Shell 以支持用于与 Azure 数字孪生和其他 IoT 服务进行交互的命令。 使用以下命令添加该扩展：

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

如果该扩展之前已经安装，输出可能会显示“azure-iot 扩展已安装”。 这种情况下，请运行以下命令确保具有最新更新： 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

现在 Azure 数字孪生已可在 Cloud Shell 中使用。

若要验证 Azure 数字孪生在 Cloud Shell 中的就绪性，可以随时运行 `az dt -h` 查看可用的顶层 Azure 数字孪生命令列表。