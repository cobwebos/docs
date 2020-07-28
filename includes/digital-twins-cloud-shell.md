---
author: baanders
description: Azure 数字孪生的包含文件 - 设置 Cloud Shell 和 IoT 扩展
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032168"
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

首先，运行此命令来查看已安装的所有扩展的列表。

```azurecli-interactive
az extension list
```

输出是你当前拥有的所有扩展的数组。 查找每个列表项的 `"name"` 字段，以查看扩展的名称。

使用输出确定要为扩展安装程序运行的以下哪个命令（可以运行多个）。
* 如果列表包含 `azure-iot`：你已拥有该扩展。 运行此命令以确保你拥有最新更新且没有其他更新可用：

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* 如果列表不包含 `azure-iot`：需要安装扩展。 使用此命令：

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* 如果列表包含 `azure-iot-cli-ext`：这是该扩展的旧版本。 一次只能安装该扩展的一个版本，因此应卸载旧扩展。 使用此命令：

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

现在 Azure 数字孪生已可在 Cloud Shell 中使用。

若要验证 Azure 数字孪生在 Cloud Shell 中的就绪性，可以随时运行 `az dt -h` 查看可用的顶层 Azure 数字孪生命令列表。