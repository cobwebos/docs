---
title: Azure AD Connect：排查对象同步错误 | Microsoft Docs
description: 本主题按步骤介绍了如何使用故障排除任务来排查对象同步问题。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f65e84bff63bbdb781991ff6648b0fb98ca5208f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592307"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>使用 Azure AD Connect 同步排查对象同步问题
本文按步骤介绍了如何使用故障排除任务来排查对象同步问题。 若要了解如何在 Azure Active Directory (Azure AD) Connect 中对工作进行故障排除，请观看[此简短视频](https://aka.ms/AADCTSVideo)。

## <a name="troubleshooting-task"></a>故障排除任务
对于 1.1.749.0 或更高版本的 Azure AD Connect 部署，请使用向导中的故障排除任务来排查对象同步问题。 对于早期版本，请手动进行故障排除，如[此文](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)所述。

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>在向导中运行故障排除任务
若要在向导中运行故障排除任务，请执行以下步骤：

1.  使用“以管理员身份运行”选项，在 Azure AD Connect 服务器上打开一个新的 Windows PowerShell 会话。
2.  运行 `Set-ExecutionPolicy RemoteSigned` 或 `Set-ExecutionPolicy Unrestricted`。
3.  启动 Azure AD Connect 向导。
4.  导航到“其他任务”页面，选择“故障排除”，然后单击“下一步”。
5.  在“故障排除”页上，单击“启动”以在 PowerShell 中启动故障排除菜单。
6.  在主菜单中，选择“排查对象同步问题”。
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>排查输入参数问题
以下输入参数是故障排除任务所需的：
1.  **对象可分辨名称** – 这是需要进行故障排除的对象的可分辨名称
2.  **AD 连接器名称** – 这是上述对象所驻留的 AD 林的名称。
3.  Azure AD 租户全局管理员凭据 ![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>了解故障排除任务的结果
此故障排除任务执行以下检查：

1.  在对象已同步到 Azure Active Directory 的情况下检测 UPN 不匹配的情况
2.  检查对象是否已因域筛选而被筛选出来
3.  检查对象是否已因 OU 筛选而被筛选出来
4.  检查对象同步是否由于链接的邮箱而被阻止
5. 检查对象是否是不应该同步的动态通讯组

本部分的剩余内容说明了此任务返回的具体结果。 在每个示例中，此任务都提供了分析以及解决问题所需的建议操作。

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>在对象已同步到 Azure Active Directory 的情况下检测 UPN 不匹配的情况
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>使用 Azure AD 租户时，不验证 UPN 后缀
如果没有通过 Azure AD 租户对 UserPrincipalName (UPN)/备用登录 ID 后缀进行验证，Azure Active Directory 会将 UPN 后缀替换为默认的域名“onmicrosoft.com”。

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>将 UPN 后缀从一个联合域更改到另一个联合域
Azure Active Directory 不允许将 UserPrincipalName (UPN)/备用登录 ID 后缀的更改从一个联合域同步到另一个联合域。 这适用于通过 Azure AD 租户进行验证且“身份验证类型”为“联合身份验证”的域。

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD 租户 DirSync 功能“SynchronizeUpnForManagedUsers”已禁用
对于使用托管身份验证的许可用户帐户，禁用 Azure AD 租户 DirSync 功能“SynchronizeUpnForManagedUsers”后，Azure Active Directory 不允许将更新同步到 UserPrincipalName/备用登录 ID。

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>对象已因域筛选而被筛选出来
### <a name="domain-is-not-configured-to-sync"></a>未将域配置为同步
由于未配置域，对象未在范围内。 在下面的示例中，对象不在范围内，因为其所属的域已从同步中筛选出来。

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>域已配置为同步，但缺少运行配置文件/运行步骤
对象不在范围内，因为域缺少运行配置文件/运行步骤。 在下面的示例中，对象不在范围内，因为其所属的域缺少“完全导入”运行配置文件的运行步骤。
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>对象已因 OU 筛选而被筛选出来
对象因 OU 筛选配置而不在同步范围内。 在下面的示例中，对象属于 OU=NoSync,DC=bvtadwbackdc,DC=com。  此 OU 不包括在同步范围内。</br>

![OU](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch7.png)

## <a name="linked-mailbox-issue"></a>链接邮箱问题
链接邮箱假设与位于另一个受信任帐户林中的外部主帐户相关联。 如果没有此类外部主帐户，则 Azure AD Connect 不会将 Exchange 林中对应于链接邮箱的用户帐户与 Azure AD 租户同步。</br>
![链接邮箱](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>动态通讯组问题
由于本地 Active Directory 和 Azure Active Directory 之间的各种差异，Azure AD Connect 不会将动态通讯组与 Azure AD 租户同步。

![动态通讯组](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch13.png)

## <a name="html-report"></a>HTML 报表
除了分析对象，故障排除任务还会生成 HTML 报表，其中包含有关该对象的一切已知内容。 此 HTML 报表可以与支持团队共享，以便根据需要进行进一步的故障排除。

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch8.png)

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。
