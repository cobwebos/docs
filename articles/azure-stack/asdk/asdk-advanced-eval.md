---
title: 高级 Azure 堆栈评估任务 |Microsoft 文档
description: 本指南介绍了高级的 Azure 堆栈评估任务。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c4bf76aa07ec5025d9e53b5518929199ace27e18
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
ms.locfileid: "29976349"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>高级的 Azure 堆栈开发工具包评估任务
已获得熟悉的基本 Azure 堆栈开发工具包 (ASDK) 服务特性和功能后，你可以通过更高级的方案测试深化 Azure 堆栈进一步了解。 这些更高级的评估任务均完全记录在 Azure 堆栈运算符文档。

> [!NOTE]
> 许多运算符任务支持 ASDK 和生产时，Azure 堆栈的多节点部署，而不是所有的使用方案支持 ASDK 部署。 请参阅[ASDK 和多节点 Azure 堆栈差异](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences)有关详细信息。

## <a name="delegate-offers-in-azure-stack"></a>在 Azure Stack 中委托产品/服务
作为 Azure 堆栈操作员，你通常想要将负责创建提议和注册用户的其他人。 例如，如果你的服务提供商，你可能想客户注册和管理它们代表你的经销商。 或者，如果你是企业的中心 IT 小组的成员，则可能需要下属公司在你不参与的情况下注册用户。

[委派 Azure 堆栈的产品/服务](.\.\azure-stack-delegated-provider.md)通过使访问和管理更多用户不是直接可以帮助您与这些任务。 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>将 SQL 数据库提供给 Azure Stack 用户使用
作为 Azure 堆栈运算符，你可以创建提供使你的用户 （租户） 创建它们可以使用其云本机应用程序、 网站和工作负荷的 SQL 数据库。 通过向用户提供这些基于云的按需自定义数据库的访问权限，可以节省用户的时间和资源。 

使用 SQL Server 资源提供程序适配器到[让 Azure 堆栈用户能够使用 SQL 数据库](.\.\azure-stack-tutorial-sql-server.md)作为 Azure 堆栈的服务。 安装资源提供程序之后你可以将其连接到一个或多个 SQL Server 实例。

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>向 Azure 堆栈用户提供 web 应用和 API 应用
作为 Azure 堆栈运算符，你可以创建提供使你的用户 （租户） 创建 Azure 函数和 web 应用程序和 API 的应用程序。 通过向用户提供这些基于云的按需应用程序的访问权限，可以节省用户的时间和资源。

部署到 App Service 资源提供程序[使 web 应用和 API 应用适用于 Azure 堆栈用户](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>后续步骤
[了解有关提供与 Azure 堆栈集成系统的服务的详细信息](.\.\azure-stack-offer-services-overview.md)