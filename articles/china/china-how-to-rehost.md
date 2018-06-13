---
title: 将应用程序重新托管和迁移到 Azure 中国世纪互联 | Microsoft Docs
description: 如果已将应用程序或工作负荷部署到全球 Azure，可将其重新托管在 Azure 中国世纪互联，但可能需要对其进行更改。 本页介绍如何改编 Azure Active Directory、Azure 流量管理器、Azure 通知中心和 Azure Key Vault。
services: china
cloud: na
documentationcenter: na
author: v-wimarc
manager: edprice
ms.assetid: na
ms.service: china
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: v-wimarc
ms.openlocfilehash: 73dd210224b51893b5b32acb8ad22787f3607e82
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
ms.locfileid: "23635191"
---
# <a name="rehost-and-migrate-an-application-to-azure"></a>将应用程序重新托管和迁移到 Azure
如果已将应用程序或工作负荷部署到全球 Azure，可将其重新托管在世纪互联运营的 Microsoft Azure（Azure 中国世纪互联），但可能需要进行某些设计更改。 本页介绍在重新托管时如何改编 Azure Active Directory、Azure 流量管理器、Azure 通知中心和 Azure Key Vault。

## <a name="azure-active-directory-services"></a>Azure Active Directory 服务
Microsoft Azure 中国世纪互联将 [Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/) (AD) 列为一项专用服务，专门供在此环境中访问应用程序的用户使用。 Azure AD 标识无法在全球 Azure 与 Azure 中国世纪互联之间同步。 

目前不支持 Azure AD Premium、Azure AD B2C 和 Azure AD 域服务。 如果应用程序使用上述任何服务，必须寻求替代解决方案。 

下表汇总了可在 Azure 上使用的 Azure AD 功能。 

### <a name="basic-edition"></a>Basic 版：

|**功能**  |**全球 Azure**  |**Azure 中国世纪互联**  |
|---------|---------|---------|
|目录对象     |    X     |     X    |
|单一登录 (SSO)     |    X     |    X     |
|云用户的自助密码更改     |    X     |     X    |
|Connect（将本地目录扩展到 Azure AD 的同步引擎）     |     X    |    X     |
|安全和使用情况报告     |     X    |         |
|

### <a name="premium-and-basic-edition"></a>Premium 和 Basic 版：

|**功能**  |**全球 Azure**  |**Azure 中国世纪互联**  |
|---------|---------|---------|
|基于组的访问管理和预配     |     X    |         |
|云用户的自助密码更改     |     X   |         |
|公司品牌（登录页、访问面板自定义）     |     X    |         |
|应用程序代理     |     X    |         |
|SLA     |     X    |         |
|


### <a name="premium-edition"></a>Premium 版：


|**功能**  |**全球 Azure**  |**Azure 中国世纪互联**   |
|---------|---------|---------|
|自助组和应用管理、自助应用程序添加件、动态组     |    X     |         |
|通过本地写回实现自助密码重置、更改或解锁     |     X    |         |
|云中和本地多重身份验证（MFA 服务器）     |    X     |    仅适用于 Azure 门户     |
|Microsoft Identity Manager 用户 CAL*     |    X     |         |
|Cloud App Discovery     |     X    |         |
|Connect Health     |     X    |         |
|组帐户的自动密码滚动更新     |    X     |         |
|

### <a name="azure-ad-b2c-and-social-identity-provider"></a>Azure AD B2C 和社交标识提供者
如果应用程序使用 Azure AD B2C，则必须寻求替代方案。 AD B2C 是全球范围的 Azure 服务，提供一个社交标识提供者来与流行的社交网络（例如 Facebook、Google+、LinkedIn 和 Amazon）集成，但这些社交网络在中国被封锁。 必须专门针对微信、新浪微博或其他已获批准的社交网络定制社交营销策略。

尽管 Microsoft Azure 中国世纪互联目前不支持社交标识提供者，但中国客户顾问团队 (CAT) 一直在研发许多可与微信集成的 Azure 解决方案。 有关开发指南，请与 Microsoft 客户代表联系。

