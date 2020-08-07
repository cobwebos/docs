---
title: 什么是 Azure 上的 Oracle WebLogic Server？
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
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851846"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>什么是 Azure 上的 Oracle WebLogic Server？

本页介绍了在 Azure 虚拟机上运行 WebLogic Server (WLS) 的解决方案。  这些解决方案由 Oracle 和 Microsoft 共同开发。

Oracle WebLogic Server 是世界上第一个用于开发和部署多层分布式企业应用的云原生企业 Java 平台应用服务器。 使用 Azure WebLogic 服务器产品/服务，可以接纳云计算。  你可以为 WebLogic 迁移提供更好的选择和灵活性，包括将 Java EE 应用程序直接迁移到 Azure 云。   Azure 上的 WLS 在一小段工作中产生了重大影响。 此产品/服务可让你快速开始 (LOB) 应用程序。  每个产品/服务自动预配虚拟网络、存储和 Linux 资源。  如果没有工作量，则安装 WebLogic Server。  Azure 上的 WLS 设置网络安全组的安全性，使用 Azure 应用网关进行负载平衡，使用 Azure Active Directory 进行身份验证，并自动连接到现有数据库。

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="可以使用 Azure 门户在 Azure 上部署 WebLogic 服务器":::

有四种产品可用于满足不同的方案：单一节点无管理服务器、具有管理服务器、群集和动态群集的单节点。  请尝试该产品/服务，免费提供。

这些产品/服务自带许可。 它们假设你已经拥有了 Oracle 的相应许可证，并获得了在 Microsoft Azure 中适当的许可证运行。

_如果要与开发这些产品的工程团队密切合作迁移方案，请在 Azure Marketplace 中选择 "[联系我](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)" 按钮_。 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) 计划经理、架构师和工程师将在你不久后再开始协作！

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 单节点

此产品/服务预配一个虚拟机，并在其上安装 WLS。 它不创建域或启动管理服务器。 单个节点对于具有高度自定义域配置的方案非常有用。

## <a name="oracle-weblogic-server-with-admin-server"></a>含管理服务器的 Oracle WebLogic Server

此产品/服务预配一个虚拟机，并在其上安装 WLS。 它会创建域并启动管理服务器，这样你就可以管理域了。

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 群集

此产品/服务可创建 WLS 虚拟机的高度可用群集。 管理服务器和所有托管服务器默认启动，这样你就可以管理域了。

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 动态群集

此产品/服务可创建 WLS 虚拟机的高度可用且可缩放的动态群集。 管理服务器和所有托管服务器默认启动，这样你就可以管理域了。

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
