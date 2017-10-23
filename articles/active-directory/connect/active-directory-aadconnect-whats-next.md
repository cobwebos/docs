---
title: "Azure AD Connect：后续步骤以及如何管理 Azure AD Connect | Microsoft Docs"
description: "了解如何扩展 Azure AD Connect 的默认配置和操作任务。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: beace24fa00c85a5038a3c39ae8f76af5fd12111
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>后续步骤以及如何管理 Azure AD Connect
使用本文中介绍的操作流程，根据组织的需要和要求自定义 Azure Active Directory (Azure AD) Connect。  

## <a name="add-additional-sync-admins"></a>添加更多的同步管理员
默认情况下，只有执行安装的用户和本地管理员才可以管理安装的同步引擎。 要使其他用户能够访问和管理同步引擎，请在本地服务器上找到名为 ADSyncAdmins 的组，并将这些用户添加到此组中。

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>向 Azure AD Premium 和企业移动性套件用户分配许可证
将用户同步到云后，需要向他们分配许可证，以便其可以继续使用 Office 365 等云应用。

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>分配 Azure AD Premium 或企业移动套件许可证

1. 以管理员身份登录 Azure 门户。
2. 在左侧选择“Active Directory”。
3. 在“Active Directory”页上，双击要设置的用户所在的目录。
4. 在“目录”页的顶部，选择“许可证”。
5. 在“许可证”页上，选择“Active Directory Premium”或“企业移动套件”，并单击“分配”。
6. 在对话框中，选择要向其分配许可证的用户，并单击复选标记图标以保存更改。

## <a name="verify-the-scheduled-synchronization-task"></a>验证计划的同步任务
使用 Azure 门户检查同步状态。

### <a name="to-verify-the-scheduled-synchronization-task"></a>验证计划的同步任务
1. 以管理员身份登录 Azure 门户。
2. 在左侧选择“Active Directory”。
3. 在“Active Directory”页上，双击要设置的用户所在的目录。
4. 在“目录”页的顶部，选择“目录集成”。
5. 在“与本地 Active Directory 集成”的下方，注意上次同步时间。

<center>![目录同步时间](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>启动计划的同步任务
如果需要运行同步任务，可以通过再次运行 Azure AD Connect 向导来执行此操作。  需要提供 Azure AD 凭据。  在向导中，选择“自定义同步选项”任务，并一直单击“下一步”完成向导。 最后，请确保已选中“初始配置完成后立即开始同步过程”框。

<center>![开始同步](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

有关 Azure AD Connect 同步计划程序的详细信息，请参阅 [Azure AD Connect 计划程序](active-directory-aadconnectsync-feature-scheduler.md)。

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Azure AD Connect 中提供的其他任务
在完成 Azure AD Connect 的初始安装后，随时可以从 Azure AD Connect 启动页或桌面快捷方式再次启动向导。  会发现，再次运行向导会以其他任务的形式提供一些新选项。  

下表提供了这些任务的摘要以及各个任务的简要描述。

![其他任务列表](./media/active-directory-aadconnect-whats-next/addtasks.png)

| 其他任务 | 说明 |
| --- | --- |
| **查看所选方案** |查看当前的 Azure AD Connect 解决方案。  包括常规设置、同步的目录和同步设置等。 |
| **自定义同步选项** |更改当前配置，包括在配置中添加其他 Active Directory 林，或启用同步选项（如用户、组、设备或密码回写）。 |
| **启用暂存模式** |对不会立即同步且不会导出到 Azure AD 或本地 Active Directory 的信息进行同步。  使用此功能可在同步前进行预览。 |

## <a name="next-steps"></a>后续步骤
了解有关[将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。
