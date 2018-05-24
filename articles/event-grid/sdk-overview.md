---
title: Azure 事件网格 SDK
description: 介绍 Azure 事件网格的 SDK。 这些 SDK 提供管理、发布和使用功能。
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 82f08341f8c96695a4ceddb19d1b610d70b89f88
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>用于管理和发布的事件网格 SDK

使用事件网格提供的 SDK 可以编程方式管理资源和发布事件。

## <a name="management-sdks"></a>管理 SDK

使用管理 SDK 可以创建、更新和删除事件网格主题和订阅。 目前有以下 SDK 可用：

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure.eventgrid-2018-01-01/azure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>数据平面 SDK

使用数据平面 SDK，可以通过处理身份验证、形成事件并以异步方式发布到指定终结点来将事件发布到主题。 还可以通过这些 SDK 使用第一方事件。 目前有以下 SDK 可用：

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[什么是事件网格？](overview.md)
* 有关 Azure CLI 中的事件网格命令，请参阅 [Azure CLI](/cli/azure/eventgrid)。
* 有关 PowerShell 中的事件网格命令，请参阅 [PowerShell](/powershell/module/azurerm.eventgrid)。