---
title: "Azure 堆栈数据中心集成-发布终结点"
description: "了解如何在你的数据中心中发布 Azure 堆栈终结点"
services: azure-stack
author: jeffgilb
ms.service: azure-stack
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: 
ms.openlocfilehash: ae59ae74dd6dfe29a077ed5943eb1a16e561078a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure 堆栈数据中心集成-发布终结点

*适用范围： Azure 堆栈集成系统*

Azure 堆栈将设置其基础结构角色的各种终结点 (Vip 的虚拟 IP 地址)。 这些 Vip 是从公共 IP 地址池分配的。 每个 VIP 访问控制列表 (ACL) 中的软件定义网络层保护。 Acl 还跨物理交换机 （TORs 和 BMC） 用于进一步强化处理解决方案。 为每个终结点在部署时指定的外部 DNS 区域中创建 DNS 条目。


下面的体系结构关系图显示了不同的网络层和 Acl:

![体系结构图](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>端口和协议 （入站）

下表中列出所需的到外部网络的发布 Azure 堆栈终结点的基础结构 Vip。 此列表显示每个终结点、 所需的端口和协议。 终结点所需的 SQL 资源提供程序和其他人，等其他资源提供程序特定的资源提供程序部署文档中介绍。

未列出 Vip，因为它们不需要发布 Azure 堆栈的内部基础结构。

> [!NOTE]
> 用户 Vip 是动态的由用户自行通过 Azure 堆栈运算符没有控件的定义。


|终结点 (VIP)|DNS 主机记录|协议|端口|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|门户 （管理员）|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure 资源管理器 （管理员）|Adminmanagement。*&lt;区域 >。&lt;fqdn >*|HTTPS|443<br>30024|
|门户 （用户）|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure 资源管理器 （用户）|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|证书吊销列表|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP 和 UDP|53|
|密钥保管库 （用户）|*.vault.*&lt;region>.&lt;fqdn>*|TCP|443|
|密钥保管库 （管理员）|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|TCP|443|
|存储队列|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储表|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储 Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>端口和 Url （出站）

Azure 堆栈仅支持透明的代理服务器。 在部署中其中透明代理的上行以传统的代理服务器，你必须允许下列端口和 Url 用于出站通信：


|目的|URL|协议|端口|
|---------|---------|---------|---------|
|标识|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|应用商店联合|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|修补程序和更新|https://&#42;.azureedge.net|HTTPS|443|
|注册|https://management.azure.com|HTTPS|443|
|使用情况|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|


## <a name="next-steps"></a>接下来的步骤
[Azure 堆栈数据中心集成的安全](azure-stack-integrate-security.md)