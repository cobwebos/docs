---
title: Azure AD 连接云预配新代理配置
description: 本文介绍如何安装云资源调配。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620969"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>为 Azure AD 连接基于云的预配创建新配置

安装代理后，需要登录到 Azure 门户并配置 Azure 活动目录 （Azure AD） 连接云预配。 按照以下步骤启用代理。

## <a name="configure-provisioning"></a>配置预配
要配置预配，请按照以下步骤操作。

1.  在 Azure 门户中，选择**Azure 活动目录**。
1.  选择“Azure AD Connect”****。
1.  选择 **"管理预配（预览）"。**

    ![管理预配（预览）](media/how-to-configure/manage1.png)

1.  选择 **"新配置**"。
1.  在配置屏幕上，本地域已预填充。
1.  输入**通知电子邮件**。 当预配不正常时，将通知此电子邮件。
1.  将选择器移到**启用**，然后选择 **"保存**"。

    ![Azure AD 预配（预览）](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>特定用户和组的范围预配
您可以使用本地活动目录组或组织单位对代理进行限定以同步特定用户和组。 不能在配置中配置组和组织单位。 

1.  在 Azure 门户中，选择**Azure 活动目录**。
1.  选择“Azure AD Connect”****。
1.  选择 **"管理预配（预览）"。**
1.  在 **"配置"** 下，选择您的配置。

    ![“配置”部分](media/how-to-configure/scope1.png)

1.  在“配置”下选择“所有用户”，以更改配置规则的范围。********

    ![所有用户选项](media/how-to-configure/scope2.png)

1. 在右侧，您可以更改范围以仅包括安全组。 输入组的可分辨名称，然后选择 **"添加**"。

    ![选定的安全组选项](media/how-to-configure/scope3.png)

1.  或者，您可以更改范围以仅包括特定的组织单位。 选择 **"完成"** 并**保存**。  
2.  更改范围后，应[重新启动预配](#restart-provisioning)以启动更改的立即同步。

    ![选定的组织单位选项](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>重新启动预配 
如果不想等待下一次计划运行，请使用 **"重新启动预配**"按钮触发预配运行。 
1.  在 Azure 门户中，选择**Azure 活动目录**。
1.  选择“Azure AD Connect”****。
1.  选择 **"管理预配（预览）"。**
1.  在 **"配置"** 下，选择您的配置。

    ![用于重新启动预配的配置选择](media/how-to-configure/scope1.png)

1.  在顶部，选择 **"重新启动预配**"。

## <a name="remove-a-configuration"></a>删除配置
要删除配置，请按照以下步骤操作。

1.  在 Azure 门户中，选择**Azure 活动目录**。
1.  选择“Azure AD Connect”****。
1.  选择 **"管理预配（预览）"。**
1.  在 **"配置"** 下，选择您的配置。

    ![要删除配置的配置选择](media/how-to-configure/scope1.png)

1.  在配置屏幕的顶部，选择 **"删除**"。

    ![“删除”按钮](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>删除配置之前没有确认。 在选择 **"删除**"之前，请确保这是要执行的操作。


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
