---
title: "Azure 堆栈开发工具包基础知识 |Microsoft 文档"
description: "描述如何执行基本管理的 Azure 堆栈开发工具包。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: cb169c2d2a5aa918fb6d330ebc4677d6c16d308d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="asdk-administration-basics"></a>ASDK 管理基础知识 
有几种方法，你需要知道是否你不熟悉 Azure 堆栈开发工具包 (ASDK) 管理。 本指南提供的作为在评估环境中，Azure 堆栈操作员角色的概述和如何确保你的测试用户快速可以提高工作效率。

首先，你应查看[什么是 Azure 堆栈开发工具包？](asdk-what-is.md)文章，以确保你了解 ASDK 和其自身的局限性的用途。 你应使用的开发工具包作为"沙盒"，你可以在其中评估 Azure 堆栈，以开发和测试你的应用在非生产环境中。 

如 Azure，Azure 堆栈快速 innovates 因此我们将定期发布新生成的 ASDK。 但是，不能升级 ASDK 集成的 Azure 堆栈系统部署一样。 因此，如果你想要将移动到的最新版本，则必须完全[重新部署 ASDK](asdk-redeploy.md)。 不能应用更新包。 此过程需要时间，但好处是，你还可以尝试的最新功能变得可用时，就会立即。 

## <a name="what-tools-do-i-use-to-manage"></a>使用哪些工具进行管理？
你可以使用[Azure 堆栈管理员门户](https://adminportal.local.azurestack.external)或 PowerShell 来管理 Azure 堆栈。 若要了解基本概念，最简单的方式是使用门户。 如果你想要使用 PowerShell，你需要安装[PowerShell for Azure 堆栈](asdk-post-deploy.md#install-azure-stack-powershell)和[从 GitHub 下载 Azure 堆栈工具](asdk-post-deploy.md#download-the-azure-stack-tools)。

Azure Stack 使用 Azure 资源管理器作为其基础的部署、管理和组织机制。 如果你要管理 Azure 堆栈和帮助支持用户，你应该了解有关 Azure 资源管理器。 你可以了解详细信息，请阅读[Getting Started with Azure 资源管理器白皮书](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)。

## <a name="your-typical-responsibilities"></a>典型责任
用户需要使用服务， 从其角度来看，你的主要角色是向他们提供这些服务。 使用 ASDK，你可以了解哪些服务提供，和如何使这些服务可通过[创建计划、 服务和配额](asdk-offer-services.md)。 你还需要将项添加到应用商店，如虚拟机映像。 最简单方法是到[下载应用商店项](asdk-marketplace-item.md)从 Azure 到 Azure 堆栈。

> [!NOTE]
> 如果你想要测试计划、 服务和服务，则应使用[用户门户](https://portal.local.azurestack.external); 不[管理员门户](https://adminportal.local.azurestack.external)。

除了提供服务，必须执行的所有正则职责的 Azure 堆栈运算符需要 ASDK 启动并正在运行。 这些任务包括如下所示的内容：
- 为 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 部署中添加用户帐户。
- 分配基于角色的访问控制 (RBAC) 角色 （这并不局限于不仅限于管理员）
- 监视基础结构运行状况
- 管理网络和存储资源
- 替换失败的开发工具包主机计算机硬件 

## <a name="where-to-get-support"></a>从何处获取支持
对于开发工具包中，唯一支持的选项是支持相关在中提问[Microsoft Azure 堆栈论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)。 如果单击管理员门户右上角的“帮助和支持”图标（问号），然后单击“新建支持请求”，则会直接打开论坛站点。 我们会定期关注这些论坛。 

> [!IMPORTANT]
> 由于 ASDK 是评估环境，没有提供通过 Microsoft 客户支持服务 (CSS) 正式支持。

## <a name="next-steps"></a>后续步骤
[部署 ASDK](asdk-deploy.md)

