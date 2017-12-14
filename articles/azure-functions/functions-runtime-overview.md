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
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 557f071e2cd8d4f639c881274e6e74a8fb745859
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="azure-functions-runtime-overview"></a>Azure Functions 运行时概述

Azure Functions 运行时提供了一种新方法供你用来在本地利用 Azure Functions 编程模型的简单性和灵活性。 Azure Functions 运行时基于与 Azure Functions 相同的开源代码根而构建，并且部署在本地来提供与云服务几乎完全相同的部署体验。

![Azure Functions 运行时预览版门户][1]

Azure Functions 运行时提供了一种方法供你用来在提交到云之前体验 Azure Functions。 这样，构建的代码资产可以在迁移时随你迁移到云。  运行时还提供了新选择，例如使用本地计算机的备用计算能力在夜间运行批处理。 还可以使用组织中的设备来有条件地向本地和云中的其他系统发送数据。

Azure Functions 运行时由两部分组成：

* Azure Functions 运行时管理角色
* Azure Functions 运行时辅助角色

## <a name="azure-functions-management-role"></a>Azure Functions 管理角色

Azure Functions 管理角色提供了用于管理本地 Functions 的宿主。 此角色执行以下任务：

* 托管 Azure Functions 管理门户，该门户与你在 [Azure 门户](https://portal.azure.com)中看到的是同一个。 该门户提供一致的体验，可让你以在 Azure 门户中的相同方式来开发函数。
* 将函数分布到多个 Functions 辅助角色中。
* 提供发布终结点，以便可以直接通过下载和导入发布配置文件从 Microsoft Visual Studio 发布函数。

## <a name="azure-functions-worker-role"></a>Azure Functions 辅助角色

Azure Functions 辅助角色部署在 Windows 容器中，它是执行函数代码的地方。  可以在整个组织中部署多个辅助角色，此选项是客户可以利用备用计算能力的主要方式。  许多组织中存在备用计算能力的一个示例是一直通电但很长时间未使用的计算机。

## <a name="minimum-requirements"></a>最低要求

若要开始使用 Azure Functions 运行时，必须具有运行 Windows Server 2016 或 Windows 10 Creators Update 且能够访问 SQL Server 实例的计算机。

## <a name="next-steps"></a>后续步骤

安装 [Azure Functions 运行时预览版](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
