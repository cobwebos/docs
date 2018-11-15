---
title: 保护 Azure 安全中心的 Azure SQL 服务和数据 | Microsoft Docs
description: 本文档介绍了 Azure 安全中心提供的建议，这些建议有助于保护数据和 Azure SQL 服务，并遵守安全策略。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: terrylan
ms.openlocfilehash: 177deb779ca3e3e9575a41ab9a37bb51d5e79df8
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008070"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>在 Azure 安全中心保护 Azure SQL 服务和数据
Azure 安全中心可分析 Azure 资源的安全状态。 在安全中心识别潜在的安全漏洞时，它会创建一些建议，这些建议会指导完成配置所需控件的过程。  适用于 Azure 资源类型的建议：虚拟机 (VM)、网络、SQL 和数据，以及应用程序。

本文介绍适用于 Azure SQL 服务和数据的建议。 启用 Azure SQL 服务器和数据库审核、启用 SQL 数据库加密和启用 Azure 存储帐户加密的建议中心。  使用下表作为参考，以便了解可用的 SQL 服务和数据的建议，以及应用建议后每个建议的做法。
### <a name="monitor-data-security"></a>监视数据安全性

单击“预防”部分中的“数据安全性”时，“数据资源”会打开，其中显示了有关 SQL 和存储的建议。 此外还有针对数据库常规运行状况的[建议](security-center-sql-service-recommendations.md)。 有关存储加密的详细信息，请阅读 [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md)（在 Azure 安全中心为 Azure 存储帐户启用加密）。

![数据资源](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

在“SQL 建议”下面，可以单击任一建议，获取有关进一步操作的更多详细信息来解决问题。 以下示例显示了“SQL 数据库的数据库审核与威胁检测”建议的补充说明。

![有关 SQL 建议的详细信息](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

“对 SQL 数据库启用审核与威胁检测”包含以下信息：

* SQL 数据库列表
* 所在的服务器
* 此设置是从服务器继承的，还是此数据库独有的
* 当前状态
* 问题的严重性

单击数据库处理建议时，会打开“审核和威胁检测”，如以下屏幕截图所示。

![审核和威胁检测](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

若要启用审核，请在“审核”选项下选择“启用”。

## <a name="data-and-storage-recommendations"></a>数据和存储建议

|资源类型|安全功能分数|建议|Description|
|----|----|----|----|
|存储帐户|20|需要安全传输到存储帐户|安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击。|
|Redis|20|仅启用到 Redis 缓存的安全连接|仅启用通过 SSL 来与 Redis 缓存建立连接。 使用安全连接可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击。|
|SQL|15|启用 SQL 数据库的透明数据加密|启用透明数据加密以保护静态数据并满足合规性要求。|
|SQL|15|对 SQL 服务器启用审核|启用 Azure SQL 服务器的审核。 （仅 Azure SQL 服务。 不包括在虚拟机上运行的 SQL。）|
|SQL|15|对 SQL 数据库启用审核|启用 Azure SQL 数据库的审核。 （仅 Azure SQL 服务。 不包括在虚拟机上运行的 SQL。）|
|Data Lake Analytics|15|启用 Data Lake Analytics 的静态加密|启用透明数据加密以保护 Data Lake Analytics 中的静态数据。 静态加密是透明的，这意味着，Data Lake Analytics 会自动在保存之前加密数据，在检索之前解密数据。 无需出于加密而在与 Data Lake Analytics 交互的应用程序和服务中进行更改。 静态加密可以最大程度地减少由于实物失窃造成的数据丢失风险，而且还有助于满足合规性要求。|
|Data Lake Store|15|启用 Data Lake Store 的静态加密|启用透明数据加密以保护 Data Lake Store 中的静态数据。 静态加密是透明的，这意味着，Data Lake Store 会自动在保存之前加密数据，在检索之前解密数据。 无需为了适应加密，而在与 Data Lake Store 交互的应用程序和服务中进行更改。 静态加密可以最大程度地减少由于实物失窃造成的数据丢失风险，而且还有助于满足合规性要求。|
|存储帐户|15|为 Azure 存储帐户启用加密|为静态数据启用 Azure 存储服务加密。 存储服务加密 (SSE) 的工作原理是，在将数据写入 Azure 存储时对数据进行加密，以及在检索前对数据进行解密。 SSE 当前仅适用于 Azure Blob 服务，并可用于块 blob、页 blob 和追加 blob。|
|Data Lake Analytics|5|启用 Data Lake Analytics 中的诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|Data Lake Store|5|启用 Azure Data Lake Store 中的诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|SQL|30|修复 SQL 数据库中的漏洞|SQL 漏洞评估会扫描数据库中的安全漏洞，并显示与最佳实践之间的任何偏差，如配置错误、权限过多和敏感数据未受保护。 解决发现的漏洞可以极大地改善数据库安全态势。|
|SQL|20|预配 SQL Server 的 Azure AD 管理员|预配 SQL Server 的 Azure AD 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。|
|存储帐户|15|禁用对存储帐户进行无限制的网络访问|在存储帐户防火墙设置中审核无限制的网络访问权限。 应该配置网络规则，以便只有来自许可网络的应用程序才能访问存储帐户。 若要允许特定的 Internet 或本地客户端建立连接，可以授予对来自特定 Azure 虚拟网络的流量或者对公共 Internet IP 地址范围的访问权限。|
|存储帐户|1||将存储帐户迁移到新 AzureRM 资源|为存储帐户使用新的 Azure 资源管理器 v2 以提供安全增强功能，例如：更强的访问控制(RBAC)、更好地审核、基于资源管理器的部署和治理、托管标识访问权限、用于提供机密的 Key Vault 的访问权限、基于 Azure AD 的身份验证以及可实现更轻松安全管理的标记和资源组支持。|



## <a name="see-also"></a>另请参阅
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

* [保护 Azure 安全中心中的虚拟机](security-center-virtual-machine-recommendations.md)
* [保护 Azure 安全中心中的应用程序](security-center-application-recommendations.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。
