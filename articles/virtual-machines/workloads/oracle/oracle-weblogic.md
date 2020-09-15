---
title: Azure 上的 Oracle WebLogic 服务器解决方案
description: 了解如何在 Microsoft Azure 上运行 Oracle WebLogic Server。
services: virtual-machines-linux
documentationcenter: ''
author: rezar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: rezar
ms.openlocfilehash: e408f9e245fb78b475a194bc0db6f1edfdf85b41
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069709"
---
# <a name="solutions-for-running-oracle-weblogic-server-on-azure"></a>在 Azure 上运行 Oracle WebLogic Server 的解决方案

本页介绍了在 Azure 虚拟机上运行 Oracle WebLogic Server (WLS) 的解决方案。 这些解决方案由 Oracle 和 Microsoft 共同开发。

WLS 是一台领先的 Java 应用程序服务器，可在全球范围内运行一些最重要的关键企业 Java 应用程序。 WLS 构成了 Oracle 软件套件的中间件基础。 Oracle 和 Microsoft 致力于让 WLS 的客户在 Azure 上运行工作负荷作为领先的云平台，从而实现选择和灵活性。

Azure WLS 解决方案旨在使其尽可能轻松地将 Java EE 应用程序直接迁移到 Azure 虚拟机，并自动执行大部分样板操作。 解决方案自动预配虚拟网络、存储、Java 和 Linux 资源。 只需极少的工作量，就能安装 WebLogic Server。 这些解决方案可以使用网络安全组设置安全性，使用 Azure 应用网关进行负载平衡，并使用 Azure Active Directory 进行身份验证。 你还可以在 Oracle 云或 Azure 上自动连接到现有数据库，包括 Azure PostgreSQL、Azure SQL 和 Oracle DB。 解决方案的路线图包括能够通过 Oracle 一致性启用分布式日志记录和分布式缓存。 Microsoft 和 Oracle 建立了合作，实现 WebLogic 和 Azure Kubernetes Service (AKS) 的类似功能。

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="你可以使用 Azure 门户在 Azure 上部署 WebLogic 服务器":::

有四种产品可用于满足不同的方案：单一节点无管理服务器、具有管理服务器、群集和动态群集的单节点。 提供免费提供。 下面介绍和链接这些产品/服务。

这些产品/服务自带许可。 它们假设你已有合适的 Oracle 许可证，并获得在 Azure 中运行产品/服务的适当许可。

此产品/服务支持一系列的操作系统、Java 和 WLS 版本， (例如 Oracle Linux 7.6) 上的 WebLogic Server 14 和 JDK 11。 这些基本映像还可在 Azure 上自行使用。 基本映像适用于需要复杂的自定义 Azure 部署的客户。 [此处](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1)提供当前的基本映像集。

_如果你对开发这些产品/服务的工程团队有兴趣密切关注你的迁移方案，请[CONTACT ME](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _在[marketplace 产品概述页](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)上选择 "联系我"。 计划经理、架构师和工程师将尽快进入你，并开始密切合作。 在产品/服务处于积极开发阶段时，可以免费开发迁移方案。

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 单节点

此产品/服务预配一个虚拟机，并在其上安装 WLS。 它不创建域或启动管理服务器。 单节点提供适用于具有高度自定义域配置的方案。

## <a name="oracle-weblogic-server-with-admin-server"></a>含管理服务器的 Oracle WebLogic Server

此产品/服务预配一个虚拟机，并在其上安装 WLS。 它将创建一个域并启动管理服务器。 你可以立即管理域并立即开始应用程序部署。

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 群集

此产品/服务可创建 WLS 虚拟机的高度可用群集。 默认情况下，将启动管理服务器和所有托管服务器。 你可以立即管理群集并开始使用高度可用的应用程序。

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 动态群集

此产品/服务可创建 WLS 虚拟机的高度可用且可缩放的动态群集。 默认情况下，将启动管理服务器和所有托管服务器。

这些解决方案可以相对轻松地启用各种生产就绪部署体系结构。 你可以通过允许关注业务应用程序开发，以最高效的方式满足大多数迁移案例。

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="在 Azure 上启用了复杂的 WebLogic 服务器部署":::

除了解决方案自动设置的功能之外，客户还可以灵活地进一步自定义部署。 部署应用程序很可能会在部署应用程序时将更多 Azure 资源与部署进行集成。 建议客户提供进一步改进解决方案的反馈。

## <a name="next-steps"></a>后续步骤

了解 Azure 上的产品/服务。

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 单节点](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [含管理服务器的 Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 群集](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 动态群集](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
