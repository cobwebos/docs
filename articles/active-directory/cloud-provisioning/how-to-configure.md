---
title: Azure AD Connect 云预配新代理配置
description: 本文介绍如何安装云设置。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628873"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>为 Azure AD Connect 基于云的设置创建新配置

安装代理后，需要登录到 Azure 门户并配置 Azure Active Directory (Azure AD) 连接云设置。 按照以下步骤启用代理。

## <a name="configure-provisioning"></a>配置设置
若要配置预配，请执行以下步骤。

 1. 在 Azure 门户中，选择 " **Azure Active Directory**
 2. 选择“Azure AD Connect”****。
 3. 选择 " **管理设置**"。

 ![管理预配](media/how-to-configure/manage1.png)
 
 4. 选择 " **新配置**"。
 5. 在 "配置" 屏幕上，选择你的域以及是否启用密码哈希同步。 单击 " **创建**"。  
 
 ![创建新配置](media/how-to-configure/configure1.png)


 6.  将打开 "编辑预配配置" 屏幕。

   ![编辑配置](media/how-to-configure/configure2.png)

 7. 输入 **通知电子邮件**。 设置不正常时，此电子邮件将收到通知。
 8. 将选择器移动到 "启用"，然后选择 "保存"。

## <a name="scope-provisioning-to-specific-users-and-groups"></a>向特定用户和组设置作用域
可以通过使用本地 Active Directory 组或组织单位来确定代理的范围，以便同步特定的用户和组。 不能在配置中配置组和组织单位。 

 1.  在 Azure 门户中，选择“Azure Active Directory”。
 2. 选择“Azure AD Connect”****。
 3. 选择 " **管理预配 (预览") **。
 4. 在 " **配置**" 下，选择您的配置。

 ![“配置”部分](media/how-to-configure/scope1.png)
 
 5. 在 " **配置**" 下，选择 " **编辑范围筛选器** " 以更改配置规则的作用域。
 ![编辑作用域](media/how-to-configure/scope3.png)
 7. 在右侧，可以更改范围。  单击 " **完成**  "，完成后 **保存** 。
 8. 更改作用域后，应 [重新启动预配](#restart-provisioning) ，以便立即同步更改。

## <a name="attribute-mapping"></a>属性映射
Azure AD Connect 云预配，你可以轻松地在本地用户/组对象与 Azure AD 中的对象之间映射属性。  可以根据业务需求自定义默认的属性映射。 因此，可以更改或删除现有属性映射，或创建新的属性映射。  有关详细信息，请参阅 [属性映射](how-to-attribute-mapping.md)。

## <a name="on-demand-provisioning"></a>按需预配
Azure AD Connect 云预配可通过将这些更改应用于单个用户或组来测试配置更改。  你可以使用它来验证并验证对配置所做的更改是否已正确应用并正确地同步到 Azure AD。  有关详细信息，请参阅按 [需预配](how-to-on-demand-provision.md)。

## <a name="restart-provisioning"></a>重新启动预配 
如果不想等待下一次计划的运行，请使用 " **重新启动设置** " 按钮触发设置运行。 
 1.  在 Azure 门户中，选择“Azure Active Directory”。
 2. 选择“Azure AD Connect”****。
 3.  选择 " **管理预配 (预览") **。
 4. 在 " **配置**" 下，选择您的配置。

   ![用于重新启动预配的配置选择](media/how-to-configure/scope1.png)

 5. 在顶部，选择 " **重新启动设置**"。

## <a name="remove-a-configuration"></a>删除配置
若要删除配置，请执行以下步骤。

 1.  在 Azure 门户中，选择“Azure Active Directory”。
 2. 选择“Azure AD Connect”****。
 3. 选择 " **管理预配 (预览") **。
 4. 在 " **配置**" 下，选择您的配置。
   
   ![要删除配置的配置选择](media/how-to-configure/scope1.png)

 5. 在配置屏幕的顶部，选择 " **删除**"。

>[!IMPORTANT]
>在删除配置之前，没有确认。 在选择 " **删除**" 之前，请确保这是要执行的操作。


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
