---
title: Azure AD Connect 云预配新代理配置
description: 本文介绍如何安装云设置。
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
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620969"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>为 Azure AD Connect 基于云的设置创建新配置

安装代理后，你需要登录到 Azure 门户并配置 Azure Active Directory （Azure AD）连接云设置。 按照以下步骤启用代理。

## <a name="configure-provisioning"></a>配置设置
若要配置预配，请执行以下步骤。

1.  在 Azure 门户中，选择“Azure Active Directory”。
1.  选择“Azure AD Connect”。
1.  选择 "**管理预配（预览版）** "。

    ![管理预配（预览版）](media/how-to-configure/manage1.png)

1.  选择 "**新配置**"。
1.  在 "配置" 屏幕上，预填充了本地域。
1.  输入**通知电子邮件**。 设置不正常时，此电子邮件将收到通知。
1.  将选择器移动到 "**启用**"，然后选择 "**保存**"。

    ![Azure AD 预配（预览版）](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>向特定用户和组设置作用域
可以通过使用本地 Active Directory 组或组织单位来确定代理的范围，以便同步特定的用户和组。 不能在配置中配置组和组织单位。 

1.  在 Azure 门户中，选择“Azure Active Directory”。
1.  选择“Azure AD Connect”。
1.  选择 "**管理预配（预览版）** "。
1.  在 "**配置**" 下，选择您的配置。

    ![“配置”部分](media/how-to-configure/scope1.png)

1.  在“配置”下选择“所有用户”，以更改配置规则的范围。

    ![所有用户选项](media/how-to-configure/scope2.png)

1. 在右侧，可以将作用域更改为仅包括安全组。 输入组的可分辨名称，然后选择 "**添加**"。

    ![选择的安全组选项](media/how-to-configure/scope3.png)

1.  或者，你可以更改范围以仅包括特定的组织单位。 选择 "**完成**并**保存**"。  
2.  更改作用域后，应[重新启动预配](#restart-provisioning)，以便立即同步更改。

    ![所选组织单位选项](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>重新启动预配 
如果不想等待下一次计划的运行，请使用 "**重新启动设置**" 按钮触发设置运行。 
1.  在 Azure 门户中，选择“Azure Active Directory”。
1.  选择“Azure AD Connect”。
1.  选择 "**管理预配（预览版）** "。
1.  在 "**配置**" 下，选择您的配置。

    ![用于重新启动预配的配置选择](media/how-to-configure/scope1.png)

1.  在顶部，选择 "**重新启动设置**"。

## <a name="remove-a-configuration"></a>删除配置
若要删除配置，请执行以下步骤。

1.  在 Azure 门户中，选择“Azure Active Directory”。
1.  选择“Azure AD Connect”。
1.  选择 "**管理预配（预览版）** "。
1.  在 "**配置**" 下，选择您的配置。

    ![要删除配置的配置选择](media/how-to-configure/scope1.png)

1.  在配置屏幕的顶部，选择 "**删除**"。

    ![“删除”按钮](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>在删除配置之前，没有确认。 在选择 "**删除**" 之前，请确保这是要执行的操作。


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
