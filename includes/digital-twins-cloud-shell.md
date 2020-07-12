---
author: baanders
description: Azure 数字孪生的包含文件 - 设置 Cloud Shell 和 IoT 扩展
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277763"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>设置 Cloud Shell 会话

在打开 Cloud Shell 窗口后，要做的第一件事是登录，并将 shell 上下文设置为此会话的订阅。 在 Cloud Shell 中运行以下命令：

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> 你还可以使用订阅名称来设置订阅。 使用此命令： 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
如果这是你首次将此订阅与 Azure 数字孪生一起使用，请运行该命令以向 Azure 数字孪生命名空间进行注册。 （如果不确定，可以再次运行该命令，即使过去某个时候操作过也是如此。）

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

接下来，将[适用于 Azure CLI 的 Microsoft Azure IoT 扩展](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)添加到 Cloud Shell 以支持用于与 Azure 数字孪生和其他 IoT 服务进行交互的命令。 

首先，运行此命令来查看已安装的所有扩展的列表。

```azurecli-interactive
az extension list
```

在输出中，查找 `"name"` 每个列表条目的字段，以查看扩展的名称。

使用输出确定要为扩展安装程序运行的以下哪个命令 (可以运行多个) 。
* 如果列表包含 `azure-iot` ：你已有扩展。 运行此命令以确保具有最新更新：

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* 如果列表**不**包含 `azure-iot` ：你需要安装扩展。 使用此命令：

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* 如果列表包含 `azure-iot-cli-ext` ：这是扩展的旧版本。 一次只能安装一个版本的扩展，因此应卸载旧扩展。 使用此命令：

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

现在 Azure 数字孪生已可在 Cloud Shell 中使用。

若要验证 Azure 数字孪生在 Cloud Shell 中的就绪性，可以随时运行 `az dt -h` 查看可用的顶层 Azure 数字孪生命令列表。