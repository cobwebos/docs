---
title: "Azure Cosmos DB 防火墙支持和 IP 访问控制 | Microsoft Docs"
description: "了解如何将 IP 访问控制策略用于 Azure Cosmos DB 数据库帐户上的防火墙支持。"
keywords: "IP 访问控制, 防火墙支持"
services: cosmos-db
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: ankshah
ms.openlocfilehash: 9e4419b57edf86e03044ad1047b18397ff4d8d19
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="azure-cosmos-db-firewall-support"></a>Azure Cosmos DB 防火墙支持
为保护存储在 Azure Cosmos DB 数据库帐户的数据，Azure Cosmos DB 已提供对基于机密的[授权模型](https://msdn.microsoft.com/library/azure/dn783368.aspx)的支持，该模型利用强大的基于哈希的消息验证代码 (HMAC)。 现在，除了基于机密的授权模型以外，Azure Cosmos DB 还支持针对入站防火墙支持的基于 IP 的策略驱动访问控制。 此模型与传统数据库系统的防火墙规则非常类似，并且对 Azure Cosmos DB 数据库帐户提供额外级别的安全性。 利用此模型，现可将 Azure Cosmos DB 数据库帐户配置为仅可从一组已批准的计算机和/或云服务进行访问。 从这些已批准的计算机和服务访问 Azure Cosmos DB 资源仍要求调用方提供有效的授权令牌。

## <a name="ip-access-control-overview"></a>IP 访问控制概述
默认情况下，只要请求附有有效的授权令牌，便可从公共 Internet 访问 Azure Cosmos DB 数据库帐户。 要配置基于 IP 策略的访问控制，用户必须提供 CIDR 格式的 IP 地址或 IP 地址范围的集，以将其包含在给定数据库帐户的客户端 IP 允许列表中。 应用此配置后，服务器将阻止从该允许列表外的计算机发出的所有请求。  下图描述了基于 IP 的访问控制的连接处理流。

![该图显示基于 IP 的访问控制的连接过程](./media/firewall-support/firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>云服务中的连接
在 Azure 中，云服务是一种使用 Azure Cosmos DB 承载中间层服务逻辑的常用方法。 要从云服务启用对 Azure Cosmos DB 数据库帐户的访问，必须通过[配置 IP 访问控制策略](#configure-ip-policy)，将云服务的公共 IP 地址添加到与 Azure Cosmos DB 数据库帐户关联的 IP 地址的允许列表中。  这可确保云服务的所有角色实例都有权访问 Azure Cosmos DB 数据库帐户。 如以下屏幕截图所示，可以在 Azure 门户中检索云服务的 IP 地址。

![该屏幕截图显示在 Azure 门户中显示的云服务的公共 IP 地址](./media/firewall-support/public-ip-addresses.png)

通过添加其他角色实例扩大云服务时，这些新的实例会自动具有 Azure Cosmos DB 数据库帐户的访问权限，因为它们属于同一云服务。

## <a name="connections-from-virtual-machines"></a>从虚拟机连接
[虚拟机](https://azure.microsoft.com/services/virtual-machines/)或[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)还可以用于通过 Azure Cosmos DB 来托管中间层服务。  要将 Azure Cosmos DB 数据库帐户配置为允许从虚拟机访问，必须通过[配置 IP 访问控制策略](#configure-ip-policy)，将虚拟机和/或虚拟机规模集的公共 IP 地址配置为 Azure Cosmos DB 数据库帐户允许的 IP 地址之一。 如以下屏幕截图所示，可以在 Azure 门户中检索虚拟机的 IP 地址。

![该屏幕截图显示在 Azure 门户中显示的虚拟机的公共 IP 地址](./media/firewall-support/public-ip-addresses-dns.png)

将其他虚拟机实例添加到组时，会自动向其提供对 Azure Cosmos DB 数据库帐户的访问权限。

## <a name="connections-from-the-internet"></a>从 Internet 连接
从 Internet 上的计算机访问 Azure Cosmos DB 数据库帐户时，必须将客户端 IP 地址或计算机的 IP 地址范围添加到 Azure Cosmos DB 数据库帐户 IP 地址的允许列表中。 

## <a id="configure-ip-policy"></a>配置 IP 访问控制策略
可以在 Azure 门户中设置 IP 访问控制策略，也可以通过 [Azure CLI](cli-samples.md)、[Azure Powershell](powershell-samples.md) 或 [REST API](/rest/api/documentdb/) 通过更新 `ipRangeFilter` 属性以编程方式设置 IP 访问控制策略。 IP 地址/范围必须以逗号分隔，且不能包含空格。 示例：“13.91.6.132,13.91.6.1/24”。 通过这些方法更新数据库帐户时，请确保填充所有属性以防止重置为默认设置。

> [!NOTE]
> 通过启用 Azure Cosmos DB 数据库帐户的 IP 访问控制策略，阻止从 IP 地址范围已配置的允许列表外部的计算机访问 Azure Cosmos DB 数据库帐户。 通过此模型，也将阻止从门户浏览数据平面操作，以确保访问控制的完整性。

为简化开发，Azure 门户将帮助你识别客户端计算机的 IP 并将其添加到允许的列表中，以便在计算机上运行的应用可以访问 Azure Cosmos DB 帐户。 请注意，此处的客户端 IP 地址与通过门户看到的一样。 它可能是计算机的客户端 IP 地址，也可能是网络网关的 IP 地址。 在迁移到生产环境之前，不要忘记将其删除。

要在 Azure 门户中设置 IP 访问控制策略，请导航到 Azure Cosmos DB 帐户边栏选项卡，在导航菜单中单击“防火墙”，并单击“开启”。 

![此屏幕截图显示了如何在 Azure 门户中打开“防火墙”边栏选项卡](./media/firewall-support/azure-portal-firewall.png)

在新窗格中，指定 Azure 门户是否可以访问该帐户，并根据情况添加其他地址和范围，单击“保存”。  

> [!NOTE]
> 启用 IP 访问控制策略时，需要添加 Azure 门户的 IP 地址以维护访问。 门户 IP 地址是：
> |区域|IP 地址|
> |------|----------|
> |所有区域（下面指定的这些区域除外）|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
> |德国|51.4.229.218|
> |中国|139.217.8.252|
> |美国政府|52.244.48.71|
>

![此屏幕截图显示了如何在 Azure 门户中配置防火墙设置](./media/firewall-support/azure-portal-firewall-configure.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP 访问控制策略的故障排除
### <a name="portal-operations"></a>门户操作
通过启用 Azure Cosmos DB 数据库帐户的 IP 访问控制策略，阻止从 IP 地址范围已配置的允许列表外部的计算机访问 Azure Cosmos DB 数据库帐户。 因此，如果希望启用门户数据平面操作，例如浏览集合和查询文档，则需要使用门户中的“防火墙”边栏选项卡显式允许 Azure 门户访问。 

![此屏幕截图显示了如何启用对 Azure 门户的访问](./media/firewall-support/azure-portal-access-firewall.png)

### <a name="sdk--rest-api"></a>SDK & Rest API
出于安全考虑，通过 SDK 或 REST API 从允许列表以外的计算机进行的访问将返回通用的“404 未找到”响应，不包含任何其他详细信息。 请验证 Azure Cosmos DB 数据库帐户配置的 IP 允许列表，以确保将正确的策略配置应用到 Azure Cosmos DB 数据库帐户。

## <a name="next-steps"></a>后续步骤
有关网络相关性能技巧的信息，请参阅[性能提示](performance-tips.md)。

