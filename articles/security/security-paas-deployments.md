---
title: "保护 PaaS 部署 | Microsoft 文档"
description: " 了解 PaaS 相比其他云服务模型的安全优势，了解保护 Azure PaaS 部署的建议做法. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b3db1281bbd37fcfbec54bdeb998e6338bb08369
ms.openlocfilehash: c20e0187f7bf6247e4a685c642edfc62303d4ef5


---
# <a name="securing-paas-deployments"></a>保护 PaaS 部署

参考本文中的信息，可以：

- 了解云中的托管应用程序的安全优势
- 评估平台即服务 (PaaS) 相比其他云服务模型的安全优势
- 将安全重心从以网络为中心的方案转换为以标识为中心的外围安全方案
- 实施一般的 PaaS 安全最佳实践建议

## <a name="cloud-security-advantages"></a>云的安全优势
转移到云中可带来一定的安全优势。 在本地环境中，组织的可用资源可能有限，无法尽责在安全措施上投资，使得攻击者能够利用所有层中的漏洞。

![云时代的安全优势][1]

组织可以使用提供商的基于云的安全功能和云智能来改善其威胁检测和响应时间。  通过将责任转移到云提供商，组织可以扩大安全覆盖范围，为其他优先业务重新调配安全资源与预算。

## <a name="division-of-responsibility"></a>责任划分
必须了解你与 Microsoft 之间的责任划分。 在本地，你拥有整个堆栈，但迁移到云后，某些责任将转移到 Microsoft。 以下责任矩阵显示了在 SaaS、PaaS 和 IaaS 部署中，分别由你和 Microsoft 负责的堆栈区域。

![责任区域][2]

对于所有云部署类型，你拥有你的数据和标识。 你需要负责保护由你控制的数据和标识、本地资源及云组件的安全（保护的项目因服务类型而异）。

不管部署类型为何，你都必须负责的项目包括：

- 数据
- 终结点
- 帐户
- 访问管理

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>PaaS 云服务模型的安全优势
让我们使用上面这个责任矩阵来了解一下 Azure PaaS 部署相比本地部署的安全优势。

![PaaS 的安全优势][3]

从堆栈的底部（即物理基础结构）开始，Microsoft 可以消除常见的风险和管理责任。 由于 Microsoft 云受到 Microsoft 的持续监视，因此很难攻破。 攻击者将 Microsoft 云当作攻击目标是不会得逞的。 他们往往会改换目标，除非他们有大量的金钱和资源。  

在堆栈的中间，PaaS 部署与本地之间没有差别。 在应用程序层和帐户与访问管理层，面临的风险是类似的。 本文的后续步骤部分将提供有关消除或尽量避免这些风险的最佳实践指导。

堆栈的顶层（即数据监管和权限管理）存在一种风险，不过可以使用密钥管理来缓解。 （最佳实践中介绍了密钥管理。）尽管密钥管理是一个附加的责任，但你不再需要管理 PaaS 部署中的某些区域，因此可将资源转移到密钥管理。

Azure 平台还使用各种基于网络的技术提供强大的 DDoS 保护。 但是，根据链路和数据中心的不同，所有类型的基于网络的 DDoS 保护方法都有自身的限制。 为了帮助避免大规模 DDoS 攻击造成的影响，可以利用 Azure 的核心云功能快速自动扩展，以防御 DDoS 攻击。 在建议的实践文章中，我们将更详细地介绍如何采取这种措施。

## <a name="modernizing-the-defenders-mindset"></a>革新防御者的思维方式
PaaS 部署为整体安全方案带来了变革。 事必躬亲的局面现在可以改为与 Microsoft 分担责任。

PaaS 与传统本地部署之间的另一个重大差别在于，前者为主要安全边界的界定因素提供了全新的视野。 一直以来，主要的本地安全边界就是网络，大多数本地安全设计都使用网络作为主要安全枢纽。 在 PaaS 部署中，可将标识视为主要安全边界，从而改善安全性。

## <a name="identity-as-the-primary-security-perimeter"></a>标识用作主要安全边界
在云计算的五大基本特征中，一个特征就是网络访问范围广泛，这使得以网络为中心的理念显得有点毫不相干。 许多云计算解决方案的目标是不管用户身居何处，都能允许他们访问资源。 对于大多数用户而言，他们的位置就是 Internet 上的某个节点。

下图演示了安全边界从网络边界演进成标识边界的过程。 安全性越来越少地与如何保护网络相关，而更多地与如何保护数据，以及如何管理应用和用户的安全性相关。 两者的关键差别在于如何为公司的重要资产提供更多的安全保障。

