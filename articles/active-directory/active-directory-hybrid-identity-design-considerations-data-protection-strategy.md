---
title: "Azure Active Directory 混合标识设计注意事项 - 定义数据保护策略 | Microsoft 文档"
description: "根据规定的业务要求，为混合标识解决方案定义数据保护策略。"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d4f5d9dbe60e549d270e190e626a87a66696f07c


---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>为混合标识解决方案定义数据保护策略
在此任务中，你将根据规定的业务要求，为混合标识解决方案定义数据保护策略。

* [确定数据保护要求](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [确定内容管理要求](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [确定访问控制要求](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [确定事件响应要求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>定义数据保护选项
如[确定目录同步要求](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)中所述，Microsoft Azure AD 可与本地 Active Directory 域服务 (AD DS) 同步。 通过这种集成，当用户尝试访问企业资源时，组织可以使用 Azure AD 来验证用户的凭据。 可对本地和云中的静态数据使用此方法。  访问 Azure AD 中的数据需要通过安全令牌服务 (STS) 进行用户身份验证。

完成身份验证后，将从身份验证令牌中读取用户主体名称 (UPN)，并确定对应于用户域的复制分区和容器。 授权系统使用有关用户存在性、已启用状态和角色的信息，确定此用户在此会话中对目标租户的访问请求是否已获授权。 特定的已授权操作（具体而言，指创建用户和重置密码）将创建可供租户管理员用来管理法规遵从工作或调查的审核线索。

由于数据量、带宽可用性或其他考虑因素，通过 Internet 连接将数据从本地数据中心转到 Azure 存储并不一定始终可行。 [Azure 存储导入/导出服务](../storage/storage-import-export-service.md)提供基于硬件的选项用于在 Blob 存储中放置/检索大量数据。 使用该服务可将 [BitLocker 加密的](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2)硬盘直接发送到 Azure 数据中心，云操作员可在数据中心将内容上载到存储帐户，或者将 Azure 数据下载到你的驱动器并返还给你。 在此过程中只能使用加密磁盘（使用服务本身在作业设置期间生成的 BitLocker 密钥）。 BitLocker 密钥单独提供给 Azure，以此提供带外密钥共享。

由于数据传输可能在不同的情况下进行，因此还需要了解 Microsoft Azure 使用[虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)隔离租户彼此之间的流量，它所采用的机制有可能是主机和来宾级别防火墙、IP 数据包筛选、端口阻止和 HTTPS 终结点等。 但是，Azure 的内部通信大多也已加密，包括基础结构到基础结构和基础结构到客户（本地）的通信。 另一个重要的方案是 Azure 数据中心内的通信；Microsoft 管理网络，确保没有 VM 可以模拟或窃听其他 VM 的 IP 地址。 访问 Azure 存储或 SQL 数据库，或者在连接到云服务时，将使用 TLS/SSL。 在此情况下，客户的管理员负责获取 TLS/SSL 证书，并将其部署到其租户基础结构。 通过 Microsoft Azure 虚拟网络在相同部署中的虚拟机之间或单个部署中的租户之间移动的数据流量，可通过加密的协议（例如 HTTPS、SSL/TLS 或其他协议）受到保护。

根据在[确定数据保护要求](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)中回答问题的情况，应该能够确定如何保护数据，以及混合标识解决方案如何帮助保护数据。 下表显示 Azure 支持的、可用于各种数据保护方案的选项。

| 数据保护选项 | 云中的静态数据 | 本地静态数据 | 传输中 |
| --- | --- | --- | --- |
| BitLocker 驱动器加密 |X |X | |
| 要加密数据库的 SQL Server |X |X | |
| VM 到 VM 加密 | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> 请阅读 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)提供的[功能合规性](https://azure.microsoft.com/support/trust-center/services/)，详细了解每项 Azure 服务已通过的认证。
> 由于数据保护选项采用多层方式，因此在运行此任务时无法比较这些选项。 请确保根据数据所处的每种状态，利用所有可用选项。
> 
> 

## <a name="define-content-management-options"></a>定义内容管理选项
使用 Azure AD 管理混合标识基础结构的优点之一是整个过程对用户而言是完全透明的。 用户尝试访问共享资源，资源需要身份验证，因此用户必须将身份验证请求发送到 Azure AD 才能够获取令牌和访问资源。 整个过程在后台发生，无需用户交互。 还可以向某个用户[组](active-directory-manage-groups.md#getting-started-with-access-management)授权，使其能够执行某些常见操作。

有数据隐私顾虑的组织通常需要对其解决方案进行数据分类。 如果其当前本地基础结构已使用数据分类，就能够使用 Azure AD 作为用户标识的主要存储库。 在 Windows Server 2012 R2 中，本地用于数据分类的常用工具称为[数据分类工具包](https://msdn.microsoft.com/library/Hh204743.aspx)。 借助此工具可以标识、分类和保护私有云中文件服务器上的数据。 还可以使用 Windows Server 2012 中的[自动文件分类](https://technet.microsoft.com/library/hh831672.aspx)来实现此目的。

如果组织目前没有数据分类机制，但需要在本地不添加服务器的情况下保护敏感文件，可以使用 [Microsoft Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx)。  Azure RMS 使用加密、标识和授权策略帮助保护文件和电子邮件，并且可跨多个设备运行 - 手机、平板电脑和 PC。 Azure RMS 属于云服务，因此无需显式设置对其他组织的信任，即可与他们共享受保护的内容。 如果其他组织已有 Office 365 或 Azure AD 目录，则自然而然支持跨组织协作。 还可以使用 Azure Active Directory 同步服务（AAD 同步）或 Azure AD Connect 来只同步 Azure RMS 支持本地 Active Directory 帐户的通用标识所需的目录属性。

了解谁正在访问哪个资源，是内容管理的重要一部分，因此强大的记录功能对于标识管理解决方案而言非常重要。 Azure AD 提供超过 30 天的日志，包括：

* 角色成员身份的更改（例如：已将用户添加到全局管理员角色）
* 凭据更新（例如：密码更改）
* 域管理（例如：验证自定义域、删除域）
* 添加或删除应用程序
* 用户管理（例如：添加、删除、更新用户）
* 添加或删除许可证

> [!NOTE]
> 请阅读 [Microsoft Azure 安全和审核日志管理](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)详细了解 Azure 中的日志记录功能。
> 根据在[确定内容管理要求](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)中回答问题的情况，应该能够确定在混合标识解决方案中要如何保护内容。 尽管表 6 中显示的所有选项都能够与 Azure AD 集成，但请务必定义更适合业务要求的选项。
> 
> 

| 内容管理选项 | 优点 | 缺点 |
| --- | --- | --- |
| 本地集中式 (Active Directory Rights Management Server) |完全控制负责分类数据的服务器基础结构 <br> Windows Server 的内置功能，无需购买额外的许可证或订阅 <br> 可与混合方案中的 Azure AD 集成 <br> 支持 Microsoft Online Services，例如 Exchange Online 和 SharePoint Online 以及 Office 365 中的信息权限管理 (IRM) 功能 <br> 支持本地 Microsoft 服务器产品，例如 Exchange Server、SharePoint Server，以及运行 Windows Server 和文件分类基础结构 (FCI) 的文件服务器。 |需要较多的维护（随时进行更新、配置和潜在升级），因为 IT 拥有服务器 <br> 需要本地服务器基础结构<br> 在本机状态下不使用 Azure 功能 |
| 云中集中式 (Azure RMS) |比本地解决方案更容易管理 <br> 可与混合方案中的 AD DS 集成 <br>  完全与 Azure AD 集成 <br> 无需本地服务器即可部署服务 <br> 支持本地 Microsoft 服务器产品，例如 Exchange Server、SharePoint Server，以及运行 Windows Server 和文件分类基础结构 (FCI) 的文件服务器 <br> IT 可通过 BYOK 功能完全控制其租户的密钥。 |组织必须具有支持 RMS 的云订阅 <br> 组织必须有 Azure AD 目录才能支持 RMS 的用户身份验证 |
| 混合（Azure RMS 与本地 Active Directory Rights Management Server 集成） |此方案结合了本地集中式和云中集成式的优点。 |组织必须具有支持 RMS 的云订阅 <br> 组织必须有 Azure AD 目录才能支持 RMS 的用户身份验证 <br> 需要在 Azure 云服务与本地基础结构之间建立连接 |

## <a name="define-access-control-options"></a>定义访问控制选项
公司可以借助 Azure AD 中提供的身份验证、授权和访问控制功能来使用中央标识存储库，同时允许用户与合作伙伴使用单一登录 (SSO)，如下图所示：

![](./media/hybrid-id-design-considerations/centralized-management.png)

集中式管理以及与其他目录的完全集成

Azure Active Directory 为数千种 SaaS 应用程序与本地 Web 应用程序提供单一登录。 有关 Microsoft 已测试的第三方 SSO 解决方案的详细信息，请参阅 [Azure Active Directory 联合兼容性列表：可用于实现单一登录的第三方标识提供者](https://msdn.microsoft.com/library/azure/jj679342.aspx)一文。 此功能可让组织实现各种 B2B 方案，同时可以控制标识和访问权限管理。 但是，在 B2B 设计过程中，必须了解合作伙伴将要使用的身份验证方法，并验证 Azure 是否支持此方法。 下面是 Azure AD 目前支持的方法：

* 安全断言标记语言 (SAML)
* OAuth
* Kerberos
* 令牌
* 证书

> [!NOTE]
> 请阅读 [Azure Active Directory 身份验证协议](https://msdn.microsoft.com/library/azure/dn151124.aspx)，详细了解 Azure 中的每个协议及其功能。
> 
> 

通过 Azure AD 支持，移动业务应用程序可以使用相同的简单移动服务身份验证体验，让员工使用企业的 Active Directory 凭据登录到其移动应用程序。 通过此功能，Azure AD 可受到支持作为移动服务中的标识提供者，与支持的其他标识提供者（包括 Microsoft 帐户、Facebook ID、Google ID 和 Twitter ID）配合运行。 如果本地应用程序使用位于公司 AD DS 的用户凭据，则来自云的第三方和用户所做的访问应该是透明的。 可以使用条件性访问控制管理用户对（基于云）Web 应用程序、Web API、Microsoft 云服务、第三方 SaaS 应用程序和本机（移动）客户端应用程序的访问，并且可以在一个位置实现安全性、审核和报告。 但是，建议在非生产环境中或用户数量有限的环境中进行此验证。

> [!TIP]
> 必须提到的是，Azure AD 不像 AD DS 一样具有组策略。 若要为设备强制实施策略，需要使用移动设备管理解决方案，例如 [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx)。
> 
> 

使用 Azure AD 验证用户身份之后，必须评估用户将要获得的访问级别。 用户对资源的访问级别可能改变。由于 Azure AD 可通过控制对某些资源的访问而增加额外的安全级别，因此必须记住，资源本身也可能有其本身的访问控制列表，例如，对位于文件服务器中的文件的访问控制。 下图汇总了可以在混合方案中使用的访问控制级别：

![](./media/hybrid-id-design-considerations/accesscontrol.png)

图 X 中显示的每个交互分别代表一个可在 Azure AD 中引入的访问控制方案。 每种方案的说明如下：

1.对在本地托管的应用程序的条件性访问：可以将已注册的设备与配置为将 AD FS 与 Windows Server 2012 R2 一起使用的应用程序的访问策略一起使用。 有关设置本地条件性访问的详细信息，请参阅 [使用 Azure Active Directory 设备注册设置本地条件性访问](active-directory-conditional-access-on-premises-setup.md)。
2. 对 Azure 管理门户的访问控制：Azure 还能使用 RBAC（基于角色的访问控制）来控制对管理门户的访问。 公司可以使用此方法限制有权访问 Azure 管理门户的个人可以执行的操作数量。 使用 RBAC 控制对门户的访问时，IT 管理员可通过下列访问管理方式委派访问权限：

* 基于组的角色分配：为可从本地 Active Directory 同步的 Azure AD 组分配访问权限。 这使你能够充分利用组织在工具和组管理过程中已有的现有投资。 你也可以使用 Azure AD 高级版中的委派组管理功能。
* 充分利用 Azure 中的内置角色：可以使用三个角色 - 所有者、参与者和阅读人员，确保用户和组仅具有完成工作所需任务的权限。
* 对资源的细致访问：针对特定的订阅、资源组或单独的 Azure 资源（例如，网站或数据库），可以将角色分配给用户和组。 这样，可以确保用户有权访问所有所需资源，并且无权访问不需要管理的资源。

> [!NOTE]
> 请阅读 [Azure 中基于角色的访问控制](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/)详细了解此功能。 构建应用程序并想要自定义其访问控制的开发人员可以使用 Azure AD 应用程序角色进行授权。 请参阅 [WebApp-RoleClaims-DotNet 示例](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet)，了解如何构建使用此功能的应用。
> 
> 

3.使用 Microsoft Intune 的 Office 365 应用程序的条件性访问 ：IT 管理员可以预配条件性访问设备策略来保护公司资源，同时允许信息工作者在符合条件的设备上访问服务。 有关详细信息，请参阅 [Office 365 服务的条件性访问设备策略](active-directory-conditional-access-device-policies.md)。

4.适用于 SaaS 应用的 Azure 条件性访问：使用[此功能](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx)可以配置基于应用程序的多重身份验证，以及阻止不在受信任网络中的用户的访问。 可以将多重身份验证规则应用于已分配给该应用程序的所有用户，或者仅应用于指定安全组中的用户。 如果用户是从组织网络内部的 IP 地址访问应用程序，则可能不需要进行多重身份验证。

由于访问控制选项采用多层方式，因此在运行此任务时无法比较这些选项。 请确保针对每个要求控制资源访问权限的方案使用所有适用的选项。

## <a name="define-incident-response-options"></a>定义事件响应选项
Azure AD 可通过监视用户活动，帮助 IT 人员识别环境中潜在的安全风险；IT 人员可使用 Azure AD 访问和使用量报告详细了解组织目录的完整性与安全性。 使用此信息，IT 管理员能够更好地确定可能发生安全风险的位置，以便做出充分的规划来缓解这些风险。  [Azure AD Premium 订阅](active-directory-get-started-premium.md)提供一组安全报告来让 IT 人员获取此信息。 [Azure AD 报告](active-directory-view-access-usage-reports.md)的分类如下：

* **异常报告**：包含存在异常的登录事件。 我们的目标是让你知道这项活动并使你能够就事件是否可疑做出决定。
* **集成式应用程序报告**：就组织如何使用云应用程序提供见解。 Azure Active Directory 提供与数千个云应用程序的集成。
* **错误报告**：指示在为外部应用程序预配帐户时可能发生的错误。
* **用户特定的报告**：显示特定用户的设备/登录活动数据。
* **活动日志**：包含过去 24 小时、过去 7 天或过去 30 天内的所有已审核事件的记录，以及组活动更改记录、密码重置和注册活动记录。

> [!TIP]
> 此外，可帮助事件响应团队处理案例的另一份报告是[凭据已泄漏的用户](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx)报告。  此报告呈现这些泄漏凭据列表与租户之间的所有匹配项。
> 
> 

Azure AD 中还提供其他可在事件响应调查期间使用的重要内置报告，包括：

* **密码重置活动**：可让管理员详细了解组织中使用密码重置的频率。
* **密码重置注册活动**：提供有关哪些用户注册了密码重置方法，以及他们选择了哪些方法的深入信息。
* **组活动**：提供在访问面板中对组启动的更改的历史记录（例如：添加或删除用户）。

除了 Azure AD Premium 提供的可在事件响应调查期间使用的核心报告功能以外，IT 人员还可以使用审核报告获取信息，例如：

* 角色成员身份的更改（例如：已将用户添加到全局管理员角色）
* 凭据更新（例如：密码更改）
* 域管理（例如：验证自定义域、删除域）
* 添加或删除应用程序
* 用户管理（例如：添加、删除、更新用户）
* 添加或删除许可证

由于事件响应选项采用多层方式，因此在运行此任务时无法比较这些选项。 请确保针对每个要求在公司的事件响应过程中使用 Azure AD 报告功能的方案使用所有适用的选项。

## <a name="next-steps"></a>后续步骤
[确定混合标识管理任务](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](active-directory-hybrid-identity-design-considerations-overview.md)




<!--HONumber=Dec16_HO5-->


