---
title: 以编程方式管理 IoT Central |Microsoft Docs
description: 本文介绍如何以编程方式创建和管理 IoT Central。 可以使用多种语言的 SDK（例如 JavaScript、Python、C#、Ruby 和 Go）来查看、修改和删除应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/19/2020
ms.topic: how-to
ms.openlocfilehash: ba0ee0a610299bbe6b7e550f204cd2fd50d6d71a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748626"
---
# <a name="manage-iot-central-programmatically"></a>以编程方式管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

可以使用 Azure SDK 以编程方式管理应用程序，而不是在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建和管理 IoT Central 应用程序。 支持的语言包括 JavaScript、Python、C#、Ruby 和 Go。

## <a name="install-the-sdk"></a>安装 SDK

下表列出了 SDK 存储库和包安装命令：

| SDK 存储库 | 包安装 |
| -------------- | ------------ |
| [适用于 JavaScript 的 Azure IotCentralClient SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [适用于 Python 的 Microsoft Azure SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [用于 .NET 的 Azure SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [适用于 Ruby 的 Microsoft Azure SDK - 资源管理（预览版）](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Maven 包](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [包版本](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>示例

[Azure IoT Central ARM SDK 示例](https://docs.microsoft.com/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/)存储库提供多种编程语言的代码示例，这些示例演示如何创建、更新、列出和删除 Azure IoT Central 应用程序。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何以编程方式管理 Azure IoT Central 应用程序，建议接下来了解有关 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)服务的详细信息。
