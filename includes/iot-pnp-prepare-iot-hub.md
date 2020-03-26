---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "80234175"
---
## <a name="prepare-an-iot-hub"></a>准备 IoT 中心

Azure 订阅中还需要有一个 Azure IoT 中心才能完成本快速入门。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 如果你没有 IoT 中心，请遵照[此处的说明创建一个](../articles/iot-hub/iot-hub-create-using-cli.md)。

如果要在本地使用 Azure CLI，请先使用 `az login` 登录到 Azure 订阅。 如果在 Azure Cloud Shell 中运行这些命令，则会自动登录。

如果在本地使用 Azure CLI，则 `az` 的版本应为 2.0.73 或更高版本；Azure Cloud Shell 使用最新版本  。 使用 `az --version` 命令检查计算机上安装的版本。

运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到你的实例：

```azurecli-interactive
az extension add --name azure-iot
```

运行以下命令，在 IoT 中心创建设备标识。 将 YourIoTHubName 和 YourDeviceID 占位符替换为你自己的“IoT 中心名称”和所选“设备 ID”     。

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

运行以下命令，获取刚才注册的设备的_设备连接字符串_（记录以供稍后使用）：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
