---
title: 从 Azure CLI 管理 IoT Central |Microsoft Docs
description: 从 Azure CLI 管理 IoT Central。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 021341406f1a7269aadf18eb3a748e539cbb5bf5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086961"
---
# <a name="manage-iot-central-from-azure-cli"></a>从 Azure CLI 管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

你可以使用[Azure CLI](/cli/azure/)来管理你的应用程序, 而不是在[Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建和管理 IoT Central 应用程序。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果希望在本地计算机上运行 Azure CLI, 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 在本地运行 Azure CLI 时, 请在尝试本文中的命令之前, 使用**az login**命令登录到 Azure。

## <a name="create-an-application"></a>创建应用程序

使用[az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create)命令在 Azure 订阅中创建 IoT Central 应用程序。 例如：

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

这些命令首先在 "美国东部" 区域中为应用程序创建一个资源组。 下表描述了用于**az iotcentral app create**命令的参数:

| 参数         | 描述 |
| ----------------- | ----------- |
| resource-group    | 包含该应用程序的资源组。 此资源组必须已存在于订阅中。 |
| location          | 默认情况下, 此命令使用来自资源组的位置。 目前，可以在**美国东部**、**美国西部**、**北欧**或**西欧**区域创建 IoT Central 应用程序。 |
| name              | 应用程序在 Azure 门户中的名称。 |
| 域名         | 应用程序 URL 中的子域。 在该示例中，应用程序 URL 为 https://mysubdomain.azureiotcentral.com 。 |
| sku               | 目前，唯一的值是 **S1**（标准层）。 请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。 |
| 模板          | 要使用的应用程序模板。 有关详细信息，请参阅下表： |
| display-name      | UI 中显示的应用程序名称。 |

**应用程序模板**

| 模板名称            | 描述 |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 创建一个空的应用程序，以便在其中填充你自己的设备模板和设备。 |
| iotc-demo@1.0.0          | 创建一个应用程序，其中包含已为冷藏食品贩卖机创建的设备模板。 通过此模板来完成 Azure IoT Central 的入门。 |
| iotc-devkit-sample@1.0.0 | 创建一个应用程序，其中的设备模板可以用来连接 MXChip 或 Raspberry Pi 设备。 如果你是在其中任一设备上进行试验的设备开发人员，请使用此模板。 |

## <a name="view-your-applications"></a>查看应用程序

使用[az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list)命令列出你的 IoT Central 应用程序并查看元数据。

## <a name="modify-an-application"></a>修改应用程序

使用[az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update)命令更新 IoT Central 应用程序的元数据。 例如，若要更改应用程序的显示名称：

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>删除应用程序

使用[az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete)命令删除 IoT Central 应用程序。 例如：

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何从 Azure CLI 管理 Azure IoT Central 应用程序, 下面是建议的后续步骤:

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)
