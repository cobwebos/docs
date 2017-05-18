---
title: "Azure Functions 运行时概述 | Microsoft Docs"
description: "Azure Functions 运行时预览版概述"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d9e655b6ebbdb02bae21fa7a8fa7ee5b46d6e63b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---

# <a name="azure-functions-runtime-overview"></a>Azure Functions 运行时概述

Azure Functions 运行时提供了一种新方法供你用来在本地利用 Azure Functions 编程模型的简单性和灵活性。 Azure Functions 运行时基于与 Azure Functions 相同的开源代码根而构建，并且部署在本地来提供与云服务几乎完全相同的部署体验。

![Azure Functions 运行时预览版门户][1]

Azure Functions 运行时提供了一种方法供你用来在提交到云之前体验 Azure Functions。 这样，你构建的代码资产可以在你迁移时随你迁移到云。  运行时还为你提供了新选择，例如使用你的本地计算机的备用计算能力在夜间运行批处理。 你还可以使用你的组织中的设备来有条件地向本地和云中的其他系统发送数据。

Azure Functions 运行时由两部分组成：
* Azure Functions 运行时管理角色
* Azure Functions 运行时辅助角色

## <a name="azure-functions-management-role"></a>Azure Functions 管理角色

Azure Functions 管理角色提供了用于管理本地 Functions 的宿主。 此角色执行以下任务：

* 托管 Azure Functions 管理门户，该门户与你在 [Azure 门户](https://portal.azure.com)中看到的是同一个。 这使得你可以采用与在 Azure 门户中相同的方式来部署函数。
* 将函数分布到多个 Functions 辅助角色中。
* 提供发布终结点，以便你可以直接从 Microsoft Visual Studio 发布函数。

## <a name="azure-functions-worker-role"></a>Azure Functions 辅助角色

Azure Functions 辅助角色部署在 Windows 容器中，这是执行你的函数代码的地方。  你可以在整个组织中部署多个辅助角色，并且这是客户可以利用备用计算能力的主要方式。

## <a name="minimum-requirements"></a>最低要求

若要开始使用 Azure Functions 运行时，必须具有运行 **Windows Server 2016 或 Windows 10 Creators Update** 且能够访问 **SQL Server** 实例的计算机。

## <a name="next-steps"></a>后续步骤

安装 [Azure Functions 运行时预览版](https://aka.ms/azafr)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png

