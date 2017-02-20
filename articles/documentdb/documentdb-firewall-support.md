---
title: "Azure DocumentDB 防火墙支持和 IP 访问控制 | Microsoft Docs"
description: "了解如何将 IP 访问控制策略用于 Azure DocumentDB 数据库帐户上的防火墙支持。"
keywords: "IP 访问控制, 防火墙支持"
services: documentdb
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: ankshah; kraman
translationtype: Human Translation
ms.sourcegitcommit: 08cac64a6b08266f78bca03f1139a13e9686ebc3
ms.openlocfilehash: 819602cda932ea698287724e307ebbd73f1af988


---
# <a name="documentdb-firewall-support"></a>DocumentDB 防火墙支持
为保护存储在 Azure DocumentDB 数据库帐户的数据，DocumentDB 已提供对基于机密的[授权模型](https://msdn.microsoft.com/library/azure/dn783368.aspx)的支持，该模型利用强大的基于哈希的消息验证代码 (HMAC)。 现在，除了基于机密的授权模型以外，DocumentDB 还支持针对入站防火墙支持的基于 IP 的策略驱动访问控制。 此模型与传统数据库系统的防火墙规则非常类似，并且对 DocumentDB 数据库帐户提供额外的安全级别。 利用此模型，现可将 DocumentDB 数据库帐户配置为仅可从一组已批准的计算机和/或云服务进行访问。 从该组已批准的计算机和服务访问 DocumentDB 资源仍要求调用方提供有效的授权令牌。

## <a name="ip-access-control-overview"></a>IP 访问控制概述
默认情况下，只要请求附有有效的授权令牌，便可从公共 Internet 访问 DocumentDB 数据库帐户。 若要配置基于 IP 策略的访问控制，用户必须提供 CIDR 格式的 IP 地址或 IP 地址范围的集，以将其包含在给定数据库帐户的客户端 IP 允许列表中。 应用此配置后，服务器将阻止从该允许列表外的计算机发出的所有请求。  下图描述了基于 IP 的访问控制的连接处理流。

![该图显示基于 IP 的访问控制的连接过程](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>云服务中的连接
在 Azure 中，云服务是一种使用 DocumentDB 承载中间层服务逻辑的常用方法。 若要从云服务启用对 DocumentDB 数据库帐户的访问，必须通过[配置 IP 访问控制策略](#configure-ip-policy)，将云服务的公共 IP 地址添加到与 DocumentDB 数据库帐户关联的 IP 地址的允许列表中。  这可确保云服务的所有角色实例都有权访问 DocumentDB 数据库帐户。 如以下屏幕截图所示，可以在 Azure 门户中检索云服务的 IP 地址。

![该屏幕截图显示在 Azure 门户中显示的云服务的公共 IP 地址](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

通过添加其他角色实例扩大云服务时，这些新的实例将自动具有 DocumentDB 数据库帐户的访问权限，因为它们属于同一云服务。

## <a name="connections-from-virtual-machines"></a>从虚拟机连接
[虚拟机](https://azure.microsoft.com/services/virtual-machines/)或[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)还可以用于托管使用 DocumentDB 的中间层服务。  若要将 DocumentDB 数据库帐户配置为允许从虚拟机访问，必须通过[配置 IP 访问控制策略](#configure-ip-policy)，将虚拟机和/或虚拟机规模集的公共 IP 地址配置为 DocumentDB 数据库帐户允许的 IP 地址之一。 如以下屏幕截图所示，可以在 Azure 门户中检索虚拟机的 IP 地址。

![该屏幕截图显示在 Azure 门户中显示的虚拟机的公共 IP 地址](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

将其他虚拟机实例添加到组时，将自动向其提供对 DocumentDB 数据库帐户的访问权限。

## <a name="connections-from-the-internet"></a>从 Internet 连接
从 Internet 上的计算机访问 DocumentDB 数据库帐户时，必须将客户端 IP 地址或计算机的 IP 地址范围添加到 DocumentDB 数据库帐户 IP 地址的允许列表中。 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a>配置 IP 访问控制策略
更新 `ipRangeFilter` 属性后，可以通过 [Azure CLI](documentdb-automation-resource-manager-cli.md)、[Azure Powershell](documentdb-manage-account-with-powershell.md) 或 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 以编程方式设置 IP 访问控制策略。 IP 地址/范围必须以逗号分隔，且不能包含空格。 示例：“13.91.6.132,13.91.6.1/24”。 通过这些方法更新数据库帐户时，请确保填充所有属性以防止重置为默认设置。

> [!NOTE]
> 通过启用 DocumentDB 数据库帐户的 IP 访问控制策略，阻止从 IP 地址范围已配置的允许列表外部的计算机访问 DocumentDB 数据库帐户。 通过此模型，也将阻止从门户浏览数据平面操作，以确保访问控制的完整性。

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP 访问控制策略的故障排除
### <a name="portal-operations"></a>门户操作
通过启用 DocumentDB 数据库帐户的 IP 访问控制策略，阻止从 IP 地址范围已配置的允许列表外部的计算机访问 DocumentDB 数据库帐户。 通过此模型，也将阻止从门户浏览数据平面操作，以确保访问控制的完整性。 

### <a name="sdk--rest-api"></a>SDK & Rest API
出于安全考虑，通过 SDK 或 REST API 从允许列表以外的计算机进行的访问将返回通用的“404 未找到”响应，不包含任何其他详细信息。 请验证 DocumentDB 数据库帐户配置的 IP 允许列表，以确保将正确的策略配置应用到 DocumentDB 数据库帐户。

## <a name="next-steps"></a>后续步骤
有关网络相关性能技巧的信息，请参阅[性能提示](documentdb-performance-tips.md)。




<!--HONumber=Feb17_HO1-->


