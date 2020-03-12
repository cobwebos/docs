---
title: 数据库安全性 - Azure Cosmos DB
description: 了解 Azure Cosmos DB 如何为数据提供数据库保护和数据安全性。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 0c97d70ea3e5c7fdd14b0f97c5e393359f2b948e
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087225"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 安全性 - 概述

本文介绍了数据库安全最佳做法以及 Azure Cosmos DB 提供的关键功能，帮助你防范、检测和应对数据库入侵。

## <a name="whats-new-in-azure-cosmos-db-security"></a>Azure Cosmos DB 安全性的新增功能

静态加密现已可用于所有 Azure 区域的 Azure Cosmos DB 中存储的文档和备份。 对于这些区域中的新客户和现有客户，会自动应用静态加密。 无需进行任何配置；可获得与以前（即知道使用静态加密可确保数据安全之前）一样的出色延迟、吞吐量、可用性和功能。

## <a name="how-do-i-secure-my-database"></a>如何实现保护数据库

数据安全性的责任由你、客户和数据库提供程序共同分担。 根据所选的数据库提供程序，要承担的责任大小将有所不同。 如果选择本地解决方案，则从终结点保护到硬件物理安全性的所有工作都由你负责 - 这不是一个轻松的任务。 如果选择 Azure Cosmos DB 等 PaaS 云数据库提供程序，要考虑的问题会明显减少。 下图摘自 Microsoft 的 [Shared Responsibilities for Cloud Computing](https://aka.ms/sharedresponsibility)（云计算的责任分担）白皮书，显示了使用 Azure Cosmos DB 等 PaaS 提供程序时，责任会得到怎样的减轻。

![客户和数据库提供程序的责任](./media/database-security/nosql-database-security-responsibilities.png)

上图显示了高层级的云安全组件，但是，对于数据库解决方案，需要考虑到哪些具体的事项呢？ 如何对不同的解决方案进行比较？

建议根据以下要求查检表来比较数据库系统：

- 网络安全和防火墙设置
- 用户身份验证和精细用户控制
- 能够全局复制数据来应对区域性故障
- 能够从一个数据中心故障转移到另一个数据中心
- 在数据中心内执行本地数据复制
- 自动数据备份
- 从备份还原已删除的数据
- 保护和隔离敏感数据
- 监视攻击
- 响应攻击
- 能够地域隔离数据以遵守数据监管限制
- 对受保护数据中心内的服务器实施物理保护
- 认证

以下要求看似理所当然，但最近发生的[大规模数据库入侵](https://thehackernews.com/2017/01/mongodb-database-security.html)提醒我们这些要求尽管很简单，但却至关重要：

- 修补的服务器保持最新
- 按默认启用 HTTPS/SSL 加密
- 使用强密码的管理帐户

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Azure Cosmos DB 如何保护我的数据库

让我们回顾前面的列表 - Azure Cosmos DB 能够满足其中的多少项要求？ 它满足每一项要求。

让我们深入分析其中的每项要求。

|安全要求|Azure Cosmos DB 的安全方法|
|---|---|
|网络安全|使用 IP 防火墙是保护数据库安全的第一个保护层。 Azure Cosmos DB 支持使用基于 IP 的策略驱动访问控制来提供入站防火墙支持。 基于 IP 的访问控制类似于传统数据库系统使用的防火墙规则，但会进行扩展，以便只能从已批准的一组计算机或云服务访问 Azure Cosmos 数据库帐户。 有关详细信息，请参阅[Azure Cosmos DB 防火墙支持](firewall-support.md)文章。<br><br>使用 Azure Cosmos DB 可以启用特定的 IP 地址 (168.61.48.0)、IP 范围 (168.61.48.0/8) 以及 IP 和范围的组合。 <br><br>从此允许列表之外的计算机发出的所有请求会被 Azure Cosmos DB 阻止。 从获批准计算机和云服务发出的请求必须完成身份验证过程才能获得资源的访问控制权。<br><br> 你可以使用[虚拟网络服务标记](../virtual-network/service-tags-overview.md)来实现网络隔离，并通过常规 Internet 保护 Azure Cosmos DB 资源。 创建安全规则时，使用服务标记代替特定的 IP 地址。 通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称（例如，AzureCosmosDB），可以允许或拒绝相应服务的流量。|
|授权|Azure Cosmos DB 使用基于哈希的消息身份验证代码 (HMAC) 进行授权。 <br><br>每个请求将使用机密帐户密钥进行哈希处理，后续的 base-64 编码哈希将连同每个调用发送到 Azure Cosmos DB。 要验证请求，Azure Cosmos DB 服务需使用正确的机密密钥和属性生成哈希值，然后将该值与请求中的值进行比较。 如果两个值匹配，将成功为操作授权并处理请求，否则，会发生授权失败并拒绝请求。<br><br>可以使用[主密钥](secure-access-to-data.md#master-keys)或[资源令牌](secure-access-to-data.md#resource-tokens)对文档等资源进行精细访问。<br><br>在[保护对 Azure Cosmos DB 资源的访问](secure-access-to-data.md)中了解详细信息。|
|用户和权限|使用帐户的主密钥可为每个数据库创建用户资源和权限资源。 资源令牌与数据库中的权限相关联，确定用户是否对数据库中的应用程序资源拥有访问权限（读写、只读或无访问权限）。 应用程序资源包括容器、文档、附件、存储过程、触发器和 UDF。 然后，在身份验证期间，将使用资源令牌来允许或拒绝访问资源。<br><br>在[保护对 Azure Cosmos DB 资源的访问](secure-access-to-data.md)中了解详细信息。|
|Active Directory 集成 (RBAC)| 你还可以在 Azure 门户中使用访问控制（IAM）来提供或限制对 Cosmos 帐户、数据库、容器和产品/服务（吞吐量）的访问权限。 IAM 提供基于角色的访问控制并与 Active Directory 集成。 你可以使用内置角色或自定义角色用于个人和组。 有关详细信息，请参阅[Active Directory 集成](role-based-access-control.md)一文。|
|全局复制|Azure Cosmos DB 提供全面的全局分发。只需单击一个按钮，就能将数据复制到 Azure 的任何一个全球数据中心。 全局复制可以实现全局缩放，以较低的延迟访问全球各地的数据。<br><br>从安全的上下文来看，全局复制可确保数据受到保护，防范区域性故障。<br><br>在[全球分布数据](distribute-data-globally.md)中了解详细信息。|
|区域性故障转移|如果已将数据复制到多个数据中心，当区域数据中心脱机时，Azure Cosmos DB 会自动滚动切换操作。 可以使用数据复制到的区域创建故障转移区域的优先级列表。 <br><br>在 [Azure Cosmos DB 中的区域故障转移](high-availability.md)中了解详细信息。|
|本地复制|即使是在单个数据中心内，Azure Cosmos DB 也会自动复制数据来实现高可用性，并允许选择[一致性级别](consistency-levels.md)。 此复制确保所有单区域帐户和具有宽松一致性的所有多区域帐户的 99.99%[可用性 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) ，以及所有多区域数据库帐户的99.999% 读取可用性。|
|自动联机备份|Azure Cosmos 数据库会定期备份并存储在异地冗余存储中。 <br><br>在 [Azure Cosmos DB 的自动联机备份和还原](online-backup-and-restore.md)中了解详细信息。|
|还原已删除的数据|可以使用自动联机备份来恢复大约 30 天内意外删除的数据。 <br><br>在 [Azure Cosmos DB 的自动联机备份和还原](online-backup-and-restore.md)中了解详细信息|
|保护和隔离敏感数据|“新增功能”中列出的区域中的所有数据现已处于静态加密状态。<br><br>可将个人数据和其他机密数据隔离到特定的容器，并限制为只能由特定的用户进行读写或只读访问。|
|监视攻击|使用[审核日志和活动日志](logging.md)，可以监视帐户中的正常和异常活动。 可以查看针对资源执行了哪些操作、操作是谁发起的、操作是何时发生的、操作的状态等，如此表后面的屏幕截图所示。|
|响应攻击|联系 Azure 支持部门举报潜在的攻击行为后，将启动由 5 个步骤构成的事件响应过程。 该 5 步骤过程的目的是在检测到问题并启动调查后，尽快将服务安全性和操作恢复正常。<br><br>在[云中的 Microsoft Azure 安全响应](https://aka.ms/securityresponsepaper)中了解详细信息。|
|地域隔离|Azure Cosmos DB 确保符合主权区域（例如德国、中国和 US Gov）的数据治理要求。|
|受保护的设施|Azure Cosmos DB 中的数据存储在 Azure 的受保护数据中心内的 SSD 上。<br><br>在 [Microsoft 全球数据中心](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)中了解详细信息|
|HTTPS/SSL/TLS 加密|与 Azure Cosmos DB 的所有连接都支持 HTTPS。 Azure Cosmos DB 还支持 TLS 1.2。<br>可以强制服务器端使用最低 TLS 版本。 为此，请联系[azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)。|
|静态加密|存储在 Azure Cosmos DB 中的所有数据都进行静态加密。 在 [Azure Cosmos DB 静态加密](./database-encryption-at-rest.md)中了解详细信息|
|修补的服务器|作为一种托管数据库，在 Azure Cosmos DB 中无需管理和修补服务器，系统会自动完成这些操作。|
|使用强密码的管理帐户|难以相信，我们竟然还要提到这项要求。但与我们的某些竞争产品不同，在 Azure Cosmos DB 中，不带密码的管理帐户是根本不受允许的。<br><br> DocumentDB 中默认融入了基于 SSL 和 HMAC 机密的身份验证安全性。|
|安全和数据保护认证| 若要获取最新的认证列表，请参阅整体[Azure 符合性站点](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings)以及包含所有认证的最新[Azure 符合性文档](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)（搜索 Cosmos）。 若要进一步了解2018年4月25日，请阅读 post [Azure #CosmosDB：安全、私有、符合性，其中包括 SOC 1/2 类型2、HITRUST、PCI DSS 级别1、ISO 27001、HIPAA、FedRAMP 高，等等。

以下屏幕截图显示如何使用审核日志和活动日志监视帐户：![Azure Cosmos DB 的活动日志](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>后续步骤

有关主密钥和资源令牌的详细信息，请参阅[保护对 Azure Cosmos DB 数据的访问](secure-access-to-data.md)。

有关审核日志记录的详细信息，请参阅[Azure Cosmos DB 诊断日志记录](logging.md)。

有关 Microsoft 认证的详细信息，请参阅[Azure 信任中心](https://azure.microsoft.com/support/trust-center/)。