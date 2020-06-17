---
author: baanders
description: Azure 数字孪生的包含文件 - 设置 Cloud Shell 和 IoT 扩展
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611469"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>设置 Cloud Shell 会话

在打开 Cloud Shell 窗口后，要做的第一件事是登录，并将 shell 上下文设置为此会话的订阅。 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

如果这是你首次将此订阅与 Azure 数字孪生一起使用，请运行该命令以向 Azure 数字孪生命名空间进行注册。 （如果不确定，可以再次运行该命令，即使过去某个时候操作过也是如此。）

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

然后，在 Cloud Shell 实例中运行以下命令，以添加用于 Azure CLI 的 Microsoft Azure IoT 扩展。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> 本文使用最新版本的 Azure IoT 扩展（称为 `azure-iot`）。 旧版本称为 `azure-iot-cli-ext`。只应同时安装有一个版本。 可以使用命令 `az extension list` 来验证当前安装的扩展。
> 使用 `az extension remove --name azure-cli-iot-ext` 可删除扩展的旧版本。
> 使用 `az extension add --name azure-iot` 可添加扩展的新版本。 若要查看已安装了哪些扩展，请使用 `az extension list`。

> [!TIP]
> 可以运行 `az dt -h` 以查看顶级 Azure 数字孪生命令。