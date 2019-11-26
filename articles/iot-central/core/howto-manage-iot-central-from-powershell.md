---
title: 从 Azure PowerShell 管理 IoT Central | Microsoft Docs
description: This article describes how to create and manage your IoT Central applications from Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 506eb38a2844ed8e8eb9739b116d7647bc1810ec
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480281"
---
# <a name="manage-iot-central-from-azure-powershell"></a>从 Azure PowerShell 管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Instead of creating and managing IoT Central applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, you can use [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) to manage your applications.

## <a name="prerequisites"></a>必备组件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果你偏好于在本地计算机上运行 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。 在本地运行 Azure PowerShell 时，请使用 **Connect-AzAccount** cmdlet 登录到 Azure，然后尝试运行本文中所述的 cmdlet。

## <a name="install-the-iot-central-module"></a>安装 IoT Central 模块

运行以下命令来检查 PowerShell 环境中是否已安装 [IoT Central 模块](https://docs.microsoft.com/powershell/module/az.iotcentral/)：

```powershell
Get-InstalledModule -name Az.I*
```

如果已安装的模块列表不包括 **Az.IotCentral**，请运行以下命令：

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>创建应用程序

使用 [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet 在 Azure 订阅中创建 IoT Central 应用程序。 例如：

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

The script first creates a resource group in the east US location for the application. 下表描述了与 **New-AzIotCentralApp** 命令配合使用的参数：

|参数         |描述 |
|------------------|------------|
|ResourceGroupName |包含该应用程序的资源组。 此资源组必须已存在于订阅中。 |
|Location |此 cmdlet 默认使用资源组中的位置。 Currently, you can create an IoT Central application in the **United States**, **Australia**, **Asia Pacific**, or in the **Europe** locations.  |
|名称              |应用程序在 Azure 门户中的名称。 |
|子域         |应用程序 URL 中的子域。 在该示例中，应用程序 URL 为 https://mysubdomain.azureiotcentral.com 。 |
|Sku               |目前，唯一的值是 **S1**（标准层）。 请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。 |
|模板          | 要使用的应用程序模板。 有关详细信息，请参阅下表： |
|DisplayName       |UI 中显示的应用程序名称。 |

**Application templates with generally available features**

| 模板名            | 描述 |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 创建一个空的应用程序，以便在其中填充你自己的设备模板和设备。 |
| iotc-demo@1.0.0          | 创建一个应用程序，其中包含已为冷藏食品贩卖机创建的设备模板。 通过此模板来完成 Azure IoT Central 的入门。 |
| iotc-devkit-sample@1.0.0 | 创建一个应用程序，其中的设备模板可以用来连接 MXChip 或 Raspberry Pi 设备。 如果你是在其中任一设备上进行试验的设备开发人员，请使用此模板。 |


**Application templates with public preview features**

| 模板名            | 描述 |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Creates an empty plug and play preview application for you to populate with your own device templates and devices. |
| iotc-condition@1.0.0     | Creates an application with a in-store analytics – condition monitoring template. Use this template to connect and monitor store environment. |
| iotc-consumption@1.0.0   | Creates an application with water consumption monitoring template. Use this template to monitor and control water flow. |
| iotc-distribution@1.0.0  | Creates an application with a Digital distribution template. Use this template to improve warehouse output efficiency by digitalizing key assets and actions. |
| iotc-inventory@1.0.0     | Creates an application with a smart inventory management template. Use this template to automate receiving, product movement, cycle counting, and tracking of sensors. |
| iotc-logistics@1.0.0     | Creates an application with a Connected logistics template. Use this template to track your shipment in real-time across air, water and land with location and condition monitoring. |
| iotc-meter@1.0.0         | Creates an application with smart meter monitoring template. Use this template to monitor energy consumption, network status, and identify trends to improve customer support and smart meter management.  |
| iotc-patient@1.0.0       | Creates an application with continuous patient monitoring template. Use this template to extend patient care, re-admissions, and manage diseases. |
| iotc-power@1.0.0         | Creates an application with solar panel monitoring template. Use this template to monitor solar panel status, energy generation trends. |
| iotc-quality@1.0.0       | Creates an application with water quality monitoring template. Use this template to digitally monitor water quality.|
| iotc-store@1.0.0         | Creates an application with a in-store analytics – checkout template. Use this template to monitor and manage the checkout flow inside your store. |
| iotc-waste@1.0.0         | Creates an application with a Connected waste management template. Use this template to monitor waste bins and dispatch field operators. |

> [!NOTE]
> The preview application templates are currently only available in the **Europe** and **United States** locations.

## <a name="view-your-iot-central-applications"></a>查看 IoT Central 应用程序

使用 [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet 可列出 IoT Central 应用程序和查看元数据。

## <a name="modify-an-application"></a>修改应用程序

使用 [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet 可更新 IoT Central 应用程序的元数据。 例如，若要更改应用程序的显示名称：

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>删除应用程序

使用 [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet 可删除 IoT Central 应用程序。 例如：

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure PowerShell 管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)
