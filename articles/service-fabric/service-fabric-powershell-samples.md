---
title: Azure PowerShell 示例 - Service Fabric | Microsoft Docs
description: Azure PowerShell 示例 - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 04/09/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 92dbdf2d2e3bdc48cda9ef0e0a53212cc7ea4cb7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="azure-powershell-samples"></a>Azure PowerShell 示例

下表包含用于创建和管理 Service Fabric 群集、应用程序和服务的 PowerShell 脚本示例的链接。

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **创建群集** ||
| [创建群集 (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| 创建 Azure Service Fabric 群集。 |
|[创建测试群集 (Azure)](./scripts/service-fabric-powershell-create-test-cluster.md)| 在 Azure 上创建三节点测试 Service Fabric 群集。|
| **管理群集、节点和基础结构** ||
| [添加应用程序证书](./scripts/service-fabric-powershell-add-application-certificate.md)| 将应用程序 X.509 证书添加到群集中的所有节点。 |
| [更新群集 VM 上的 RDP 端口范围](./scripts/service-fabric-powershell-change-rdp-port-range.md)|更改已部署群集中群集节点 VM 上的 RDP 端口范围。|
| [更新群集节点 VM 的管理员用户和密码](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | 更新群集节点 VM 的管理员用户名和密码。 |
| [打开负载均衡器的端口](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | 在 Azure 负载均衡器中打开应用程序端口，以允许特定端口上的入站流量。 |
| [创建入站网络安全组规则](./scripts/service-fabric-powershell-add-nsg-rule.md) | 创建入站网络安全组规则，以允许入站流量发往特定端口上的群集。 |
| **管理应用程序** ||
| [部署应用程序](./scripts/service-fabric-powershell-deploy-application.md)| 将应用程序部署到群集。|
| [升级应用程序](./scripts/service-fabric-powershell-upgrade-application.md)| 升级应用程序。|
| [删除应用程序](./scripts/service-fabric-powershell-remove-application.md)| 从群集中删除应用程序。|
