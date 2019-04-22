---
title: 教程：配置自动用户预配 Azure Active Directory 与 Velpic |Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消其预配用户帐户添加到 Velpic。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16c302fbe151d6cd8c2198240bc31a2bd69dbd7b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270897"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Velpic

本教程的目的是说明您需要在 Velpic 和 Azure AD 自动预配和取消其预配到 Velpic 的用户帐户从 Azure AD 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定已有以下各项：

* 一个 Azure Active Directory 租户
* 与 Velpic 租户[企业计划](https://www.velpic.com/pricing.html)或更好地启用
* Velpic 中具有管理员权限的用户帐户

## <a name="assigning-users-to-velpic"></a>将用户分配到 Velpic

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只有已“分配”到 Azure AD 中的应用程序的用户和组才进行同步。 

在配置和启用预配服务前, 您需要确定哪些用户和/或 Azure AD 中的组表示需要访问 Velpic 应用的用户。 确定后，可按照此处的说明将这些用户分配到 Velpic 应用：

[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>将用户分配到 Velpic 的重要提示

* 建议将单个 Azure AD 用户分配到 Velpic 以测试预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Velpic 中，必须选择**用户**角色或另一个有效特定于应用程序角色 （如果可用） 在分配对话框中的。 请注意，**默认访问权限**角色不可用于预配，且将跳过这些用户。

## <a name="configuring-user-provisioning-to-velpic"></a>配置用户预配到 Velpic

本部分将指导你完成将 Azure AD 连接到 Velpic 的用户帐户预配 API，并配置预配服务以便创建、 更新和禁用所分配的基于用户和组分配 Azure AD 中的 Velpic 中的用户帐户。

> [!TIP]
> 您还可以选择为 Velpic 启用基于 SAML 的单一登录中的说明提供[Azure 门户](https://portal.azure.com)。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>若要配置自动用户帐户预配到 Velpic 在 Azure AD 中：

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果你已为实现单一登录配置 Velpic，搜索 Velpic 使用搜索字段的实例。 否则，请选择**外**并搜索**Velpic**应用程序库中。 从搜索结果中，选择 Velpic 并将其添加到应用程序的列表。

3. 选择实例 Velpic，然后选择**预配**选项卡。

4. 将“预配模式”设置为“自动”。

    ![Velpic 预配](./media/velpic-provisioning-tutorial/Velpic1.png)

5. 下**管理员凭据**部分中，输入**租户 URL 和机密令牌**的 Velpic。 (您可以在 Velpic 帐户下找到这些值：**管理** > **集成** > **插件** > **SCIM**)

    ![授权值](./media/velpic-provisioning-tutorial/Velpic2.png)

6. 在 Azure 门户中，单击**测试连接**以确保 Azure AD 可以连接到 Velpic 应用。 如果连接失败，请确保你的 Velpic 帐户具有管理员权限，然后重试步骤 5。

7. 在“通知电子邮件”字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中下面的复选框。

8. 单击“ **保存**”。

9. 在映射部分下选择**Azure Active Directory 用户同步到 Velpic**。

10. 在中**属性映射**部分中，查看到 Velpic 将从 Azure AD 中同步的用户属性。 请注意，选为**匹配**属性将用于匹配 Velpic 中以执行更新操作的用户帐户。 选择“保存”按钮以提交任何更改。

11. 若要启用 Azure AD 预配服务的 Velpic，更改**预配状态**到**上**中**设置**部分

12. 单击“ **保存**”。

这将启动初始任何的同步用户和/或组分配到 Velpic 中的用户和组部分。 请注意，初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)