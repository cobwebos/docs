---
title: Azure Active Directory 概念证明操作手册：构建基块 | Microsoft Docs
description: 研究并快速实现标识和访问管理方案
services: active-directory
keywords: azure active directory 操作手册, 概念证明, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: 1efb8d89b0a78dcf88c60c2e8cd3b968a725e8b9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Azure Active Directory 概念证明操作手册：构建基块

## <a name="catalog-of-roles"></a>角色的目录

| 角色 | 说明 | 概念证明 (PoC) 责任 |
| --- | --- | --- |
| 标识体系结构/开发团队 | 此团队通常负责设计解决方案、实现原型、推动审批但最终不涉及运营 | 他们提供环境，并从可管理性角度评估不同的方案 |
| **本地标识运营团队** | 管理不同的本地标识源：Active Directory 林、LDAP 目录、HR 系统和联合身份验证标识提供程序。 | 提供 PoC 方案所需的对本地资源的访问权限。<br/>他们应尽量少涉及|
| **应用程序技术所有者** | 将与 Azure AD 集成的不同云应用和服务的技术所有者 | 提供有关 SaaS 应用程序（可能是用于测试的实例）的详细信息 |
| **Azure AD 全局管理员** | 管理 Azure AD 配置 | 提供用于配置同步服务的凭据。 在 PoC 过程中通常与标识体系结构是同一团队，但是在运营阶段通常是独立团队|
| **数据库团队** | 数据库基础结构的所有者 | 提供对 SQL 环境（ADFS 或 Azure AD Connect）的访问权限，为特定方案做准备工作。<br/>他们应尽量少涉及 |
| **网络团队** | 网络基础结构的所有者 | 提供同步服务器在网络级别的所需访问权限，以便正常访问数据源和云服务（防火墙规则、打开的端口、IPSec 规则等） |
| 安全团队 | 定义安全策略、分析来自各种源的安全报告并遵循分析结果。 | 提供目标安全评估方案 |

## <a name="common-prerequisites-for-all-building-blocks"></a>所有构建基块的常见先决条件

以下是使用 Azure AD Premium 进行任何 POC 所需的部分先决条件。

