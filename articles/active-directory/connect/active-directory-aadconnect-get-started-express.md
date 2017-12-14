---
title: "通过快速设置开始使用 Azure AD Connect | Microsoft 文档"
description: "了解如何下载、安装和运行 Azure AD Connect 的设置向导。"
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 638e654cb9e7a2454b37beb6067cb5a2fb9a57b5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>通过快速设置开始使用 Azure AD Connect
有单林拓扑和用于身份验证的 **密码同步** 时，便可使用 Azure AD Connect [快速设置](active-directory-aadconnectsync-implement-password-synchronization.md)。 **快速设置** 是默认选项，用于最常见的部署方案。 只需按几下鼠标即可将本地目录扩展到云中。

开始安装 Azure AD Connect 之前，确保[下载 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)，完成 [Azure AD Connect：硬件和先决条件](active-directory-aadconnect-prerequisites.md)中的预备步骤。

如果快速设置与拓扑不匹配，请参阅 [相关文档](#related-documentation) 中的其他方案。

## <a name="express-installation-of-azure-ad-connect"></a>Azure AD Connect 的快速安装
可以在 [视频](#videos) 一节观看这些步骤的操作示范。

1. 以本地管理员身份登录到要安装 Azure AD Connect 的服务器。 应该在要用作同步服务器的服务器上执行此操作。
2. 导航到 **AzureADConnect.msi**并双击它。
3. 在“欢迎”屏幕上，选中对应的框，同意许可条款，并单击“继续”。  
4. 在“快速设置”屏幕上，单击“使用快速设置”。  
   ![欢迎使用 Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. 在“连接到 Azure AD”屏幕上，输入 Azure AD 的全局管理员用户名和密码。 单击“下一步”。  
   ![连接到 Azure AD](./media/active-directory-aadconnect-get-started-express/connectaad.png)如果收到错误消息并出现连接问题，请参阅[排查连接问题](active-directory-aadconnect-troubleshoot-connectivity.md)。
6. 在“连接到 AD DS”屏幕上，输入企业管理员帐户的用户名和密码。 可以采用 NetBios 或 FQDN 格式输入域部分，即 FABRIKAM\administrator 或 fabrikam.com\administrator。 单击“下一步”。  
   ![连接到 AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. 只有在未完成[先决条件](active-directory-aadconnect-prerequisites.md)中的[验证域](../active-directory-domains-add-azure-portal.md)步骤时，才会显示 [**Azure AD 登录配置**](active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration)页。
   ![未验证的域](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
   如果看到此页，请查看标记为“未添加”和“未验证”的每个域。 确保使用的域都已在 Azure AD 中验证。 验证域后，请单击“刷新”符号。
8. 在“准备好配置”屏幕上，单击“安装”。
   * 在“已准备好配置”页上，可以取消选中“配置完成后立即开始同步过程”  复选框。 如果想要进行其他配置（例如[筛选](active-directory-aadconnectsync-configure-filtering.md)），应取消选中此复选框。 如果取消选择此选项，向导将配置同步，但会保持禁用计划程序。 在[重新运行安装向导](active-directory-aadconnectsync-installation-wizard.md)手动启用计划程序之前，计划程序不会运行。
   * 如果本地 Active Directory 中有 Exchange，也可以选择启用 [**Exchange 混合部署**](https://technet.microsoft.com/library/jj200581.aspx)。 如果打算同时在云中和本地设置 Exchange 邮箱，请启用此选项。
     ![已准备好配置 Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. 安装完成后，单击“退出” 。
10. 安装完成后，请注销并再次登录，即可使用同步服务管理器或同步规则编辑器。

## <a name="videos"></a>视频
有关使用快速安装的视频，请参阅：

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
> 
> 

## <a name="next-steps"></a>后续步骤
安装 Azure AD Connect 后，可以[验证安装并分配许可证](active-directory-aadconnect-whats-next.md)。

若要了解有关这些功能（在安装过程中已启用）的详细信息，请参阅[自动升级](active-directory-aadconnect-feature-automatic-upgrade.md)、[防止意外删除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)和 [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md)。

若要了解有关这些常见主题的详细信息，请参阅[计划程序以及如何触发同步](active-directory-aadconnectsync-feature-scheduler.md)。

了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。

## <a name="related-documentation"></a>相关文档
| 主题 |
| --- | --- |
| Azure AD Connect 概述 |
| 使用自定义设置安装 |
| 从 DirSync 升级 |
| 用于安装的帐户 |

