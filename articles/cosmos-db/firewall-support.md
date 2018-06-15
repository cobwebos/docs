---
title: Azure Cosmos DB 防火墙支持和 IP 访问控制 | Microsoft Docs
description: 了解如何将 IP 访问控制策略用于 Azure Cosmos DB 数据库帐户上的防火墙支持。
keywords: IP 访问控制, 防火墙支持
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sngun
ms.openlocfilehash: f0cbbe147386aa5d50e207fdd9c86fd9571ec144
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611732"
---
# <a name="azure-cosmos-db-firewall-support"></a>Azure Cosmos DB 防火墙支持
为保护存储在 Azure Cosmos DB 数据库帐户的数据，Azure Cosmos DB 已提供对基于机密的[授权模型](https://msdn.microsoft.com/library/azure/dn783368.aspx)的支持，该模型利用强大的基于哈希的消息验证代码 (HMAC)。 现在，除了基于机密的授权模型以外，Azure Cosmos DB 还支持针对入站防火墙支持的基于 IP 的策略驱动访问控制。 此模型与传统数据库系统的防火墙规则类似，并且对 Azure Cosmos DB 数据库帐户提供了额外级别的安全性。 利用此模型，现可将 Azure Cosmos DB 数据库帐户配置为仅可从一组已批准的计算机和/或云服务进行访问。 从这些已批准的计算机和服务访问 Azure Cosmos DB 资源仍要求调用方提供有效的授权令牌。

> [!NOTE]
> 目前，Azure Cosmos DB SQL API 和 Mongo API 帐户支持使用防火墙。 不久之后，可为其他 API 和主权云（例如 Azure 德国版或 Azure 政府版）配置防火墙。 如果打算为配置了现有 IP 防火墙的 Azure Cosmos DB 帐户配置服务终结点 ACL，请记下防火墙配置，删除 IP 防火墙，然后配置服务终结点 ACL。 配置服务终结点后，可根据需要重新启用 IP 防火墙。

## <a name="ip-access-control-overview"></a>IP 访问控制概述
默认情况下，只要请求附有有效的授权令牌，便可从公共 Internet 访问 Azure Cosmos DB 数据库帐户。 要配置基于 IP 策略的访问控制，用户必须提供 CIDR 格式的 IP 地址或 IP 地址范围的集，以将其包含在给定数据库帐户的客户端 IP 允许列表中。 应用此配置后，服务器会阻止从该允许列表外的计算机发出的所有请求。  下图描述了基于 IP 的访问控制的连接处理流：

![该图显示基于 IP 的访问控制的连接过程](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a>配置 IP 访问控制策略
可以在 Azure 门户中设置 IP 访问控制策略，也可以通过 [Azure CLI](cli-samples.md)、[Azure Powershell](powershell-samples.md) 或 [REST API](/rest/api/cosmos-db/) 通过更新 **ipRangeFilter** 属性以编程方式设置 IP 访问控制策略。 

若要在 Azure 门户中设置 IP 访问控制策略，请导航到 Azure Cosmos DB 帐户页，在导航菜单中单击“防火墙”，将“允许从以下位置访问”值更改为“选定的网络”，并单击“保存”。 

![此屏幕截图显示了如何在 Azure 门户中打开“防火墙”页](./media/firewall-support/azure-portal-firewall.png)

启用 IP 访问控制后，可在门户中指定 IP 地址和范围，并使用相应的开关来启用对其他 Azure 服务和 Azure 门户的访问。 以下各节提供了有关这些开关的其他信息。

> [!NOTE]
> 通过启用 Azure Cosmos DB 数据库帐户的 IP 访问控制策略，阻止从 IP 地址范围已配置的允许列表外部的计算机访问 Azure Cosmos DB 数据库帐户。 通过此模型，也将阻止从门户浏览数据平面操作，以确保访问控制的完整性。

## <a name="connections-from-the-azure-portal"></a>从 Azure 门户连接 

以编程的方式启用 IP 访问控制策略时，需将 Azure 门户的 IP 地址添加到 ipRangeFilter 属性以维持访问。 门户 IP 地址是：

|区域|IP 地址|
|------|----------|
|所有区域（下面指定的这些区域除外）|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|德国|51.4.229.218|
|中国|139.217.8.252|
|美国政府|52.244.48.71|

在 Azure 门户中将“防火墙”设置更改为“选定的网络”时，默认已启用对 Azure 门户的访问。 

![此屏幕截图显示了如何启用对 Azure 门户的访问](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-other-azure-paas-services"></a>从 Azure PaaS 服务连接 
在 Azure 中，PaaS 服务（如 Azure 流分析、Azure Functions、Azure 应用服务）都是与 Azure Cosmos DB 结合使用的。 要从此类 IP 地址不可用的服务中启用对 Azure Cosmos DB 数据库帐户的访问，请以编程方式将 IP 地址 0.0.0.0 添加到与 Azure Cosmos DB 数据库帐户关联的允许 IP 地址列表中。 

在 Azure 门户中将“防火墙”设置更改为“选定的网络”时，默认已启用对其他 Azure 服务的访问。 

![此屏幕截图显示了如何在 Azure 门户中打开“防火墙”页](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>从当前 IP 连接

为简化开发，Azure 门户将帮助你识别客户端计算机的 IP 并将其添加到允许的列表中，以便在计算机上运行的应用可以访问 Azure Cosmos DB 帐户。 此处的客户端 IP 地址与通过门户看到的一样。 它可能是计算机的客户端 IP 地址，也可能是网络网关的 IP 地址。 在迁移到生产环境之前，不要忘记将其删除。

若要启用当前 IP，请选择“添加当前 IP”将当前 IP 添加到 IP 列表，并单击“保存”。

![此屏幕截图显示了如何为当前 IP 配置防火墙设置](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>云服务中的连接
在 Azure 中，云服务是一种使用 Azure Cosmos DB 承载中间层服务逻辑的常用方法。 要从云服务启用对 Azure Cosmos DB 数据库帐户的访问，必须通过[配置 IP 访问控制策略](#configure-ip-policy)，将云服务的公共 IP 地址添加到与 Azure Cosmos DB 数据库帐户关联的 IP 地址的允许列表中。 这可确保云服务的所有角色实例都有权访问 Azure Cosmos DB 数据库帐户。 如以下屏幕截图所示，可以在 Azure 门户中检索云服务的 IP 地址：

![该屏幕截图显示在 Azure 门户中显示的云服务的公共 IP 地址](./media/firewall-support/public-ip-addresses.png)

通过添加其他角色实例扩大云服务时，这些新的实例会自动具有 Azure Cosmos DB 数据库帐户的访问权限，因为它们属于同一云服务。

## <a name="connections-from-virtual-machines"></a>从虚拟机连接
[虚拟机](https://azure.microsoft.com/services/virtual-machines/)或[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)还可以用于通过 Azure Cosmos DB 来托管中间层服务。  要将 Azure Cosmos DB 数据库帐户配置为允许从虚拟机访问，必须通过[配置 IP 访问控制策略](#configure-ip-policy)，将虚拟机和/或虚拟机规模集的公共 IP 地址配置为 Azure Cosmos DB 数据库帐户允许的 IP 地址之一。 如以下屏幕截图所示，可以在 Azure 门户中检索虚拟机的 IP 地址。

![该屏幕截图显示在 Azure 门户中显示的虚拟机的公共 IP 地址](./media/firewall-support/public-ip-addresses-dns.png)

将其他虚拟机实例添加到组时，会自动向其提供对 Azure Cosmos DB 数据库帐户的访问权限。

## <a name="connections-from-the-internet"></a>从 Internet 连接
从 Internet 上的计算机访问 Azure Cosmos DB 数据库帐户时，必须将客户端 IP 地址或计算机的 IP 地址范围添加到 Azure Cosmos DB 数据库帐户 IP 地址的允许列表中。 

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP 访问控制策略的故障排除
### <a name="portal-operations"></a>门户操作
通过启用 Azure Cosmos DB 数据库帐户的 IP 访问控制策略，阻止从 IP 地址范围已配置的允许列表外部的计算机访问 Azure Cosmos DB 数据库帐户。 因此，如果希望启用门户数据平面操作，例如浏览集合和查询文档，则需要使用门户中的“防火墙”页显式允许访问 Azure 门户。 

![此屏幕截图显示了如何启用对 Azure 门户的访问](./media/firewall-support/azure-portal-firewall.png)

### <a name="sdk--rest-api"></a>SDK & Rest API
出于安全考虑，通过 SDK 或 REST API 从允许列表以外的计算机进行的访问将返回通用的“404 未找到”响应，不包含任何其他详细信息。 验证 Azure Cosmos DB 数据库帐户配置的 IP 允许列表，以确保将正确的策略配置应用到 Azure Cosmos DB 数据库帐户。

## <a name="next-steps"></a>后续步骤
有关网络相关性能提示的信息，请参阅[性能提示](performance-tips.md)。

