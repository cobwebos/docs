---
title: 使用模板创建 Azure IoT 中心 (PowerShell) | Microsoft Docs
description: 如何使用 Azure 资源管理器模板创建包含 Azure PowerShell 的 IoT 中心。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976625"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>使用 Azure 资源管理器模板创建 IoT 中心 (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

了解如何使用 Azure 资源管理器模板创建 IoT 中心和使用者组。 Resource Manager 模板为 JSON 文件，用于定义针对解决方案进行部署时所需的资源。 有关开发资源管理器模板的详细信息，请参阅[Azure 资源管理器文档](https://docs.microsoft.com/azure/azure-resource-manager/)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

本快速入门中使用的资源管理器模板来自[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/)。 下面是该模板的副本：

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

模板创建包含三个终结点（eventhub、云到设备和消息传递）和使用者组的 Azure Iot 中心。 有关更多模板示例，请参阅[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)。 可在[此处](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions)找到 Iot 中心模板架构。

部署模板有多种方法。  在本教程中，您将使用 Azure PowerShell。

若要运行 PowerShell 脚本，请选择 "**尝试**" 以打开 Azure Cloud shell。 若要粘贴脚本，请右键单击 shell，然后选择 "粘贴"：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

从 PowerShell 脚本可以看出，使用的模板来自 Azure 快速入门模板。 若要使用自己的，需要首先将模板文件上传到 Cloud shell，然后使用 `-TemplateFile` 开关指定文件名。  有关示例，请参阅[部署模板](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)。

## <a name="next-steps"></a>后续步骤

现在，你已使用 Azure 资源管理器模板部署了 IoT 中心，你可能需要进一步浏览：

* 了解[IoT 中心资源提供程序][lnk-rest-api]的功能 REST API。
* 阅读[azure 资源管理器概述][lnk-azure-rm-overview]，了解有关 Azure 资源管理器功能的详细信息。
* 有关要在模板中使用的 JSON 语法和属性，请参阅 [Microsoft.Devices 资源类型](/azure/templates/microsoft.devices/iothub-allversions)。

若要详细了解如何开发 IoT 中心，请参阅以下文章：

* [C SDK 简介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 将 AI 部署到边缘设备][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