### <a name="set-up-azure-traffic-manager"></a>设置 Azure 流量管理器
在 Microsoft Azure 中国世纪互联，由 Azure 流量管理器管理的应用程序[终结点](https://www.azure.cn/documentation/articles/traffic-manager-endpoint-types/)（中文版）必须托管在中国东部或中国北部数据中心。

流量管理器基于所选的流量路由方法和当前终结点的运行状况，使用 DNS 将用户定向到特定的服务终结点。 流量管理器支持 Azure 虚拟机、Azure Web 应用和其他 Azure 服务的终结点，此外，还支持不在 Azure 中托管的外部终结点。 有关详细信息，请参阅[终结点文档](https://www.azure.cn/documentation/articles/traffic-manager-endpoint-types/)（中文版），或参阅[英文翻译版](https://translate.google.com.hk/translate?hl=zh-CN&sl=zh-CN&tl=en&u=https%3A%2F%2Fwww.azure.cn%2Fdocumentation%2Farticles%2Ftraffic-manager-endpoint-types%2F)）。

重新托管全球 Azure 应用程序时，可通过在全球 Azure 中设置流量管理器，为在中国境外旅行的用户提供支持，然后将流量路由到 Azure 中国世纪互联托管的站点。 确保同步用户数据。 使用[性能流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#performance)配置流量管理器。 如果流量管理器能够将用户路由到根据最低网络延迟测算出的最靠近位置，则可以改善应用的响应能力。

### <a name="set-up-push-notifications"></a>设置推送通知
重新托管使用推送通知的应用程序时，请注意，Azure 通知中心在中国使用不同的平台通知服务 (PNS)。 对于 Android 设备，通知中心对发送到中国境内移动设备的通知使用“百度推送”PNS。

相比之下，全球 Azure [通知中心](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)使用 APNS (Apple Push Notification Services)、GCM (Google Cloud Message)、WNS (Windows Push Notification Services) 和 MPNS (Microsoft Push Notification Service)。

应用程序推送通知的设计应考虑到服务的用户是否在中国境外旅行。 中国境内的用户可以非常顺利地访问 Azure 中托管的应用程序，并可以通过 Azure 通知中心接收通知。 但是，为了改善中国境外旅行的用户在访问应用程序时获得的性能，请考虑添加一个动态路由解决方案。 例如，可以改用[性能流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#performance)将用户重新路由到全球 Azure 中托管的应用程序实例。

### <a name="design-secure-key-management"></a>设计安全的密钥管理
安全的密钥管理对于保护云中的数据至关重要。 可以借助 Azure Key Vault 来保护 Microsoft Azure 中国世纪互联托管的云应用程序和服务所用的加密密钥和机密。 但是，无法导入或生成硬件安全模块 (HSM) - 这是可在全球 Azure 中使用的一项附加功能。

[Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-whatis/) 可以加密密钥和机密，例如身份验证密钥、存储帐户密钥、数据加密密钥、.PFX 文件和密码。 Key Vault 简化了密钥管理过程，可让我们控制用于访问和加密数据的密钥。

## <a name="use-the-global-connection-toolkit"></a>使用全局连接工具包
[Azure 全局连接工具包](https://github.com/Azure/AzureGlobalConnectionToolkit)有助于在国家云之间迁移应用程序，[GitHub](https://github.com/Azure/AzureGlobalConnectionToolkit) 中已提供该工具包。

全局连接工具包提供两个组件：
- **评估工具：** 生成一份报告，让你评估现有的全局 Azure 订阅，并获取迁移规划任务的帮助。 该报告解答有关在不同 Azure 云环境之间迁移 Azure 服务、比较服务和估算成本的问题，并列出重要注意事项。
- **CI/CD（持续集成和持续交付）工具：** 验证和执行迁移。 例如，使用 CI/CD 工具可将虚拟机从 Azure 的东亚区域迁移到中国东部区域。 该工具包可在源与目标订阅之间同步元数据和配置，确保在迁移后，这些信息与原始值匹配。 作为一个开源工具，可以任意对其进行自定义，或将其集成到现有的 DevOps 流程中。 选择支持 Mac、Linux 和 Windows 的 PowerShell 版本或跨平台 NodeJS 版本。

[获取工具包](https://github.com/Azure/AzureGlobalConnectionToolkit)。

## <a name="migrate-azure-classic-virtual-machines"></a>迁移 Azure 经典虚拟机
对于经典 Azure 服务管理器 IaaS 资源，我们强烈建议先[将其迁移到 Azure 资源管理器](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-migration-classic-resource-manager/)，然后再将其转移到 Microsoft Azure 中国世纪互联。 

可以使用 2016 年上旬公开推出的多个工具，将 IaaS 资源或虚拟机从经典 Azure 迁移到 Azure 资源管理器。 例如： 
- [正式推出可将 IaaS 从经典模型迁移到资源管理器的功能](https://azure.microsoft.com/blog/iaas-migration-ga/)
- 
            [使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-ps-migration-classic-resource-manager/)

## <a name="next-steps"></a>后续步骤
- [Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/)
- [终结点文档](https://www.azure.cn/documentation/articles/traffic-manager-endpoint-types/)（中文版）
- [通知中心](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-whatis/)
- [全局连接工具包](https://github.com/Azure/AzureGlobalConnectionToolkit)
- [迁移到 Azure 资源管理器](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-migration-classic-resource-manager/)

