---
title: "预配到 Azure AD 库应用程序的用户组错误 | Microsoft Docs"
description: "了解如何找到为何预配到应用程序的用户组与预期不同的原因"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 5ccb9e920ce8e1c95b9ce0ffc8626f2dcf3783d8
ms.contentlocale: zh-cn
ms.lasthandoff: 04/11/2017

---

<a id="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application" class="xliff"></a>

# 预配到 Azure AD 库应用程序的用户组错误

将哪些用户预配到应用主要取决于已将哪些用户和组**分配**到该应用程序。

使用以下资源，了解如何查看已将哪些用户和组分配到 Azure Active Directory 中的应用程序。

<a id="assign-a-user-directly-as-an-administrator" class="xliff"></a>

## 以管理员身份直接将用户分配到应用程序

若要直接将一个或多个用户分配到应用程序，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，以**全局管理员**身份登录

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  应用程序加载后，在应用程序的左侧导航菜单中，单击“用户和组”。

8.  单击“用户和组”列表顶部的“添加”按钮，打开“添加分配”边栏选项卡。

9.  在“添加分配”边栏选项卡中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的**用户**上以显示**复选框**。 单击用户头像或徽标旁边的复选框，将用户添加到“已选择”列表。

12. **可选：**如果想要**添加多个用户**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**全名**或**电子邮件地址**，然后单击复选框将此用户添加到“已选择”列表。

13. 选择完所有用户后，单击“选择”按钮将所有已选择的用户添加到要分配给应用程序的用户和组的列表中。

14. **可选：**单击“添加分配”边栏选项卡中的“选择角色”选择器可选择要分配给所选用户的角色。

15. 单击“分配”按钮，将应用程序分配给选定用户。

如果已配置预配且已对应用运行预配，新用户应会在约 10 分钟后预配到应用程序。 查看**审核日志**了解详细信息。

<a id="assign-a-group-directly-to-an-application-as-an-administrator" class="xliff"></a>

## 以管理员身份直接将组分配到应用程序

若要直接将一个或多个组分配到应用程序，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，以**全局管理员**身份登录

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  应用程序加载后，在应用程序的左侧导航菜单中，单击“用户和组”。

8.  单击“用户和组”列表顶部的“添加”按钮，打开“添加分配”边栏选项卡。

9.  在“添加分配”边栏选项卡中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的组的**完整组名**。

11. 将鼠标悬停在列表中的**组**上以显示**复选框**。 单击组头像或徽标旁边的复选框以将用户添加到“已选择”列表。

12. **可选：**如果想要**添加多个组**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**完整组名**，然后单击复选框以将此组添加到“已选择”列表。

13. 选择完所有组后，单击“选择”按钮将所有已选择的组添加到要分配给应用程序的用户和组的列表中。

14. **可选：**单击“添加分配”边栏选项卡中的“选择角色”选择器以选择要分配给所选组的角色。

15. 单击“分配”按钮，将应用程序分配给所选组。

如果预配已配置且已针对应用运行，应在约 10 分钟内将组中包含的新用户预配到应用程序。 查看**审核日志**了解详细信息。

>[!IMPORTANT]
>如果某些应用程序支持，除了成员以外，还可以对组名和详细信息进行预配。 可通过对“预配”选项卡中显示的组对象启用或禁用**映射**，从而启用或禁用此功能。 
>
>

如果启用了预配组，请务必查看属性映射以确保相应字段正用于“匹配 ID”。 这可以是显示名称或电子邮件别名，因为如果匹配属性为空或未为 Azure AD 中的组填充该属性，就不会预配组及其成员。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[Azure Active Directory SaaS 应用程序的自动化用户预配和取消预配](active-directory-saas-app-provisioning.md)

