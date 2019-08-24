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
ms.date: 7/24/2019
ms.author: v-mohabe
ms.openlocfilehash: 25f691d972b208b517f92752e2a9c30b016ec62a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013352"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure 安全中心中的数据服务威胁检测

 Azure 安全中心将分析数据存储服务的日志, 并在检测到对数据资源的威胁时触发警报。 本主题列出了安全中心为以下服务生成的警报:

* [Azure SQL 数据库和 Azure SQL 数据仓库](#data-sql)
* [Azure 存储](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL 数据库和 SQL 数据仓库<a name="data-sql"></a>

SQL 威胁检测可标识异常活动, 指示对访问或利用数据库的异常和潜在有害尝试。 安全中心会分析 SQL 审核日志, 并以本机方式在 SQL 引擎中运行。

|警报|描述|
|---|---|
|**漏洞到 SQL 注入**|应用程序在数据库中生成了错误的 SQL 语句。 这可能表示存在 SQL 注入攻击的漏洞。 错误的语句有两个可能的原因。 应用程序代码中的缺陷可能构造了错误的 SQL 语句。 或者, 应用程序代码或存储过程在构造出现错误的 SQL 语句时, 不会清理用户输入, 这可被用于 SQL 注入。|
|**潜在 SQL 注入**|对已识别的应用程序进行主动攻击时, 易受 SQL 注入攻击。 这意味着攻击者正尝试使用有漏洞的应用程序代码或存储过程注入恶意 SQL 语句。|
|**从异常位置访问**|访问模式已更改为 SQL Server, 其中有人从异常的地理位置登录到了服务器。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下, 警报会检测恶意操作 (以前的员工或外部攻击者)。|
|**从不熟悉的主体进行访问**|访问模式已更改为 SQL Server。 有人使用异常的主体 (用户) 登录到服务器。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下, 警报会检测恶意操作 (以前的员工或外部攻击者)。|
|**从可能有害的应用程序访问**|已使用可能有害的应用程序访问数据库。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下, 警报会检测使用常见工具的攻击。|
|**强力强制 SQL 凭据**|发生了具有不同凭据的异常数量异常的登录失败。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。|

有关 SQL 威胁检测警报的详细信息, 请参阅[AZURE Sql 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)。 具体而言, 请查看威胁检测警报部分。 另请参阅[Azure 安全中心如何帮助展示受到](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/), 以查看安全中心如何使用恶意 SQL 活动检测来发现攻击的示例。

## 储存<a name="azure-storage"></a>

>[!NOTE]
> 适用于存储的高级威胁防护目前仅适用于 Blob 存储。

适用于存储的高级威胁防护提供额外的安全智能层, 用于检测访问或利用存储帐户的异常和潜在有害尝试。 利用这一层保护, 你无需成为安全专家, 就能解决威胁, 并管理安全监视系统。

安全中心将分析对 Blob 存储的读取、写入和删除请求的诊断日志, 以检测威胁, 并在活动发生异常时触发警报。 有关详细信息, 请参阅[Configure 存储分析日志记录](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**异常位置访问异常**|采样的网络流量分析检测到异常传出远程桌面协议 (RDP) 通信, 源自部署中的资源。 此环境的此活动被视为不正常。 这可能表示资源已遭到破坏, 现在可用于对外部 RDP 终结点进行暴力攻击。 请注意, 这种类型的活动可能会导致你的 IP 被外部实体标记为恶意。|
|**应用程序访问异常**|指示异常应用程序已访问此存储帐户。 可能的原因是攻击者通过使用新的应用程序访问了你的存储帐户。|
|**匿名访问异常**|指示存储帐户的访问模式发生更改。 例如, 帐户已被匿名访问 (无需任何身份验证), 这与此帐户上的最新访问模式有意外的对比。 可能的原因是攻击者利用了对保存 blob 存储的容器的公共读取访问权限。|
|**Tor 异常**|指示已从称为 Tor 的活动退出节点的 IP 地址 (匿名代理) 成功访问此帐户。 此警报的严重性考虑使用的身份验证类型 (如果有), 以及这是否是此类访问的第一种情况。 可能的原因可能是已使用 Tor 访问了你的存储帐户的攻击者, 或者是通过使用 Tor 访问了你的存储帐户的合法用户。|
|**数据渗透异常**|表示与此存储容器上的最近活动相比, 已提取异常大量的数据。 可能的原因是, 攻击者从容纳 blob 存储的容器中提取了大量数据。|
|**意外删除异常**|指示存储帐户中发生了一个或多个意外的删除操作, 与此帐户上的最近活动相比。 可能的原因是攻击者已从存储帐户中删除数据。|
|**上载 Azure 云服务包**|表示 Azure 云服务包 (.cspkg 文件) 已以异常方式上传到存储帐户, 与此帐户上的最近活动相比。 可能的原因是攻击者已准备好将恶意代码从存储帐户部署到 Azure 云服务。|
|**权限访问异常**|指示此存储容器的访问权限已以异常方式更改。 可能的原因是攻击者已更改容器权限以降低其安全状况或获取持久性。|
|**检查访问异常情况**|表示与此帐户上的最近活动相比, 对存储帐户的访问权限的检查方式与此相同。 一个可能的原因是攻击者为将来的攻击执行了侦测。|
|**数据浏览异常**|表示与此帐户上的最近活动相比, 存储帐户中的 blob 或容器已以异常方式进行枚举。 一个可能的原因是攻击者为将来的攻击执行了侦测。|

>[!NOTE]
>适用于存储的高级威胁防护当前在 Azure 政府版和主权云区域中不可用。

有关存储警报的详细信息, 请参阅[Azure 存储的高级威胁防护](../storage/common/storage-advanced-threat-protection.md)。 具体而言, 请查看 "保护警报" 一节。

## Azure Cosmos DB<a name="cosmos-db"></a>

以下警报由异常和可能有害的访问或利用 Azure Cosmos DB 帐户的尝试生成:

|警报|描述|
|---|---|
|**从异常位置访问**|指示 Azure Cosmos DB 帐户的访问模式发生了更改。 用户已从不熟悉的 IP 地址 (与最近的活动) 访问了此帐户。 攻击者已访问该帐户, 或者合法用户已从新的和不正常的地理位置访问了它。 后者的一个示例是从新的应用程序或开发人员进行远程维护。|
|**异常数据渗透**|指示 Azure Cosmos DB 帐户的数据提取模式发生了更改。 与最近的活动相比, 有人提取了异常的数据量。 攻击者可能已从 Azure Cosmos DB 数据库 (例如, 数据渗透或泄露或未经授权的数据传输) 提取了大量数据。 或者, 合法用户或应用程序可能已从容器提取了异常数据量 (例如, 对于维护备份活动)。|

有关详细信息, 请参阅[Azure Cosmos DB 的高级威胁防护](../cosmos-db/cosmos-db-advanced-threat-protection.md)。