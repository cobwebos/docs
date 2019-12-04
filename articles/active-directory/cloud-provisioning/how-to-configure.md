---
title: Azure AD Connect 云预配新代理配置
description: 本主题介绍如何安装云设置。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794297"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect 云预配新配置

安装代理后，需要登录到 Azure 门户并配置设置。  使用以下步骤启用代理。

## <a name="configure-provisioning"></a>配置设置
若要配置预配，请执行以下步骤：

1.  在 Azure AD 门户中，单击 " **Azure Active Directory**
2.  单击**Azure AD Connect**
3.  选择 "**管理预配（预览版）** "
![](media/how-to-configure/manage1.png)

4.  单击 "**新建配置**"。
5.  在 "配置" 屏幕上，将预填充本地域
6. 输入**通知电子邮件**。 将通知此电子邮件 
7. 如果预配不正常。  
8. 将选择器移动到 "**启用**"，然后单击 "**保存**"。
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>向特定用户和组设置作用域
如果要将代理的作用域限定为仅同步特定的用户和组，则可以执行此操作。 你可以使用本地 AD 组或组织单位来确定范围。 不能在配置中配置组和组织单位。 

1.  在 Azure AD 门户中，单击 " **Azure Active Directory**
2.  单击**Azure AD Connect**
3.  选择 "**管理预配（预览版）** "
4.  在 "**配置**" 下单击配置。  
![](media/how-to-configure/scope1.png)

5.  在 "**配置**" 下，选择 "**所有用户**" 以更改配置规则的作用域。
![](media/how-to-configure/scope2.png)

6. 在右侧，可以通过输入组的可分辨名称并单击 "**添加**"，将作用域更改为仅包含安全组。
![](media/how-to-configure/scope3.png)

7. 或将其更改为仅包括特定 Ou。 单击 "**完成**" 并**保存**。
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>重新启动预配 
如果不想等待下一个计划的运行，可以使用 "重新启动设置" 按钮触发设置运行。 
1.  在 Azure AD 门户中，单击 " **Azure Active Directory**
2.  单击**Azure AD Connect**
3.  选择 "**管理预配（预览版）** "
4.  在 "**配置**" 下单击配置。  
![](media/how-to-configure/scope1.png)

5.  在顶部单击 "**重新启动设置**"。

## <a name="removing-a-configuration"></a>删除配置
如果要删除配置，可以使用以下步骤来执行此操作。

1.  在 Azure AD 门户中，单击 " **Azure Active Directory**
2.  单击**Azure AD Connect**
3.  选择 "**管理预配（预览版）** "
4.  在 "**配置**" 下单击配置。  
![](media/how-to-configure/scope1.png)

5.  在顶部，单击 "**删除**"。
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>在删除配置之前没有确认，因此请确保在单击 "**删除**" 之前要执行的操作。


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
