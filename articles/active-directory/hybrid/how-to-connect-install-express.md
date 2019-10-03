---
title: Azure AD Connect：快速设置入门 | Microsoft Docs
description: 了解如何下载、安装和运行 Azure AD Connect 的设置向导。
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24143f8c94a294da90be84bacfe633db0cd24f85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60244520"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>通过快速设置开始使用 Azure AD Connect
采用单林拓扑并将**密码哈希同步**用于身份验证时，可以使用 Azure AD Connect [快速设置](how-to-connect-password-hash-synchronization.md)。 **快速设置** 是默认选项，用于最常见的部署方案。 只需按几下鼠标即可将本地目录扩展到云中。

开始安装 Azure AD Connect 之前，确保[下载 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)，完成 [Azure AD Connect：硬件和先决条件](how-to-connect-install-prerequisites.md)中的预备步骤。

如果快速设置与拓扑不匹配，请参阅 [相关文档](#related-documentation) 中的其他方案。

## <a name="express-installation-of-azure-ad-connect"></a>Azure AD Connect 的快速安装
可以在 [视频](#videos) 一节观看这些步骤的操作示范。

1. 以本地管理员身份登录到要安装 Azure AD Connect 的服务器。 应该在要用作同步服务器的服务器上执行此操作。
2. 导航到 **AzureADConnect.msi**并双击它。
3. 在“欢迎”屏幕上，选中对应的框，同意许可条款，并单击“继续”。   
4. 在“快速设置”屏幕上，单击“使用快速设置”。   
   ![欢迎使用 Azure AD Connect](./media/how-to-connect-install-express/express.png)
5. 在“连接到 Azure AD”屏幕上，输入 Azure AD 的全局管理员用户名和密码。 单击“下一步”。   
   ![连接到 Azure AD](./media/how-to-connect-install-express/connectaad.png)  
   如果收到错误消息并且出现连接问题，请参阅[排查连接问题](tshoot-connect-connectivity.md)。
6. 在“连接到 AD DS”屏幕上，输入企业管理员帐户的用户名和密码。 可以采用 NetBios 或 FQDN 格式输入域部分，即 FABRIKAM\administrator 或 fabrikam.com\administrator。 单击“下一步”。   
   ![连接到 AD DS](./media/how-to-connect-install-express/connectad.png)
7. 只有在未完成[先决条件](how-to-connect-install-prerequisites.md)中的[验证域](../active-directory-domains-add-azure-portal.md)步骤时，才会显示 [**Azure AD 登录配置**](plan-connect-user-signin.md#azure-ad-sign-in-configuration)页。
   ![未验证的域](./media/how-to-connect-install-express/unverifieddomain.png)  
   如果看到此页，请查看标记为“未添加”和“未验证”的每个域。   确保使用的域都已在 Azure AD 中验证。 验证域后，请单击“刷新”符号。
8. 在“准备好配置”屏幕上，单击“安装”。 
   * 在“已准备好配置”页上，可以取消选中“配置完成后立即开始同步过程”  复选框。 如果想要进行其他配置（例如[筛选](how-to-connect-sync-configure-filtering.md)），应取消选中此复选框。 如果取消选择此选项，向导将配置同步，但会保持禁用计划程序。 在[重新运行安装向导](how-to-connect-installation-wizard.md)手动启用计划程序之前，计划程序不会运行。
   * 保留启用“配置完成后立即开始同步过程”复选框会立即触发将所有用户、组和联系人完全同步到 Azure AD 的过程。 
   * 如果本地 Active Directory 中有 Exchange，也可以选择启用 [**Exchange 混合部署**](https://technet.microsoft.com/library/jj200581.aspx)。 如果打算同时在云中和本地设置 Exchange 邮箱，请启用此选项。
     ![已准备好配置 Azure AD Connect](./media/how-to-connect-install-express/readytoconfigure.png)
9. 安装完成后，单击“退出”  。
10. 安装完成后，请注销并再次登录，即可使用同步服务管理器或同步规则编辑器。

## <a name="videos"></a>视频
有关使用快速安装的视频，请参阅：

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>后续步骤
安装 Azure AD Connect 后，可以[验证安装并分配许可证](how-to-connect-post-installation.md)。

若要了解有关这些功能（在安装过程中已启用）的详细信息，请参阅：[自动升级](how-to-connect-install-automatic-upgrade.md)、[防止意外删除](how-to-connect-sync-feature-prevent-accidental-deletes.md)和 [Azure AD Connect Health](how-to-connect-health-sync.md)。

若要了解有关这些常见主题的详细信息，请参阅[计划程序以及如何触发同步](how-to-connect-sync-feature-scheduler.md)。

了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。

## <a name="related-documentation"></a>相关文档

| 主题 | 链接 |
| --- | --- |
| Azure AD Connect 概述 | [将本地目录与 Azure Active Directory 进行集成](whatis-hybrid-identity.md)
| 使用自定义设置安装 | [Azure AD Connect 的自定义安装](how-to-connect-install-custom.md) |
| 从 DirSync 升级 | [从 Azure AD 同步工具 (DirSync) 升级](how-to-dirsync-upgrade-get-started.md)|
| 用于安装的帐户 | [有关 Azure AD Connect 凭据和权限的详细信息](reference-connect-accounts-permissions.md) |