![标识用作新的安全边界][4]

最初，Azure PaaS 服务（例如 Web 角色和 Azure SQL）提供的传统网络边界防护措施很少，或者根本不提供。 开发人员已认识到，设计元素的目的就是在 Internet 上公开（Web 角色），而身份验证可提供新的边界（例如 BLOB 或 Azure SQL）。

新式安全措施假设入侵者会突破网络边界。 因此，新式防护措施已转移到标识。 组织必须使用强身份验证和授权保护机制建立基于标识的安全边界（最佳实践）。

## <a name="recommendations-for-managing-the-identity-perimeter"></a>有关管理标识边界的建议

网络边界的原理和模式早在几十年前就已建立。 相比之下，行业在使用标识作为主要安全边界的经验相对缺乏。 正因如此，我们累积了足够的经验，乐于提供已在现场得到证实的、适用于几乎所有 PaaS 服务的一些普通建议。

下面汇总了标识边界管理方法的一般最佳实践。

- **不会丢失你的密钥或凭据**保护密钥和凭据对于保护 PaaS 部署至关重要。 丢失密钥和凭据是一个常见问题。 一个不错的解决方法是使用集中式解决方案，将密钥和机密存储在硬件安全模块 (HSM) 中。 Azure 在云中提供一个包含 [Azure Key Vault](../key-vault/key-vault-whatis.md) 的 HSM。
- **不要将凭据和其他机密放入源代码或 GitHub**比丢失密钥和凭据更遭糕的唯一问题就是让未经授权的人员获取这些密钥和凭据的访问权限。 攻击者可以利用 bot 技术来查找 GitHub 等代码存储库中存储的密钥和机密。 请不要将密钥和机密放入这些公共源代码存储库。
- **保护混合 PaaS 和 IaaS 服务中的 VM 管理接口**IaaS 和 PaaS 服务在虚拟机 (VM) 上运行。 根据服务的类型，你可以使用多个管理接口来直接远程管理这些 VM。 可以使用远程管理协议，例如[安全外壳协议 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell)、[远程桌面协议 (RDP)](https://support.microsoft.com/kb/186607)和[远程 PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting)。 通常，我们建议不要从 Internet 启用对 VM 的直接远程访问。 在可能的情况下，应使用替代方法，例如，使用虚拟专用网络接入 Azure 虚拟网络。 如果无法使用替代方法，请确保使用复杂的通行短语，并在可能的情况下使用双重身份验证（例如 [Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication.md)）。
- **使用强身份验证和授权平台**

  - 在 Azure AD 而不是自定义用户存储中使用联合标识。 使用联合标识时，可以利用基于平台的方法，将已获授权的标识的管理权限委托给合作伙伴。 如果员工离职后，需要通过多个标识和授权系统反映该信息，则联合标识方法就特别重要。
  - 使用平台提供的身份验证和授权机制，而不要使用自定义代码。 原因是开发自定义身份验证代码可能很容易出错。 大部分开发人员都不是安全专家，不太可能会注意到身份验证和授权的细微之处与最新开发情况。 商业代码（例如 Microsoft 编写的代码）通常会接受广泛的评审。
  - 使用多重身份验证 多重身份验证是最新的身份验证和授权标准，它避免了用户名与密码类型的身份验证所固有的安全漏洞。 需要访问 Azure 管理界面（门户/远程 PowerShell）和面向客户的服务的应用程序应设计并配置为使用 [Azure 多重身份验证 (MFA)](../multi-factor-authentication/multi-factor-authentication.md)。
  - 使用 OAuth2 和 Kerberos 等标准身份验证协议。 这些协议经过广泛的同行评审，有时可实现为平台库的一部分用于身份验证和授权。

## <a name="next-steps"></a>后续步骤
本文重点介绍了 Azure PaaS 部署的安全优势。 接下来，请阅读有关保护 PaaS Web 和移动解决方案的建议做法。 首先，我们将介绍如何保护 Azure 应用服务、Azure SQL 数据库和 Azure SQL 数据仓库。 随着适用于其他 Azure 服务的建议做法文章的发布，我们会在以下列表中提供相应的链接：

- [Web 应用](security-paas-applications-using-app-services.md)
- [Azure SQL 数据库和 Azure SQL 数据仓库](security-paas-applications-using-sql.md)
- Azure 存储空间
- Azure REDIS 缓存
- Azure 服务总线
- Web 应用程序防火墙

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png



<!--HONumber=Feb17_HO1-->


