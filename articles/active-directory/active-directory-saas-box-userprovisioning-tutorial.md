---
title: "教程：Azure Active Directory 与 Box 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 Box 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 9f061f3f5a0a4825854b893150ceccc8951487de
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-configuring-box-for-automatic-user-provisioning"></a>教程：配置 Box 以进行自动用户预配

本教程旨在说明从 Azure AD 自动将用户帐户预配到 Box 和取消其预配需要在 Box 和 Azure 中执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定已有以下各项：

*   Azure Active Directory 租户。
*   已启用 Box 单一登录的订阅。
*   Box 中具有团队管理员权限的用户帐户。

## <a name="assigning-users-to-box"></a>将用户分配到 Box 

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 Box 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 Box 应用：

[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>分配用户和组
可以使用 Azure 门户中的“Box”>“用户和组”选项卡指定应向哪些用户和组授予对 Box 的访问权限。 分配用户或组会导致以下结果：

* Azure AD 允许分配的用户（不管是直接分配还是以组成员的方式进行分配）向 Box 进行身份验证。 如果用户尚未分配，则 Azure AD 不会允许其登录到 Box，并会在 Azure AD 登录页上返回错误。
* Box 的应用磁贴会添加到用户的[应用程序启动程序](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)中。
* 如果启用了自动预配，则会将分配的用户和/或组添加到预配队列进行自动预配。
  
  * 如果仅将用户对象配置为进行预配，则会将所有直接分配的用户置于预配队列中，所有属于任何已分配组成员的用户也会置于预配队列中。 
  * 如果已将组对象配置为进行预配，则会为 Box 预配所有已分配的组对象以及属于这些组的成员的所有用户。 写入 到 Box 以后，组和用户成员身份会保留。

可在进行基于 SAML 的身份验证时使用“属性 > 单一登录”选项卡配置需呈现给 Box 的用户属性（或声明），并可在预配操作过程中使用“属性 > 预配”选项卡配置用户和组属性如何从 Azure AD 流向 Box。

### <a name="important-tips-for-assigning-users-to-box"></a>将用户分配到 Box 的重要提示 

*   建议将单个 Azure AD 用户分配到 Box 以测试预配配置。 其他用户和/或组可以稍后分配。

*   将用户分配到 Box 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-automated-user-provisioning"></a>启用自动化用户预配

本部分指导完成将 Azure AD 连接到 Box 的用户帐户预配 API 并配置预配服务，以便在 Box 中根据 Azure AD 中的用户和组分配来创建、更新和禁用分配的用户帐户。

如果启用了自动预配，则会将分配的用户和/或组添加到预配队列进行自动预配。
    
 * 如果仅将用户对象配置为进行预配，则会将直接分配的用户置于预配队列中，所有属于任何已分配组成员的用户也会置于预配队列中。 
    
 * 如果已将组对象配置为进行预配，则会为 Box 预配所有已分配的组对象以及属于这些组的成员的所有用户。 写入 到 Box 以后，组和用户成员身份会保留。

> [!TIP] 
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Box 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-account-provisioning"></a>配置用户帐户自动预配：

本部分的目的是概述如何对 Box 启用 Active Directory 用户帐户的预配。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果已为 Box 配置单一登录，请使用搜索字段搜索 Box 实例。 否则，请选择“添加”并在应用程序库中搜索“Box”。 从搜索结果中选择 Box，并将其添加到应用程序列表。

3. 选择 Box 实例，然后选择“预配”选项卡。

4. 将“预配模式”设置为“自动”。 

    ![预配](./media/active-directory-saas-box-userprovisioning-tutorial/provisioning.png)

5. 在“管理员凭据”部分单击“授权”，在新的浏览器窗口中打开 Box 登录对话框。

6. 在“登录以授予对 Box 的访问权限”页上，提供所需的凭据，然后单击“授权”。 
   
    ![启用自动用户设置](./media/active-directory-saas-box-userprovisioning-tutorial/IC769546.png "Enable automatic user provisioning")

7. 单击“授予对 Box 的访问权限”对此操作授权，然后返回到 Azure 门户。 
   
    ![启用自动用户设置](./media/active-directory-saas-box-userprovisioning-tutorial/IC769549.png "Enable automatic user provisioning")

8. 在 Azure 门户中单击“测试连接”，确保 Azure AD 可以连接到 Box 应用。 如果连接失败，请确保 Box 帐户具有团队管理员权限，并重试“授权”步骤。

9. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址，并选中复选框。

10. 单击“保存”。

11. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Box”。

12. 在“属性映射”部分中，查看从 Azure AD 同步到 Box 的用户属性。 选为“匹配”属性的特性用于匹配 Box 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

13. 若要为 Box 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

14. 单击“保存”。

这会开始“用户和组”部分中分配给 Box 的任何用户和/或组的初始同步。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 20 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务对 Box 应用执行的所有操作。

现可创建测试帐户。 最多等待 20 分钟验证帐户是否已同步到 Box。

在 Box 租户中，同步的用户列在“管理控制台”的“托管用户”下。

![集成状态](./media/active-directory-saas-box-userprovisioning-tutorial/IC769556.png "Integration status")


## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置单一登录](active-directory-saas-box-tutorial.md)