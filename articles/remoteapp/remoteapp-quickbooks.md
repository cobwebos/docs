---
title: "在 Azure RemoteApp 中部署 QuickBooks | Microsoft Docs"
description: "了解如何与 Azure RemoteApp 共享 QuickBooks。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c5d00753-77c0-4f0d-a5df-9451b46a31d3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4015aede386913607df8a9499c0f08c414ee6959


---
# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>你如何在 Azure RemoteApp 中部署 QuickBooks？
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

使用以下信息将 QuickBooks 共享为 Azure RemoteApp 中的一个应用。

在混合或云集合中，你可以与 Azure RemoteApp 一起共享 QuickBooks 2015 Enterprise。 公司文件必须位于正在运行 QuickBooks 数据库服务器（该服务器与 Azure RemoteApp 服务器是分开的）的虚拟机上。 永远不要将公司文件存储在 Azure RemoteApp 映像上 - 如果这样做，数据会丢失。 仅 QuickBooks Enterprise 支持将 QuickBooks 文件托管在外部共享上，且 QuickBooks 数据库服务器可通过标准 Windows 网络进行访问。   

> [!IMPORTANT]
> 托管公司文件的 QuickBooks 数据库服务器必须位于与 Azure RemoteApp 集合相同的 VNET 内的一个单独虚拟机上。  
> 
> 

## <a name="steps-to-deploy-quickbooks"></a>部署 QuickBooks 的步骤
1. 创建 Azure 虚拟机并安装 QuickBooks、QuickBooks 数据库服务器，并将公司文件置于 Azure 虚拟机上。  请确保正确配置防火墙规则。
2. 在[自定义映像](remoteapp-imageoptions.md)上安装 QuickBooks，并在与托管 QuickBooks 数据库服务器和公司文件的虚拟机所在的 VNET 完全相同的 VNET 内创建一个 [Azure RemoteApp 集合](remoteapp-collections.md)（要么是云集合要么是混合集合）。 
3. 将 QuickBooks [发布](remoteapp-publish.md)给用户
4. 启动 Azure RemoteApp 托管的 QuickBooks 客户端，使用标准 Windows 网络连接导航到托管 QuickBooks 数据库服务器的虚拟机，然后打开公司文件。 

## <a name="documentation-references"></a>文档参考
* QuickBooks [支持的配置](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
* QuickBooks [部署选项](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

你还可以签出我 Ignite 演讲，[Microsoft Azure RemoteApp 管理和行政基础](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) - 快进到 1:02:45 以转到 QuickBooks 部分。

## <a name="deployment-architecture"></a>部署体系结构
![QuickBooks 和 Azure RemoteApp 部署](./media/remoteapp-quickbooks/ra-quickbooks.png)




<!--HONumber=Nov16_HO3-->


