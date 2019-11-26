---
title: Manage IoT Central from Azure CLI | Microsoft Docs
description: This article describes how to create and manage your IoT Central application using CLI. You can view, modify and remove the application using CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c5622f32dbf849b9a21a1fd2e458f35b8aa1d098
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480383"
---
# <a name="manage-iot-central-from-azure-cli"></a>Manage IoT Central from Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Instead of creating and managing IoT Central applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, you can use [Azure CLI](/cli/azure/) to manage your applications.

## <a name="prerequisites"></a>必备组件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you prefer to run Azure CLI on your local machine, see [Install the Azure CLI](/cli/azure/install-azure-cli). When you run Azure CLI locally, use the **az login** command to sign in to Azure before you try the commands in this article.

## <a name="create-an-application"></a>创建应用程序

Use the [az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) command to create an IoT Central application in your Azure subscription. 例如：

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

These commands first create a resource group in the east US location for the application. The following table describes the parameters used with the **az iotcentral app create** command:

| 参数         | 描述 |
| ----------------- | ----------- |
| resource-group    | 包含该应用程序的资源组。 此资源组必须已存在于订阅中。 |
| 位置          | By default, this command uses the location from the resource group. Currently, you can create an IoT Central application in the **United States**, **Australia**, **Asia Pacific**, or in the **Europe** locations. |
| name              | 应用程序在 Azure 门户中的名称。 |
| subdomain         | 应用程序 URL 中的子域。 在该示例中，应用程序 URL 为 https://mysubdomain.azureiotcentral.com 。 |
| sku               | 目前，唯一的值是 **S1**（标准层）。 请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。 |
| 模板          | 要使用的应用程序模板。 有关详细信息，请参阅下表： |
| display-name      | UI 中显示的应用程序名称。 |

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

## <a name="view-your-applications"></a>查看应用程序

Use the [az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) command to list your IoT Central applications and view metadata.

## <a name="modify-an-application"></a>修改应用程序

Use the [az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update) command to update the metadata of an IoT Central application. 例如，若要更改应用程序的显示名称：

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>删除应用程序

Use the [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) command to delete an IoT Central application. 例如：

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>后续步骤

Now that you've learned how to manage Azure IoT Central applications from Azure CLI, here is the suggested next step:

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)
