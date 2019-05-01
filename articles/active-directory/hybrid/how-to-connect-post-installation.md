---
title: Azure AD Connect：后续步骤以及如何管理 Azure AD Connect | Microsoft Docs
description: 了解如何扩展 Azure AD Connect 的默认配置和操作任务。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571316"
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
3. 在左侧，选择**Azure AD Connect**
4. 在页面顶部，记下最后一次同步。

![目录同步时间](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>启动计划的同步任务
如果需要运行同步任务，可以执行此操作：

1. 双击 Azure AD Connect 桌面快捷方式以启动向导。
2. 单击 **“配置”**。
3. 在任务屏幕中，选择**自定义同步选项**单击**下一步**
4. 输入 Azure AD 凭据
5. 单击“下一步”。 单击“下一步” 。  单击“下一步”。
5.  上**已准备好配置**屏幕上，确保**配置完成后启动同步过程**框处于选中状态。
6.  单击 **“配置”**。

有关 Azure AD Connect 同步计划程序的详细信息，请参阅 [Azure AD Connect 计划程序](how-to-connect-sync-feature-scheduler.md)。

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Azure AD Connect 中提供的其他任务
在完成 Azure AD Connect 的初始安装后，随时可以从 Azure AD Connect 启动页或桌面快捷方式再次启动向导。  在再次完成向导的过程中，会发现，它会以“其他任务”的形式提供一些新选项。  

下表提供了这些任务的摘要以及各个任务的简要描述。

![其他任务列表](./media/how-to-connect-post-installation/addtasks2.png)

| 其他任务 | 描述 |
| --- | --- |
|**隐私设置**|查看正在与 Microsoft 共享的遥测数据。|
|**查看当前配置**|查看当前的 Azure AD Connect 解决方案。  包括常规设置、同步的目录和同步设置等。 |
| **自定义同步选项** |更改当前配置，包括在配置中添加其他 Active Directory 林，或启用同步选项（如用户、组、设备或密码回写）。 |
|**配置设备选项**|可用于同步的设备选项|
|**刷新目录架构**|允许您添加新的本地目录对象进行同步|
|**配置暂存模式** |对不会立即同步且不会导出到 Azure AD 或本地 Active Directory 的信息进行同步。  使用此功能可在同步前进行预览。 |
|**更改用户登录**|更改用户用来登录的身份验证方法|
|**管理联合身份验证**|管理 AD FS 基础结构，续订证书，并添加 AD FS 服务器|
|**故障排除**|帮助进行故障排除 Azure AD Connect 问题|

## <a name="next-steps"></a>后续步骤
了解有关[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
