---
title: "Azure 事件网格 SDK"
description: "介绍 Azure 事件网格的 SDK。 这些 SDK 提供管理、发布和使用功能。"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9c56e4c3314090ad55017d5c681a0cfd7bf5722c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>用于管理和发布的事件网格 SDK

使用事件网格提供的 SDK 可以编程方式管理资源和发布事件。

## <a name="management-sdks"></a>管理 SDK

使用管理 SDK 可以创建、更新和删除事件网格主题和订阅。 目前有以下 SDK 可用：

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="publish-sdks"></a>发布 SDK

使用发布 SDK，可以通过处理身份验证、形成事件并以异步方式发布到指定终结点来将事件发布到主题。 目前有以下 SDK 可用：

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[什么是事件网格？](overview.md)
* 有关 Azure CLI 中的事件网格命令，请参阅 [Azure CLI](/cli/azure/eventgrid)。
* 有关 PowerShell 中的事件网格命令，请参阅 [PowerShell](/powershell/module/azurerm.eventgrid)。