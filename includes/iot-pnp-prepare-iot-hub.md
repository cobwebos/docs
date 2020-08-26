---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions
ms.openlocfilehash: adc621f932462422202d9f16fd539f5ecc7c3d8e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87336884"
---
## <a name="prepare-an-iot-hub"></a>准备 IoT 中心

Azure 订阅中需要有一个 Azure IoT 中心才能完成本文中的步骤。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果要在本地使用 Azure CLI，请先使用 `az login` 登录到 Azure 订阅。 如果在 Azure Cloud Shell 中运行这些命令，则会自动登录。

如果在本地使用 Azure CLI，则 `az` 的版本应为 2.8.0 或更高版本；Azure Cloud Shell 使用最新版本。 使用 `az --version` 命令检查计算机上安装的版本。

运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到你的实例：

```azurecli-interactive
az extension add --name azure-iot
```

如果还没有可使用的 IoT 中心，请运行以下命令，以在订阅中创建资源组和免费层的 IoT 中心。 将 `<YourIoTHubName>` 替换为所选的中心名称：

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

> [!NOTE]
> IoT 即插即用当前在美国中部、北欧和日本东部区域创建的 IoT 中心中提供。 基本层的 IoT 中心不包含 IoT 即插即用支持。

运行以下命令，在 IoT 中心创建设备标识。 将 `<YourIoTHubName>` 和 `<YourDeviceID>` 占位符替换为你自己的“IoT 中心名称”和所选“设备 ID” 。

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