| 先决条件 | 资源 |
| --- | --- |
| 使用有效的 Azure 订阅定义的 Azure AD 租户 | [如何获取 Azure Active Directory 租户](active-directory-howto-tenant.md)<br/>**请注意：**如果环境中已经具备 Azure AD Premium 许可证，则可以通过导航到 https://aka.ms/accessaad 来获取零上限订阅 <br/>请访问 https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ 和 https://technet.microsoft.com/library/dn832618.aspx 了解详细信息 |
| 已定义和验证域 | [将自定义域名添加到 Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**注意：**Power BI 等工作负荷可能已经预配了其包含的 Azure AD 租户。 要检查给定域是否与租户关联，请导航到 https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration。 如果收到成功的相应，则域已经分配给租户，并且可能需要接管。 在这种情况下，请联系 Microsoft 获取更多指导。 如需了解有关接管选项的详细信息，请参阅[什么是 Azure 自助式服务注册？](active-directory-self-service-signup.md) |
| 已启用 Azure AD Premium 或 EMS 试用版 | [Azure Active Directory Premium 免费使用一个月](https://azure.microsoft.com/trial/get-started-active-directory/) |
| 已经向 PoC 用户分配了 Azure AD Premium 或 EMS 许可证 | [向你自己以及 Azure Active Directory 中用户分配许可证](active-directory-licensing-get-started-azure-portal.md) |
| Azure AD 全局管理员凭据 | [在 Azure Active Directory 中分配管理员角色](active-directory-assign-admin-roles-azure-portal.md) |
| 此项是可选项，但强烈建议执行：将实验室环境并行为回退 | [Azure AD Connect 的先决条件](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>目录同步 - 密码哈希同步 (PHS) - 新安装

估计完成时间：低于 1,000 位 PoC 用户的情况下大约一小时

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 运行 Azure AD Connect 的服务器 | [Azure AD Connect 的先决条件](./connect/active-directory-aadconnect-prerequisites.md) |
| 目标 POC 用户，在同一域中，并且属于安全组和 OU | [Azure AD Connect 的自定义安装](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| 已标识 POC 所需的 Azure AD Connect 功能 | [将 Active Directory 连接到 Azure Active Directory - 配置同步功能](./connect/active-directory-aadconnect.md#configure-sync-features) |
| 已具有本地和云环境所需的凭据  | [Azure AD Connect：帐户和权限](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 下载最新版 Azure AD Connect | [下载 Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| 使用最简单的路径安装 Azure AD Connect，即 Express <br/>1.筛选出目标 OU，以最大限度地缩短同步周期时间<br/>2.在本地组中选择目标用户群。<br/>3.部署其他 POC 主题需要的功能 | [Azure AD Connect：自定义安装：域和 OU 筛选](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect：自定义安装：根据组进行筛选](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect：将本地标识与 Azure Active Directory 集成：配置同步功能](./connect/active-directory-aadconnect.md#configure-sync-features) |
| 打开 Azure AD Connect UI，并查看已完成的运行配置文件（导入、同步和导出） | [Azure AD Connect 同步：计划程序](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| 打开 [Azure AD 管理门户](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/)，转到“所有用户”边栏选项卡，添加“授权来源”列，之后将会显示出用户，再将其正确标记为来源于“Windows Server AD” | [Azure AD 管理门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>注意事项

1. 请在[此处](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)查看密码哈希同步的安全注意事项。  如果确实不能选择试点生产用户的密码哈希同步，请考虑使用以下替代方法：
   * 在生产域中创建测试用户。 确保不会同步任何其他帐户
   * 转到 UAT 环境
2.  如果想进行联合身份验证，则有必要了解将联合身份验证与本地标识提供程序相关联所产生的费用不包含在 POC 中，并且有必要权衡此费用与你想获得的优势之间的利弊：
    * 它是关键路径，因此应该设计为高可用性
    * 它是容量计划所需的本地服务
    * 它是需要进行监视/维护/修补的本地服务

有关详细信息，请参阅[了解 Office 365 标识和 Azure Active Directory - 联合身份](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>品牌

估计完成时间：15 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 资产（图像、徽标等）；为获得最佳的可视化效果，请确保资产大小为建议的大小。 | [在 Azure Active Directory 登录页中添加公司品牌元素](active-directory-branding-custom-signon-azure-portal.md) |
| 可选项：如果环境具有 ADFS 服务器，可通过访问该服务器来自定义 Web 主题 | [AD FS 用户登录自定义](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| 执行最终用户登录体验的客户端计算机 |  |
| 可选项：用于验证体验的移动设备 |  |

### <a name="steps"></a>Steps

| 步骤 | 资源 |
| --- | --- |
| 转到 Azure AD 管理门户 | [Azure AD 管理门户 - 公司品牌](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| 上传登录页的资产（英雄徽标、小型徽标、标签等）。 或者，如果拥有 AD FS，也可以使用与 ADFS 登录页相同的资产 | [在登录页和访问面板页中添加公司品牌：可自定义的元素](customize-branding.md) |
| 等待几分钟时间使更改完全生效 |  |
| 使用 POC 用户凭据登录 https://myapps.microsoft.com |  |
| 在浏览器中查看呈现效果 | [在登录页和访问面板页中添加公司品牌](customize-branding.md) |
| 也可以在其他设备中查看呈现效果 |  |

### <a name="considerations"></a>注意事项

如果执行自定义之后仍然是之前的呈现效果，请刷新浏览器客户端缓存，并重试该操作。

## <a name="group-based-licensing"></a>根据组进行授权

估计完成时间：10 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 所有的 POC 用户都属于安全组（云中或本地上） | [在 Azure Active Directory 中创建组并添加成员](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 在 Azure AD 管理门户中转到许可证边栏选项卡 | [Azure AD 管理门户：授权](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| 将许可证分配给具有 POC 用户的安全组。 | [将许可证分配给 Azure Active Directory 中的用户组](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>注意事项

遇到任何问题，请转到[与在 Azure Active Directory 中使用组管理许可相关的方案、限制和已知问题](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>SaaS 联合 SSO 配置

估计完成时间：60 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| SaaS 应用程序的测试环境可用。 本指南使用 ServiceNow 作为示例。<br/>强烈建议使用测试实例来最大程度地减少导航现有数据质量和映射上的冲突。 | 转到 https://developer.servicenow.com/app.do#!/home 以开始获取测试实例的过程 |
| 对 ServiceNow 管理控制台的管理员访问权限 | [教程：Azure Active Directory 与 ServiceNow 集成](active-directory-saas-servicenow-tutorial.md) |
| 要分配应用程序的目标用户群。 建议使用包含 PoC 用户的安全组。 <br/>如果无法创建组，请直接向用户分配应用程序进行 PoC | [在 Azure Active Directory 中向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 从 Microsoft 文档将教程共享给所有执行组件  | [教程：Azure Active Directory 与 ServiceNow 集成](active-directory-saas-servicenow-tutorial.md) |
| 设置一个工作会议，并按照每个执行组件的教程步骤进行操作。 | [教程：Azure Active Directory 与 ServiceNow 集成](active-directory-saas-servicenow-tutorial.md) |
| 将应用分配给在先决条件中标识的组。 如果 POC 在作用域中具有条件性访问，则可以稍后再次访问并添加 MFA 等。 <br/>请注意，这会在预配过程中启动（如果配置） |  [在 Azure Active Directory 中向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md) <br/>[在 Azure Active Directory 中创建组并添加成员](active-directory-groups-create-azure-portal.md) |
| 使用 Azure AD 管理门户从库中添加 ServiceNow 应用程序| [Azure AD 管理门户：企业应用程序](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Azure Active Directory 中企业应用程序管理的新增功能](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| 在 ServiceNow 应用的“单一登录”边栏选项卡中，启用“基于 SAML 的登录” |  |
| 使用 ServiceNow URL 填写“登录 URL”和“标识符”字段<br/>选中“使新证书处于活动状态”框<br/>并保存设置 |  |
| 打开面板底部的“配置 ServiceNow”边栏选项卡，以查看配置 ServiceNow 的自定义说明 |  |
| 按照说明配置 ServiceNow |  |
| 在 ServiceNow 应用的“预配”边栏选项卡中，启用“自动”设置 | [在新的 Azure 门户中管理企业应用的用户帐户预配](active-directory-enterprise-apps-manage-provisioning.md) |
| 稍等几分钟，等待预配完成。  在此期间，可以查看预配报告 |  |
| 以具有访问权限的测试用户身份登录 https://myapps.microsoft.com/ | [访问面板是什么？](active-directory-saas-access-panel-introduction.md) |
| 单击刚刚创建的应用程序的磁贴。 确认访问 |  |
| 或者可以查看应用程序使用情况报告。 请注意，存在一定程度的延迟，因此需要等待一段时间才能在报告中看到流量。 | [Azure Active Directory 门户中的登录活动报告：托管应用程序的使用情况](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory 报告保留策略](active-directory-reporting-retention.md) |

### <a name="considerations"></a>注意事项

1. 以上[教程](active-directory-saas-servicenow-tutorial.md)涉及到了旧版的 Azure AD 管理体验。 而 PoC 以[快速启动](active-directory-enterprise-apps-whats-new-azure-portal.md#quick-start-get-going-with-your-new-application-right-away)体验为基础。
2. 如果库中没有目标应用程序，则可以使用“提供自己的应用”。 有关详细信息，请参阅 [Azure Active Directory 中企业应用程序管理的新增功能：从一个位置添加自定义应用程序](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>SaaS 密码 SSO 配置

估计完成时间：15 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| SaaS 应用程序的测试环境。 HipChat 和 Twitter 就是密码 SSO 的示例。 而其他的任何应用程序都需要 html 登录窗体页面的确切 URL。 | [Microsoft Azure Marketplace 上的 Twitter](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Microsoft Azure Marketplace 上的 HipChat](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| 应用程序的测试帐户。 | [注册 Twitter](https://twitter.com/signup?lang=en)<br/>[免费注册：HipChat](https://www.hipchat.com/sign_up) |
| 要分配应用程序的目标用户群。 建议使用包含用户的安全组。 | [在 Azure Active Directory 中向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md) |
| 对计算机的本地管理员访问权限，以便为 Internet Explorer、Chrome 或 Firefox 部署访问面板扩展 | [适用于 IE 的访问面板扩展](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[适用于 Chrome 的访问面板扩展](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[适用于 Firefox 的访问面板扩展](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 安装浏览器扩展 | [适用于 IE 的访问面板扩展](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[适用于 Chrome 的访问面板扩展](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[适用于 Firefox 的访问面板扩展](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| 从库中配置应用程序 | [Azure Active Directory 中企业应用程序管理的新增功能：新增和改进的应用程序库](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| 配置密码 SSO | [在新的 Azure 门户中管理企业应用的单一登录：基于密码的登录](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| 将应用分配给在先决条件中标识的组 | [在 Azure Active Directory 中向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md) |
| 以具有访问权限的测试用户身份登录 https://myapps.microsoft.com/ |  |
| 单击刚刚创建的应用程序的磁贴。 | [访问面板是什么？：没有标识预配的基于密码的 SSO](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| 提供应用程序凭据 | [访问面板是什么？：没有标识预配的基于密码的 SSO](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| 关闭浏览器并再次输入登录名。 此时用户应该可以看到对应用程序的无缝访问权限。 |  |
| 或者可以查看应用程序使用情况报告。 请注意，存在一定程度的延迟，因此需要等待一段时间才能在报告中看到流量。 | [Azure Active Directory 门户中的登录活动报告：托管应用程序的使用情况](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory 报告保留策略](active-directory-reporting-retention.md) |

### <a name="considerations"></a>注意事项

如果库中没有目标应用程序，则可以使用“提供自己的应用”。 有关详细信息，请参阅 [Azure Active Directory 中企业应用程序管理的新增功能：从一个位置添加自定义应用程序](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 请注意以下几个要求：
   * 应用程序应具有已知的登录名 URL
   * 登录页应包含 HTML 窗体，并且浏览器扩展无法自动填充窗体中的一个文本字段。 至少应包含用户名和密码。

## <a name="saas-shared-accounts-configuration"></a>SaaS 共享帐户配置

估计完成时间：30 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 提前准备应用程序列表和确切的登录 URL。 可以使用 Twitter 作为示例。 | [Microsoft Azure Marketplace 上的 Twitter](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[注册 Twitter](https://twitter.com/signup?lang=en) |
| 此 SaaS 应用程序的共享凭据。 | [使用 Azure AD 共享帐户](active-directory-sharing-accounts.md)<br/>[适用于 Facebook、Twitter 和 LinkedIn 的 Azure AD 自动密码滚动更新现在提供预览版！ -“企业移动性 + 安全性”博客] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| 至少两位将访问同一帐户的团队成员的凭据。 两位成员必须都属于安全组。 | [在 Azure Active Directory 中向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md) |
| 对计算机的本地管理员访问权限，以便为 Internet Explorer、Chrome 或 Firefox 部署访问面板扩展 | [适用于 IE 的访问面板扩展](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[适用于 Chrome 的访问面板扩展](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[适用于 Firefox 的访问面板扩展](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 安装浏览器扩展 | [适用于 IE 的访问面板扩展](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[适用于 Chrome 的访问面板扩展](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[适用于 Firefox 的访问面板扩展](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| 从库中配置应用程序 | [Azure Active Directory 中企业应用程序管理的新增功能：新增和改进的应用程序库](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| 配置密码 SSO | [在新的 Azure 门户中管理企业应用的单一登录：基于密码的登录](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| 分配凭据时会应用分配给在先决条件中标识的组 | [在 Azure Active Directory 中向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md) |
| 使用其他的用户身份登录，该用户可以使用**相同共享帐户**访问应用。  |  |
| 或者可以查看应用程序使用情况报告。 请注意，存在一定程度的延迟，因此需要等待一段时间才能在报告中看到流量。 | [Azure Active Directory 门户中的登录活动报告：托管应用程序的使用情况](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory 报告保留策略](active-directory-reporting-retention.md) |


### <a name="considerations"></a>注意事项

如果库中没有目标应用程序，则可以使用“提供自己的应用”。 有关详细信息，请参阅 [Azure Active Directory 中企业应用程序管理的新增功能：从一个位置添加自定义应用程序](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 请注意以下几个要求：
   * 应用程序应具有已知的登录名 URL
   * 登录页应包含 HTML 窗体，并且浏览器扩展无法自动填充窗体中的一个文本字段。 至少应包含用户名和密码。

## <a name="app-proxy-configuration"></a>应用代理配置

估计完成时间：20 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| Microsoft Azure AD 基本或高级版订阅以及本人为全局管理员的 Azure AD 目录 | [Azure Active Directory 版本](active-directory-editions.md) |
| 想配置远程访问的本地托管 Web 应用程序 |  |
| 可以安装应用程序代理连接器，运行 Windows Server 2012 R2 或 Windows 8.1 或更高版本的服务器 | [了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md) |
| 如果路径中有防火墙，请确保防火墙已打开，以便连接器向应用程序代理发出 HTTPS (TCP) 请求 | [在 Azure 门户中启用应用程序代理：应用程序代理先决条件](active-directory-application-proxy-enable.md#application-proxy-prerequisites) |
| 如果组织使用代理服务器连接 Internet，请查看博客文章《使用现有本地代理服务器》了解配置方法的详细信息 | [使用现有的本地代理服务器](application-proxy-working-with-proxy-servers.md) |


### <a name="steps"></a>Steps

| 步骤 | 资源 |
| --- | --- |
| 在服务器上安装连接器 | [在 Azure 门户中启用应用程序代理：安装并注册连接器](active-directory-application-proxy-enable.md#install-and-register-a-connector) |
| 在 Azure AD 中将本地应用程序发布为“应用程序代理”应用程序 | [使用 Azure AD 应用程序代理发布应用程序](application-proxy-publish-azure-portal.md) |
| 分配测试用户 | [使用 Azure AD 应用程序代理（公共预览版）发布应用程序：添加测试用户](application-proxy-publish-azure-portal.md#add-a-test-user) |
| 或者也可以为用户配置单一登录体验 | [通过 Azure AD 应用程序代理提供单一登录](application-proxy-sso-azure-portal.md) |
| 使用已分配用户的身份登录 MyApps 门户，以便测试应用 | https://myapps.microsoft.com |

### <a name="considerations"></a>注意事项

1. 虽然我们建议将连接器放在公司网络中，但有时将其放在云中会有更好的性能。 有关详细信息：[使用 Azure Active Directory 应用程序代理时的网络拓扑注意事项](application-proxy-network-topology-considerations.md)
2. 如需深入了解安全性和此方式如何通过只维护出站连接来提供特定的安全远程访问解决方案，请参阅[使用 Azure AD 应用程序代理远程访问应用时的安全注意事项](application-proxy-security-considerations.md)

## <a name="generic-ldap-connector-configuration"></a>泛型 LDAP 连接器配置

估计完成时间：60 分钟

> [!IMPORTANT]
> 这是一项高级配置，需要对 FIM/MIM 有一定的了解。 如果在生产环境中使用，我们建议通过[顶级支持](https://support.microsoft.com/premier)解答有关此配置的问题。

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 已安装并配置 Azure AD Connect | 构建基块：[目录同步 - 密码哈希同步](#directory-synchronization--password-hash-sync-phs--new-installation) |
| 满足要求的 ADLDS 实例 | [泛型 LDAP 连接器技术参考：泛型 LDAP 连接器概述](./connect/active-directory-aadconnectsync-connector-genericldap.md#overview-of-the-generic-ldap-connector) |
| 用户使用的工作负荷列表，以及与这些工作负荷关联的属性列表 | [Azure AD Connect 同步：与 Azure Active Directory 同步的属性](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 添加泛型 LDAP 连接器 | [泛型 LDAP 连接器技术参考：创建新连接器](./connect/active-directory-aadconnectsync-connector-genericldap.md#create-a-new-connector) |
| 为已创建的连接器创建运行配置文件（完全导入、增量导入、完全同步、增量同步、导出） | [创建管理代理运行配置文件](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [将连接器与 Azure AD Connect Sync Service Manager 配合使用](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| 运行完全导入配置文件，并验证在连接器空间中是否存在对象 | [搜索连接器空间对象](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[将连接器与 Azure AD Connect Sync Service Manager 配合使用：搜索连接器空间](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| 创建同步规则，使 Metaverse 中的对象具有工作负荷的所需属性 | [Azure AD Connect 同步：有关更改默认配置的最佳实践：同步规则的更改](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Azure AD Connect 同步：了解声明性预配](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Azure AD Connect Sync：了解声明性预配表达式](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| 启动完全同步周期 | [Azure AD Connect 同步：计划程序：启动计划程序](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| 发生问题时执行故障排除 | [排查对象无法同步到 Azure AD 的问题](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| 确认 LDAP 用户可以登录并访问应用程序 | https://myapps.microsoft.com |

### <a name="considerations"></a>注意事项

> [!IMPORTANT]
> 这是一项高级配置，需要对 FIM/MIM 有一定的了解。 如果在生产环境中使用，我们建议通过[顶级支持](https://support.microsoft.com/premier)解答有关此配置的问题。

## <a name="groups---delegated-ownership"></a>组 - 委派的所有权

估计完成时间：10 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 已配置 SaaS 应用程序（联合 SSO 或密码 SSO） | 构建基块：[SaaS 联合 SSO 配置](#saas-federated-sso-configuration) |
| 已标识云组，并且已为该组分配对 #1 中的应用程序的访问权限 | 构建基块：[SaaS 联合 SSO 配置](#saas-federated-sso-configuration) <br/>[在 Azure Active Directory 中创建组并添加成员](active-directory-groups-create-azure-portal.md) |
| 组所有者的凭据可用 | [使用 Azure Active Directory 组管理对资源的访问权限](active-directory-manage-groups.md) |
| 已标识访问应用的信息辅助角色的凭据 | [访问面板是什么？](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 标识已被授权访问应用程序的组，并配置给定组的所有者| [在 Azure Active Directory 中管理组的设置](active-directory-groups-settings-azure-portal.md) |
| 以组所有者身份登录，在访问面板的组选项卡中查看组成员 | [Azure Active Directory 组管理页面](https://account.activedirectory.windowsazure.com/r/#/groups) |
| 添加要测试的信息辅助角色 |  |
| 以信息辅助角色身份登录，确认磁贴可用 | [访问面板是什么？](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>注意事项

如果应用程序已启用预配，可能需要稍等几分钟等待预配完成，之后才能以信息辅助角色身份访问应用程序。

## <a name="saas-and-identity-lifecycle"></a>SaaS 和标识生命周期

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 已配置 SaaS 应用程序（联合 SSO 或密码 SSO） | 构建基块：[SaaS 联合 SSO 配置](#saas-federated-sso-configuration) |
| 已标识云组，并且已为该组分配对 #1 中的应用程序的访问权限 | 构建基块：[SaaS 联合 SSO 配置](#saas-federated-sso-configuration) <br/>[在 Azure Active Directory 中创建组并添加成员](active-directory-groups-create-azure-portal.md) |
| 已标识访问应用的信息辅助角色的凭据 | [访问面板是什么？](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 从向其分配应用的组中删除用户 | [在 Azure Active Directory 租户中管理用户的组成员身份](active-directory-groups-members-azure-portal.md) |
| 请稍等几分钟等待取消预配 | [Azure AD 中的自动化 SaaS 应用用户预配：自动化预配的工作原理](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| 在单独的浏览器会话中，以信息辅助角色身份登录到“我的应用”门户，并确认磁贴缺失 | http://myapps.microsoft.com |


### <a name="considerations"></a>注意事项

推断离开者的 PoC 方案和/或离开方案。 如果用户在本地 AD 上被禁用或被删除，则无法登录到 SaaS 应用程序。

## <a name="self-service-access-to-application-management"></a>对应用程序管理进行自助访问

估计完成时间：10 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 标识将请求访问应用程序并且属于安全组的 POC 用户 | 构建基块：[SaaS 联合 SSO 配置](#saas-federated-sso-configuration) |
| 已部署目标应用程序 | 构建基块：[SaaS 联合 SSO 配置](#saas-federated-sso-configuration) |

### <a name="steps"></a>Steps

| 步骤 | 资源 |
| --- | --- |
| 在 Azure AD 管理门户中转到“企业应用程序”边栏选项卡 | [Azure AD 管理门户：企业应用程序](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| 通过自助服务方式根据先决条件配置应用程序 | [Azure Active Directory 中企业应用程序管理的新增功能：配置自助应用程序访问](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| 以信息辅助角色的身份登录到“我的应用”门户 | http://myapps.microsoft.com |
| 注意页面顶部的“+添加应用”按钮。 请使用该按钮来访问应用 |  |

### <a name="considerations"></a>注意事项

选择的应用程序可能有预配要求，所以立即打开应用可能会出错。 如果选择的应用程序支持使用 Azure AD 预配，并且已经配置完毕，则可以借此机会显示端到端的整个流程。 如需详细建议，请参阅 [SaaS 联合 SSO 配置](#saas-federated-sso-configuration)的构建基块

## <a name="self-service-password-reset"></a>自助密码重置

估计完成时间：15 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 在租户中启用自助密码管理。 | [Azure Active Directory 中针对 IT 管理员的密码重置](active-directory-passwords-update-your-own-password.md) |
| 启用密码写回，以便在本地管理密码。 请注意，此操作需要特定的 Azure AD Connect 版本 | [密码写回先决条件](active-directory-passwords-writeback.md) |
| 标识将使用此功能的 PoC 用户，并确保这些用户是安全组的成员。 这些用户不能是管理员，这样才能完全展示此功能 | [自定义：Azure AD 密码管理：限制对密码重置的访问](active-directory-passwords-writeback.md) |


### <a name="steps"></a>Steps

| 步骤 | 资源 |
| --- | --- |
| 导航到 Azure AD 管理门户：密码重置 | [Azure AD 管理门户：密码重置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| 确定密码重置策略。 可以使用电话呼叫和问题解答进行 POC。建议启用登录到访问面板时所需的注册 |  |
| 以辅助角色身份注销和登录 |  |
| 提供在步骤 2 中配置的自助密码重置数据 | https://aka.ms/ssprsetup |
| 关闭浏览器 |  |
| 以步骤 4 中使用的辅助角色身份启动登录过程 |  |
| 重置密码 | [更新自己的密码：重置我的密码](active-directory-passwords-update-your-own-password.md) |
| 尝试使用新密码登录到 Azure AD 和本地资源 |  |

### <a name="considerations"></a>注意事项

1. 如果升级 Azure AD Connect 会导致冲突，请考虑针对云帐户使用 Azure AD Connect，或者针对单独环境演示 Azure AD Connect
2. 管理员使用的策略各不相同，因此使用管理员帐户重置密码可能会破坏 PoC 并造成混乱。 请务必使用普通的用户帐户测试重置操作


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>通过电话呼叫进行 Azure 多重身份验证

估计完成时间：10 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 标识将使用 MFA 的 POC 用户  |  |
| 用于接收 MFA 质询的信号良好的电话  | [什么是 Azure 多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md) |

### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 在 Azure AD 管理门户中导航到“用户和组”边栏选项卡 | [Azure AD 管理门户：用户和组](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| 选择“所有用户”边栏选项卡 |  |
| 在顶栏中选择“多重身份验证”按钮 | Azure MFA 门户的的直接 URL：https://aka.ms/mfaportal |
| 在“用户”设置中选择 PoC 用户，并为其启用 MFA | [Azure 多重身份验证中的用户状态](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) |
| 以 PoC 用户身份登录，并完成验证过程  |  |

### <a name="considerations"></a>注意事项

1. 此构建基块中的 PoC 步骤为用户的所有登录名明确设置了 MFA。 还有条件访问和 Identity Protection 等其他工具可以将 MFA 用于目标更明确的方案。 从 POC 转移到生产时需要考虑这些问题。
2. 为方便起见，本构建基块中的 PoC 步骤明确使用电话呼叫作为 MFA 方法。 从 POC 转换为生产时，建议使用 [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) 等应用程序作为第二个考虑的因素（如果需要）。
有关详细信息，请参阅 [DRAFT NIST Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)（DRAFT NIST 特别发布 800-63B）

## <a name="mfa-conditional-access-for-saas-applications"></a>SaaS 应用程序的 MFA 条件访问

估计完成时间：10 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 标识 PoC 用户以确定策略。 这些用户应属于安全组，以便确定条件访问策略的范围 | [SaaS 联合 SSO 配置](#saas-federated-sso-configuration) |
| 已配置 SaaS 应用程序 |  |
| 已将 PoC 用户分配给应用程序 |  |
| 提供给 POC 用户的凭据可用 |  |
| POC 用户已注册 MFA。 使用的手机信号良好 | https://aka.ms/ssprsetup |
| 内部网络中的设备。 已配置内部地址范围中的 IP 地址 | 查找 IP 地址：https://www.bing.com/search?q=what%27s+my+ip |
| 外部网络中的设备（可以是使用运营商的移动网络的手机） |  |

### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 转到 Azure AD 管理门户：“条件访问”边栏选项卡 | [Azure AD 管理门户：条件访问](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| 创建条件访问策略：<br/>- 指向“用户和组”下的 PoC 用户<br/>- 指向“云应用”下的 PoC 应用程序<br/>- 指向所有位置，“条件”->“位置”下的受信任位置除外。**注意：**在 [MFA 门户](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)中配置受信任的 IP<br/>- 在“授权”下要求进行多重身份验证 | [Azure Active Directory 中的条件访问入门：策略配置步骤](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| 从公司网络内部访问应用程序 | [Azure Active Directory 中的条件访问入门：测试策略](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| 从公共网络访问应用程序 | [Azure Active Directory 中的条件访问入门：测试策略](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>注意事项

如果使用的是联合身份验证，则可以通过声明使用本地标识提供程序 (IdP) 在内部/外部公司网络状态之间通信。 在大型组织中评估和管理 IP 地址列表是一项非常复杂的工作，使用此方法可以不用管理 IP 地址列表。 在设置过程中，需用于“网络漫游”方案（用户从咖啡店等其他位置进行登录后，从内部网络进行登录）的帐户，并确保你了解其含义。 有关详细信息，请参阅[将 Azure 多重身份验证与 AD FS 配合使用来保护云资源：联合用户的受信任 IP](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

估计完成时间：15 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 标识属于PIM 的 POC 的全局管理员 | [开始使用 Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| 标识将成为安全管理员的全局管理员 | [开始使用 Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Azure Active Directory PIM 中的不同管理角色](active-directory-privileged-identity-management-roles.md) |
| 可选：确认全局管理员在 PIM 中是否有执行电子邮件通知的电子邮件访问权限 | [什么是 Azure AD Privileged Identity Management？：配置角色激活设置](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 以全局管理员 (GA) 身份登录 https://portal.azure.com 并启动“PIM”边栏选项卡。 执行此步骤的全局管理员会生成安全管理员。  我们将此参与者称为 GA1 | [使用 Azure AD Privileged Identity Management 中的安全向导](active-directory-privileged-identity-management-security-wizard.md) |
| 标识全局管理员，并将其从固定管理员移至符合条件的管理员。 为清楚起见，该管理员应该是除步骤 1 中使用的管理员之外的独立管理员。 我们将此参与者称为 GA2 | [Azure AD Privileged Identity Management：如何添加或删除用户角色](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[什么是 Azure AD Privileged Identity Management？：配置角色激活设置](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| 现在，以 GA2 身份登录 https://portal.azure.com 并尝试更改“用户设置”。 请注意，某些选项为灰显。 | |
| 现在，在新的选项卡中，在与步骤 3 相同的会话中，导航到 https://portal.azure.com 并将“PIM”边栏选项卡添加到仪表板。 | [如何在 Azure AD Privileged Identity Management 中激活或停用角色：添加 Privileged Identity Management 应用程序](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| 请求激活全局管理员角色 | [如何在 Azure AD Privileged Identity Management 中激活或停用角色：激活角色](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| 请注意，如果 GA2 之前从未注册过 MFA，则需要注册 Azure MFA |  |
| 返回步骤 3 中的原始选项卡，并单击浏览器中的刷新按钮。 请注意，现在已经有权更改“用户设置”了 | |
| 或者，如果全局管理员启用了电子邮件，也可以通过勾选 GA1 和 GA2 的收件箱来查看角色被激活的通知 |  |
| 8 查看审核历史记录并观察报告，确认显示了 GA2 的权限提升。 | [什么是 Azure AD Privileged Identity Management？：查看角色活动](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>注意事项

此功能属于 Azure AD Premium P2 和/或 EMS E5

## <a name="discovering-risk-events"></a>发现风险事件

估计完成时间：20 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 下载并安装了 Tor 浏览器设备 | [下载 Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| 对 POC 用户授予访问权限，以便进行登录 | [Azure Active Directory Identity Protection 演练手册](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 打开 tor 浏览器 | [下载 Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| 使用 POC 用户帐户登录 https://myapps.microsoft.com | [Azure Active Directory Identity Protection 操作手册：模拟风险事件](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| 等待 5 至 7 分钟 |  |
| 以全局管理员身份登录 https://portal.azure.com 并打开“Identity Protection”边栏选项卡 | https://aka.ms/aadipgetstarted |
| 打开“风险事件”边栏选项卡。 应会在“从匿名 IP 地址登录”下看到一个条目  | [Azure Active Directory Identity Protection 操作手册：模拟风险事件](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>注意事项

此功能属于 Azure AD Premium P2 和/或 EMS E5

## <a name="deploying-sign-in-risk-policies"></a>部署登录风险策略

估计完成时间：10 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 下载并安装了 Tor 浏览器设备 | [下载 Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| 以 POC 用户身份访问，以便进行登录测试 |  |
| 已注册 MFA 的 POC 用户。 请务必使用信号良好的手机 | 构建基块：[通过电话呼叫进行 Azure 多重身份验证](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Steps

| 步骤 | 资源 |
| --- | --- |
| 以全局管理员身份登录 https://portal.azure.com 并打开“Identity Protection”边栏选项卡 | https://aka.ms/aadipgetstarted |
| 启用登录风险策略，如下所示：<br/>- 分配到：POC 用户<br/>- 条件：登录风险中或高（从匿名位置进行登录被认为是中等风险级别）<br/>- 控件：需要 MFA | [Azure Active Directory Identity Protection 操作手册：登录风险](active-directory-identityprotection-playbook.md) |
| 打开 tor 浏览器 | [下载 Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| 使用 PoC 用户帐户登录 https://myapps.microsoft.com |  |
| 请注意 MFA 质询 | [Azure AD Identity Protection 中的登录体验：有风险的登录恢复](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>注意事项

此功能属于 Azure AD Premium P2 和/或 EMS E5。 有关风险事件的详细信息，请访问：[Azure Active Directory 风险事件](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>配置基于证书的身份验证

估计完成时间：20 分钟

### <a name="pre-requisites"></a>先决条件

| 先决条件 | 资源 |
| --- | --- |
| 已从企业 PKI 预配了用户证书的设备（Windows、iOS 或 Android） | [部署用户证书](https://msdn.microsoft.com/library/cc770857.aspx) |
| 与 ADFS 联合的 Azure AD 域 | [Azure AD Connect 和联合身份验证](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Active Directory 证书服务概述](https://technet.microsoft.com/library/hh831740.aspx)|
| iOS 设备需要安装 Microsoft Authenticator 应用 | [Microsoft Authenticator 应用入门](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>步骤

| 步骤 | 资源 |
| --- | --- |
| 在 ADFS 上启用“证书身份验证” | [配置身份验证策略：在 Windows Server 2012 R2 中全局配置主身份验证](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| 可选：在适用于 Exchange Active Sync 客户端的 Azure AD 中启用证书身份验证 | [Azure Active Directory 中基于证书的身份验证入门](active-directory-certificate-based-authentication-get-started.md) |
| 导航到“访问面板”并使用“用户证书”进行身份验证 | https://myapps.microsoft.com |

### <a name="considerations"></a>注意事项

如需深入了解有关此部署的注意事项，请访问：[ADFS: Certificate Authentication with Azure AD & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)（ADFS：使用 Azure AD 和 Office 365 进行证书身份验证）



> [!NOTE]
> 应保证拥有用户证书。 可以通过管理设备或使用 PIN（在智能卡的情况下）。



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
