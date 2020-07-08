---
title: Oracle WebLogic Server Azure 应用 | Microsoft Docs
description: 了解如何在 Microsoft Azure 上运行 Oracle WebLogic Server。
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664045"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Oracle WebLogic Server Azure 应用

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>Oracle WebLogic Server 是可缩放的、可用于企业的 Java EE 应用服务器。

Oracle WebLogic Server 是世界上第一个用于开发和部署多层分布式企业应用的云原生企业 Java 平台应用服务器。 Azure WebLogic Server 产品/服务为 WebLogic 迁移提供了更大的选择和灵活性，包括以最少的工作量和最大的影响将 Java EE 应用直接迁移到 Azure 云，从而让你能够欣然接受云计算。 通过自动预配虚拟网络、存储和 Linux 资源、安装 WebLogic Server、使用网络安全组设置安全机制、使用 Azure 应用程序网关进行负载均衡、使用 Azure Active Directory 进行身份验证和简化数据库连接，这些产品/服务让你能够快速启动商业应用。

有四种产品/服务可用于满足不同的方案：不含管理服务器的单节点、含管理服务器的单节点、群集和动态群集。  随时都可以试用这些产品/服务，它们是免费的。

这些产品/服务自带许可。 它们假设你已经从 Oracle 获得了适当的许可证，并且得到了在 Microsoft Azure 中运行这些产品/服务的适当许可。

若要与开发这些产品/服务的工程团队密切合作来实现你的迁移方案，只需点击 [Azure 市场](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)中的[“联系我”](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)按钮。 计划经理、架构师和工程师很快就会回复你并开始协作！

### <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 单节点

此产品/服务预配一个虚拟机，并在其上安装 Oracle WebLogic Server。 它不会创建域或启动管理服务器。 这对于具有高度自定义的域配置的方案非常有用。

### <a name="oracle-weblogic-server-with-admin-server"></a>含管理服务器的 Oracle WebLogic Server

此产品/服务预配一个虚拟机，并在其上安装 Oracle WebLogic Server。 它会创建域并启动管理服务器，这样你就可以管理域了。

### <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 群集

此产品/服务创建 Oracle WebLogic Server 虚拟机的高度可用群集。 管理服务器和所有托管服务器默认启动，这样你就可以管理域了。

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 动态群集

此产品/服务创建 Oracle WebLogic Server 虚拟机的高度可用且可缩放的动态群集。 管理服务器和所有托管服务器默认启动，这样你就可以管理域了。

## <a name="next-steps"></a>后续步骤

在 Azure 市场中探索这些产品/服务。

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 单节点](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [含管理服务器的 Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 群集](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 动态群集](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
