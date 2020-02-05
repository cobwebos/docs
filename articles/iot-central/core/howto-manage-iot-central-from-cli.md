---
title: 从 Azure CLI 管理 IoT Central |Microsoft Docs
description: 本文介绍如何使用 CLI 创建和管理 IoT Central 应用程序。 可以使用 CLI 查看、修改和删除应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8526eb50faf300892c66ac186eac25adecf62231
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019020"
---
# <a name="manage-iot-central-from-azure-cli"></a>从 Azure CLI 管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

你可以使用[Azure CLI](/cli/azure/)来管理你的应用程序，而不是在[Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建和管理 IoT Central 应用程序。

## <a name="prerequisites"></a>必备组件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果希望在本地计算机上运行 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 在本地运行 Azure CLI 时，请在尝试本文中的命令之前，使用**az login**命令登录到 Azure。

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
  --sku ST1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

这些命令首先在 "美国东部" 区域中为应用程序创建一个资源组。 下表描述了用于**az iotcentral app create**命令的参数：

| 参数         | Description |
| ----------------- | ----------- |
| resource-group    | 包含该应用程序的资源组。 此资源组必须已存在于订阅中。 |
| location          | 默认情况下，此命令使用来自资源组的位置。 目前，你可以在**美国东部**、**美国西部**、**北欧**或**西欧**地区或在**澳大利亚**或**亚太**地区创建 IoT Central 应用程序。 |
| name              | 应用程序在 Azure 门户中的名称。 |
| 域名         | 应用程序 URL 中的子域。 在该示例中，应用程序 URL 为 https://mysubdomain.azureiotcentral.com 。 |
| sku               | 目前，可以使用**ST1**或**ST2**。 请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。 |
| template          | 要使用的应用程序模板。 有关详细信息，请参阅下表： |
| display-name      | UI 中显示的应用程序名称。 |

**应用程序模板**

| 模板名            | Description |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 创建一个空的应用程序，以便在其中填充你自己的设备模板和设备。
| iotc-pnp-preview@1.0.0   | 创建一个空的即插即用（预览版）应用程序，以便使用自己的设备模板和设备进行填充。 |
| iotc-condition@1.0.0     | 使用 "存储内分析-条件监视" 模板创建应用程序。 使用此模板来连接和监视存储环境。 |
| iotc-consumption@1.0.0   | 使用水消耗监视模板创建应用程序。 使用此模板来监视和控制水源。 |
| iotc-distribution@1.0.0  | 使用数字分发模板创建应用程序。 使用此模板可以通过 digitalizing 密钥资产和操作来改善仓库输出效率。 |
| iotc-inventory@1.0.0     | 创建具有智能库存管理模板的应用程序。 使用此模板自动进行接收、产品移动、循环计数和传感器跟踪。 |
| iotc-logistics@1.0.0     | 使用连接的后勤模板创建应用程序。 使用此模板，通过位置和条件监视在无线、水和陆地之间实时跟踪货物。 |
| iotc-meter@1.0.0         | 使用智能计量监视模板创建应用程序。 使用此模板来监视能源消耗、网络状态，并识别趋势以改善客户支持和智能计量管理。  |
| iotc-patient@1.0.0       | 使用持续患者监视模板创建应用程序。 使用此模板可扩展病人护理、重新招生和管理疾病。 |
| iotc-power@1.0.0         | 使用阳历面板监视模板创建应用程序。 使用此模板可监视阳历状态、能源生成趋势。 |
| iotc-quality@1.0.0       | 使用水源质量监视模板创建应用程序。 使用此模板以数字方式监视水源质量。|
| iotc-store@1.0.0         | 使用 "应用商店内分析–签出" 模板创建应用程序。 使用此模板来监视和管理你的商店内的结帐流。 |
| iotc-waste@1.0.0         | 使用连接垃圾桶管理模板创建应用程序。 使用此模板监视废箱和调度字段运算符。 |

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

现在，你已了解如何从 Azure CLI 管理 Azure IoT Central 应用程序，下面是建议的后续步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)
