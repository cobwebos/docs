---
title: 将 API 从 Azure API 管理导出到 Power Platform | Microsoft Docs
description: 了解如何将 API 从 API 管理导出到 Power Platform。
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 9af20972a47e2d0ad20de62f1bb9d10e4d43563c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82702648"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>将 API 从 Azure API 管理导出到 Power Platform 

使用 Microsoft [Power Platform](https://powerplatform.microsoft.com) 的平民开发者经常需要访问由专业开发者开发并部署在 Azure 中的业务功能。 使用 [Azure API 管理](https://aka.ms/apimrocks)，专业开发者可以将其后端服务发布为 API，并轻松地将这些 API 作为自定义连接器导出到 Power Platform（Power Apps 和 Power Automate）供平民开发者使用。 

本文将引导你完成将 API 从 API 管理导出到 Power Platform 的步骤。 

## <a name="prerequisites"></a>先决条件

+ 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)
+ 确保 API 管理实例中存在要导出到 Power Platform 的 API
+ 确保有一个 Power Apps 或 Power Automate [环境](https://docs.microsoft.com/powerapps/powerapps-overview#power-apps-for-admins) 

## <a name="export-an-api"></a>导出 API

1. 在 Azure 门户中导航到 API 管理服务，然后从菜单中选择“API”  。
2. 单击要导出的 API 旁边的三个点。 
3. 选择“导出”。 
4. 选择“Power Apps 和 Power Automate”  。
5. 选择要将 API 导出到其中的环境。 
6. 提供一个显示名称，该名称将用作自定义连接器的名称。  
7. （可选）如果 API 受 OAuth 2.0 服务器保护，则还需提供其他详细信息，包括 `Client ID`、`Client secret`、`Authorization URL`、`Token URL` 和 `Refresh URL`。  
8. 选择“导出”。  

在导出完成后，导航到你的 Power App 或 Power Automate 环境。 此时会看到该 API 显示为自定义连接器。

## <a name="next-steps"></a>后续步骤

* [详细了解 Power Platform](https://powerplatform.microsoft.com/)
* [根据教程了解 API 管理中的常用任务](https://docs.microsoft.com/azure/api-management/import-and-publish)