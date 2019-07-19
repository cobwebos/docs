---
title: Azure 安全中心中的数据服务威胁检测 |Microsoft Docs
description: 本主题介绍 Azure 安全中心提供的数据服务警报。
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
ms.author: v-mohabe
ms.openlocfilehash: 1cafd8a3c766e57aed67634d7da8498c9a6ee120
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295825"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure 安全中心中的数据服务威胁检测

 安全中心会分析数据存储服务的日志, 并在检测到对数据资源的威胁时触发警报。 本主题列出了安全中心为以下服务生成的警报:

* [Azure SQL 数据库和 SQL 数据仓库](#data-sql)
* [Azure 存储](#azure-storage)

## Azure SQL 数据库和 SQL 数据仓库<a name="data-sql"></a>

SQL 威胁检测可检测异常活动, 指示对访问或利用数据库的异常和潜在有害尝试。 安全中心会分析 SQL 审核日志, 并以本机方式在 SQL 引擎中运行。

|警报|描述|
|---|---|
|**漏洞到 SQL 注入**|应用程序在数据库中生成了错误的 SQL 语句。 这可能表示存在 SQL 注入攻击的漏洞。 生成错误语句的可能原因有两个：或者, 应用程序代码中的缺陷构造了错误的 SQL 语句。 或者, 应用程序代码或存储过程在构造出现错误的 SQL 语句时未净化用户输入, 这可能会被用于 SQL 注入。|
|**潜在 SQL 注入**|对已识别的应用程序进行主动攻击时, 易受 SQL 注入攻击。 这意味着攻击者正尝试使用有漏洞的应用程序代码或存储过程注入恶意 SQL 语句。|
|**从异常位置访问**|SQL server 的访问模式发生了变化, 在这种情况下, 有人从异常的地理位置登录到 SQL server。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。|
|**从不熟悉的主体进行访问**|SQL server 的访问模式发生了变化-有人使用异常的主体 (SQL 用户) 登录到 SQL server。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。|
|**从可能有害的应用程序访问**|已使用可能有害的应用程序访问数据库。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。|
|**强力强制 SQL 凭据**|发生了具有不同凭据的异常大量失败登录。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。|

有关 SQL 威胁检测警报的详细信息, 请参阅[AZURE SQL 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)和查看威胁检测警报部分。 另请参阅[Azure 安全中心如何帮助展示受到](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/), 以查看安全中心如何使用恶意 SQL 活动检测来发现攻击的示例。

## Azure 存储<a name="azure-storage"></a>

>[!NOTE]
> Azure 存储的高级威胁防护目前仅适用于 Blob 存储。 

Azure 存储高级威胁防护提供额外的一层安全智能，用于检测试图访问或利用存储帐户的异常或可能有害的企图。 利用这一层保护, 你无需成为安全专家, 就能解决威胁, 并管理安全监视系统。

安全中心将分析对 Blob 存储的读取、写入和删除请求的诊断日志, 以检测威胁, 并在活动发生异常时触发警报。 有关详细信息, 请参阅[配置存储分析日志记录](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**异常位置访问异常**|采样网络流量分析检测到从你的部署中的资源发起的异常传出远程桌面协议 (RDP) 通信。 此环境中的此活动被视为异常, 可能表示资源已遭到破坏, 现已用于暴力破解外部 RDP 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**应用程序访问异常**|指示异常应用程序已访问此存储帐户。 可能的原因是攻击者使用新的应用程序访问了你的存储帐户。|
|**匿名访问异常**|指示存储帐户的访问模式发生更改。 例如, 帐户已被匿名访问 (无需任何身份验证), 这与此帐户上的最新访问模式有意外的对比。 可能的原因是攻击者利用了公共读取权限来访问包含 blob 存储的容器。|
|**数据渗透异常**|表示与此存储容器上的最近活动相比, 已提取异常大量的数据。 可能的原因是, 攻击者从容纳 blob 存储的容器中提取了大量数据。|
|**意外删除异常**|指示存储帐户中发生了一个或多个意外的删除操作, 与此帐户上的最近活动相比。 可能的原因是攻击者已从存储帐户中删除数据。|
|**上载 Azure 云服务包**|表示 Azure 云服务包 (.cspkg 文件) 已以异常方式上传到存储帐户, 与此帐户上的最近活动相比。 可能的原因是攻击者已准备好将恶意代码从存储帐户部署到 Azure 云服务。|
|**权限访问异常**|指示此存储容器的访问权限已以异常方式更改。 可能的原因是攻击者已更改容器权限以降低其安全状况或获取持久性。|
|**检查访问异常情况**|表示与此帐户上的最近活动相比, 对存储帐户的访问权限的检查方式与此相同。 一个可能的原因是攻击者为将来的攻击执行了侦测。|
|**数据浏览异常**|表示与此帐户上的最近活动相比, 存储帐户中的 blob 或容器已以异常方式进行枚举。 一个可能的原因是攻击者为将来的攻击执行了侦测。|

>[!NOTE]
>Azure 存储的高级威胁防护当前在 Azure 政府版和主权云区域中不可用。

有关存储警报的详细信息, 请参阅[Azure 存储的高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)一文, 并查看保护警报部分。
