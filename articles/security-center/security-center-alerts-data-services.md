---
title: 威胁检测的 Azure 安全中心中的数据服务 |Microsoft Docs
description: 本主题介绍了 Azure 安全中心中可用的数据服务警报。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 87cfd2769e473d26c2dcae1b7b418f6fb1739915
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626287"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure 安全中心中的数据服务的威胁检测

 安全中心会分析数据存储服务的日志，并检测到威胁到你的数据的资源时触发警报。 本主题列出了安全中心会生成以下服务的警报：

* [Azure SQL 数据库和 SQL 数据仓库](#data-sql)
* [Azure 存储](#azure-storage)

## Azure SQL 数据库和 SQL 数据仓库 <a name="data-sql"></a>

SQL 威胁检测可检测异常活动表示异常和潜在有害尝试访问或利用数据库。 安全中心会分析 SQL 审核日志和 SQL 引擎中以本机方式运行。

|警报|描述|
|---|---|
|**SQL 注入漏洞**|应用程序已在数据库中生成错误的 SQL 语句。 这可能指示可能存在易受 SQL 注入攻击漏洞。 生成错误语句的可能原因有两个：或者，应用程序代码中的有缺陷，构造错误的 SQL 语句。 或者，应用程序代码或存储的过程构造有故障的 SQL 语句可能会被 SQL 注入的时，不清理用户输入。|
|**潜在 SQL 注入**|针对标识应用程序容易受到 SQL 注入发生了有效利用。 这意味着攻击者尝试注入恶意 SQL 语句使用易受攻击的应用程序代码或存储过程。|
|**从异常位置访问**|已在 SQL server，其中有人登录到 SQL server 从异常的地理位置的访问模式发生更改。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。|
|**从不熟悉的主体的访问权限**|已在 SQL server 的访问模式发生更改-有人登录到 SQL server 使用异常的主体 （SQL 用户）。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。|
|**从可能有害的应用程序访问**|可能有害的应用程序用于访问数据库。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。|
|**暴力破解 SQL 凭据**|发生高异常使用不同的凭据的登录失败次数。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。|

SQL 威胁检测警报，请参阅有关详细信息[Azure SQL 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)，并查看威胁检测警报部分。 另请参阅[如何 Azure 安全中心帮助揭示介入](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/)若要查看安全中心如何使用恶意 SQL 活动检测来发现攻击的示例。

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> 仅当前适用于 Blob 存储的 Azure 存储高级的威胁防护。 

Azure 存储高级威胁防护提供额外的一层安全智能，用于检测试图访问或利用存储帐户的异常或可能有害的企图。 而无需是安全专家，，和管理安全监视系统中，此层的保护使您能够解决威胁。

安全中心将分析诊断日志的读取、 写入和删除请求到 Blob 存储来检测威胁，并出现在活动中的异常情况时，它会触发警报。 有关详细信息，请参阅[配置存储分析日志记录](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)有关详细信息。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**异常的位置访问异常**|采样的网络流量分析检测到异常传出远程桌面协议 (RDP) 通信源自您的部署中的资源。 此活动被视为异常为此环境，并可能表示你的资源已遭到破坏，正在用它来暴力破解外部 RDP 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**应用程序的访问异常**|指示异常的应用程序具有访问此存储帐户。 可能的原因是攻击者已访问使用新的应用程序在存储帐户。|
|**匿名访问异常**|指示存储帐户的访问模式中的更改。 例如，该帐户已访问匿名 （没有任何身份验证），这是意外与此帐户上最新的访问模式进行比较。 可能的原因是攻击者已利用了公共读取访问权限的容器，包含个 blob 存储。|
|**数据渗透异常**|指示已与此存储容器上最近的活动相比，提取的数据量非常大。 可能的原因是攻击者已从容器，包含个 blob 存储中提取大量数据。|
|**意外的删除异常**|指示一个或多个意外的删除操作发生在存储帐户中，相比在此帐户上最近的活动。 可能的原因是攻击者已从你的存储帐户中删除数据。|
|**上传 Azure 云服务包**|指示，已与此帐户上最近的活动相比以异常方式上载到存储帐户的 Azure 云服务包 （.cspkg 文件）。 可能的原因是攻击者准备要部署到 Azure 云服务从你的存储帐户的恶意代码。|
|**权限的访问异常**|指示已以异常方式中的存储容器的访问权限已更改。 可能的原因是，攻击者已更改容器权限，以降低其安全状态或获得持久性。|
|**检查访问异常**|指示具有已存储帐户的访问权限检查与此帐户上最近的活动相比以异常方式。 可能的原因是未来攻击的攻击者执行了侦测。|
|**数据探索异常**|指示存储帐户中的容器或 blob 已枚举以异常方式，与此帐户上最近的活动相比。 可能的原因是未来攻击的攻击者执行了侦测。|

>[!NOTE]
>高级的威胁防护功能用于 Azure 存储目前不在 Azure 政府版和主权云区域中可用。

有关存储的警报的详细信息，请参阅[适用于 Azure 存储高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)文章中，并查看保护警报部分。
