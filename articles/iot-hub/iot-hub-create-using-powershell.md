---
title: 使用 PowerShell cmdlet 创建 Azure IoT 中心 | Microsoft Docs
description: 如何使用 PowerShell cmdlet 创建 IoT 中心。
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: e7dbfabadf72b8abece02907cd16644ef95e67a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>使用 New-AzureRmIotHub cmdlet 创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>介绍

可以使用 Azure PowerShell cmdlet 创建和管理 Azure IoT 中心。 本教程介绍如何使用 PowerShell 创建 IoT 中心。

> [!NOTE]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Azure 资源管理器和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍了如何使用 Azure 资源管理器部署模型。

要完成本教程，需要以下各项：

* 有效的 Azure 帐户。 <br/>如果没有帐户，只需几分钟即可创建一个[免费帐户][lnk-free-trial]。
* [Azure PowerShell cmdlet][lnk-powershell-install]。

## <a name="connect-to-your-azure-subscription"></a>连接到 Azure 订阅
在 PowerShell 命令提示符中，输入以下命令以登录 Azure 订阅：

```powershell
Connect-AzureRmAccount
```

如果有多个 Azure 订阅，则访问 Azure 即有权访问与凭据关联的所有 Azure 订阅。 使用以下命令，列出可供使用的 Azure 订阅：

```powershell
Get-AzureRMSubscription
```

使用以下命令，选择想要用于运行命令以创建 IoT 中心的订阅。 可使用上一命令输出中的订阅名称或 ID：

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

## <a name="create-resource-group"></a>创建资源组

需要一个资源组来部署 IoT 中心。 可以使用现有资源组，也可以创建新组。

可以使用以下命令来搜索可以部署 IoT 中心的位置：

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

若要在 IoT 中心的其中一个支持位置中为 IoT 中心创建资源组，请使用以下命令。 此示例在“美国东部”区域中创建名为 **MyIoTRG1** 的资源组：

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>创建 IoT 中心

若要在上一步创建的资源组中创建 IoT 中心，请使用以下命令。 此示例在“美国东部”区域中创建名为 **MyTestIoTHub** 的 **S1** 中心：

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

IoT 中心的名称必须是唯一的。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


可以使用以下命令列出订阅中的所有 IoT 中心：

```powershell
Get-AzureRmIotHub
```

上一个示例添加向你计费的 S1 标准 IoT 中心。 可以使用以下命令删除 IoT 中心：

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

或者，可以使用以下命令删除资源组及其包含的所有资源：

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>后续步骤

现在，已使用 PowerShell cmdlet 部署了 IoT 中心，接下来可以进一步进行探索：

* 发现其他[可用于 IoT 中心的 PowerShell cmdlet][lnk-iothub-cmdlets]。
* 阅读了解 [IoT 中心资源提供程序 REST API][lnk-rest-api] 的相关功能。

若要详细了解如何开发 IoT 中心，请参阅以下文章：

* [C SDK 简介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 将 AI 部署到边缘设备][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
