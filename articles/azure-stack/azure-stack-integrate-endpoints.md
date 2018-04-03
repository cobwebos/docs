---
title: Azure Stack 数据中心集成 - 发布终结点
description: 了解如何在数据中心发布 Azure Stack 终结点
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: 136d78be3cddfd6fd4e491d5ea3f5d51d0dc611f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack 数据中心集成 - 发布终结点
Azure 堆栈将设置为其基础结构角色的虚拟 IP 地址 (Vip)。 这些 VIP 是从公共 IP 地址池分配的。 每个 VIP 受软件定义的网络层中的访问控制列表 (ACL) 保护。 还可以在物理交换机（TOR 和 BMC）之间使用 ACL 来进一步强化解决方案。 为每个终结点在部署时指定的外部 DNS 区域中创建 DNS 条目。


以下体系结构图显示了不同的网络层和 ACL：

![结构化的图片](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>端口和协议（入站）

一组基础结构的 Vip 所需的到外部网络的发布 Azure 堆栈终结点。 *终结点 (VIP)*表显示了每个终结点、 所需的端口和协议。 请参阅需要额外的资源提供程序，如 SQL 资源提供程序的终结点的特定资源提供程序部署文档。

Vip 未列出，因为它们不需要发布 Azure 堆栈的内部基础结构。

> [!NOTE]
> 用户 VIP 是动态的，由用户自己定义，而不受 Azure Stack 操作员的控制。


|终结点 (VIP)|DNS 主机 A 记录|协议|端口|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|门户（管理员）|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Azure 资源管理器（管理员）|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|门户（用户）|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure 资源管理器（用户）|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|图形|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|证书吊销列表|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP 和 UDP|53|
|Key Vault（用户）|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault（管理员）|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|存储队列|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储表|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储 Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 资源提供程序|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 资源提供程序|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|应用服务|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300（Azure 资源管理器）|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP、UDP|21、1021、10001-101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>端口和 URL（出站）

Azure Stack 仅支持透明代理服务器。 如果部署中的透明代理上行链接到传统的代理服务器，则必须允许以下端口和 URL，以便能够进行出站通信：


|目的|代码|协议|端口|
|---------|---------|---------|---------|
|标识|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Marketplace 联合|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|修补程序和更新|https://&#42;.azureedge.net|HTTPS|443|
|注册|https://management.azure.com|HTTPS|443|
|使用情况|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|


## <a name="next-steps"></a>后续步骤

[Azure Stack PKI 要求](azure-stack-pki-certs.md)