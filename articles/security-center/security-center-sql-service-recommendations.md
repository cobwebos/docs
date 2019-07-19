---
title: 保护 Azure 安全中心的 Azure 数据和存储服务 |Microsoft Docs
description: 本文档介绍了 Azure 安全中心提供的建议，这些建议有助于保护数据和 Azure SQL 服务，并遵守安全策略。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: v-mohabe
ms.openlocfilehash: 9e48114d0d4159d40006710f9c8194dea0d775f8
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295634"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>保护 Azure 安全中心的 Azure 数据和存储服务
本主题说明如何查看和实施有关数据和存储资源的安全建议。 Azure 安全中心在分析 Azure 资源的安全状态时发现这些建议。

## <a name="view-your-data-security-information"></a>查看数据安全信息

1. 在 "**资源安全卫生**" 部分中, 单击 "**数据和存储资源**"。

   ![数据 & 存储资源](./media/security-center-monitoring/click-data.png)

    此时会打开 "**数据安全**" 页, 其中包含对数据资源的建议。

     ![数据资源](./media/security-center-monitoring/sql-overview.png)

从此页中, 你可以:

* 单击 "**概述**" 选项卡列出要修正的所有数据资源建议。 
* 单击每个选项卡, 并按资源类型查看建议。

    > [!NOTE]
    > 有关存储加密的详细信息，请阅读 [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md)（在 Azure 安全中心为 Azure 存储帐户启用加密）。


## <a name="remediate-a-recommendation-on-a-data-resource"></a>修正对数据资源的建议

1. 在任一资源选项卡中, 单击某个资源。 此时将打开 "信息" 页, 其中列出了要修正的建议。

    ![资源信息](./media/security-center-monitoring/sql-recommendations.png)

2. 单击 "建议"。 此时会打开 "建议" 页, 其中显示了用于实施建议的**更正步骤**。

   ![修正步骤](./media/security-center-monitoring/remediate1.png)

3. 单击 "**执行操作**"。 此时将显示 "资源设置" 页。

    ![启用建议](./media/security-center-monitoring/remediate2.png)

4. 遵循**更正步骤**并单击 "**保存**"。

## <a name="data-and-storage-recommendations"></a>数据和存储建议

|资源类型|安全功能分数|建议|描述|
|----|----|----|----|
|存储帐户|20|应该启用安全传输到存储帐户|安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 HTTPS 可确保服务器与服务之间的身份验证, 并防止数据在传输过程中受到网络层攻击, 如中间人、窃听和会话劫持。|
|Redis|20|只应启用与 Redis 缓存的安全连接|仅启用通过 SSL 来与 Azure Redis 缓存建立连接。 使用安全连接可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击。|
|SQL|15|应启用 SQL 数据库上的透明数据加密|启用透明数据加密以保护静态数据并满足合规性要求。|
|SQL|15|应启用 SQL server 审核|启用 Azure SQL 服务器的审核。 （仅 Azure SQL 服务。 不包括在虚拟机上运行的 SQL。）|
|Data Lake Analytics|5|应启用 Data Lake Analytics 中的诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|Data Lake Store|5|应启用 Azure Data Lake Store 中的诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|SQL|30|应修正 SQL 数据库上的漏洞|SQL 漏洞评估会扫描数据库中的安全漏洞, 并公开最佳实践的任何偏差, 如配置错误、权限过多以及未受保护的敏感数据。 解决发现的漏洞可以极大地改善数据库安全态势。|
|SQL|20|预配 SQL Server 的 Azure AD 管理员|预配 SQL Server 的 Azure AD 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。|
|存储帐户|15|应限制对具有防火墙和虚拟网络配置的存储帐户的访问权限|在存储帐户防火墙设置中审核无限制的网络访问权限。 应该配置网络规则，以便只有来自许可网络的应用程序才能访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接, 可以授予对来自特定 Azure 虚拟网络或公共 Internet IP 地址范围的流量的访问权限。|
|存储帐户|第|应将存储帐户迁移到新的 Azure 资源管理器资源|为存储帐户使用新的 Azure 资源管理器 v2, 以提供安全增强功能, 例如: 更强的访问控制 (RBAC)、更好的审核、基于资源管理器的部署和监管、对托管标识的访问、对的密钥保管库的访问权限机密和基于 Azure AD 的身份验证, 以及对标记和资源组的支持, 以便更轻松地进行安全性管理。|

## <a name="see-also"></a>请参阅
若要详细了解适用于其他 Azure 资源类型的建议, 请参阅以下主题:

* [保护 Azure 安全中心中的虚拟机](security-center-virtual-machine-recommendations.md)
* [保护 Azure 安全中心中的应用程序](security-center-application-recommendations.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)

若要了解有关安全中心的详细信息, 请参阅以下主题:

* [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。
