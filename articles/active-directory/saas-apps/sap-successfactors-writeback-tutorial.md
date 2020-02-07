---
title: 教程：在 Azure Active Directory 中配置 SuccessFactors 写回Microsoft Docs
description: 了解如何从 Azure AD 配置属性写回 SuccessFactors
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060042"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>教程：配置从 Azure AD 到 SAP SuccessFactors 的属性写回（预览版）
本教程的目的是说明从 Azure AD 到 SuccessFactors Employee Central 需要执行写回属性所需执行的步骤。 写回当前支持的唯一特性是 email 特性。 

## <a name="overview"></a>概述

使用[SuccessFactors 和本地 AD](sap-successfactors-inbound-provisioning-tutorial.md)预配应用或[SuccessFactors 将](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)入站预配集成设置为 Azure AD 预配应用程序后，可以选择将 SuccessFactors 写回应用程序配置为将电子邮件地址写回到 SuccessFactors。 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

此 SuccessFactors 写回用户预配解决方案非常适合用于：

* 使用 Office 365 的组织希望将它管理的权威属性（如电子邮件地址）回写回 SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>为集成配置 SuccessFactors

所有 SuccessFactors 预配连接器的一个常见要求是，它们需要具有调用 SuccessFactors OData Api 的适当权限的 SuccessFactors 帐户的凭据。 本部分介绍在 SuccessFactors 中创建服务帐户并授予适当权限的步骤。 

* [在 SuccessFactors 中创建/识别 API 用户帐户](#createidentify-api-user-account-in-successfactors)
* [创建 API 权限角色](#create-an-api-permissions-role)
* [为 API 用户创建权限组](#create-a-permission-group-for-the-api-user)
* [向权限组授予权限角色](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在 SuccessFactors 中创建/识别 API 用户帐户
与 SuccessFactors 管理员团队或实施合作伙伴合作，在 SuccessFactors 中创建或标识将用于调用 OData Api 的用户帐户。 在 Azure AD 中配置预配应用时，将需要此帐户的用户名和密码凭据。 

### <a name="create-an-api-permissions-role"></a>创建 API 权限角色

* 使用有权访问管理中心的用户帐户登录到 SAP SuccessFactors。
* 搜索 "*管理权限角色*"，然后从搜索结果中选择 "**管理权限角色**"。
  ![管理权限角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* 从 "权限角色" 列表中，单击 "**新建**"。
  > [!div class="mx-imgBorder"]
  > ![创建新的权限角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 为新的权限角色添加**角色名称**和**描述**。 名称和描述应表示角色用于 API 使用权限。
  > [!div class="mx-imgBorder"]
  > ![权限角色详细信息](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* 在 "权限设置" 下，单击 "**权限 ...** "，在权限列表中向下滚动，然后单击 "**管理集成工具**"。 选中 "**允许管理员通过基本身份验证访问 ODATA API**" 框。
  > [!div class="mx-imgBorder"]
  > ![管理集成工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 在同一框中向下滚动，然后选择 "**员工中心 API**"。 添加权限，如下所示，使用 odata api 并使用 ODATA API 进行编辑。 如果你计划使用同一帐户进行写回 SuccessFactors 方案，请选择 "编辑" 选项。 
  > [!div class="mx-imgBorder"]
  > ![读取写入权限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* 单击“完成”。 单击“保存更改”。

### <a name="create-a-permission-group-for-the-api-user"></a>为 API 用户创建权限组

* 在 SuccessFactors 管理中心，搜索 "*管理权限组*"，然后从搜索结果中选择 "**管理权限组**"。
  > [!div class="mx-imgBorder"]
  > ![管理权限组](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* 从 "管理权限组" 窗口中，单击 "**新建**"。
  > [!div class="mx-imgBorder"]
  > ![添加新组](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 为新组添加组名称。 组名称应指示组适用于 API 用户。
  > [!div class="mx-imgBorder"]
  > ![权限组名称](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* 将成员添加到组。 例如，你可以从 "人员池" 下拉菜单中选择 "**用户名**"，然后输入将用于集成的 API 帐户的用户名。 
  > [!div class="mx-imgBorder"]
  > ![添加组成员](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* 单击 "**完成**" 以完成创建权限组。

### <a name="grant-permission-role-to-the-permission-group"></a>向权限组授予权限角色

* 在 SuccessFactors 管理中心，搜索 "*管理权限角色*"，然后从搜索结果中选择 "**管理权限角色**"。
* 从 "**权限角色" 列表**中，选择为 API 使用权限创建的角色。
* 在 "将**此角色授予 ...** " 下，单击 "**添加 ...** " 按钮。
* 从下拉菜单中选择 "**权限组 ...** "，然后单击 "**选择 ...** " 以打开 "组" 窗口以搜索并选择上面创建的组。 
  > [!div class="mx-imgBorder"]
  > ![添加权限组](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* 查看权限角色授予权限组。 
  > [!div class="mx-imgBorder"]
  > ![权限角色和组详细信息](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* 单击“保存更改”。

## <a name="configuring-successfactors-writeback"></a>配置 SuccessFactors 写回

本部分提供 

* [添加预配连接器应用并配置与 SuccessFactors 的连接](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [配置属性映射](#part-2-configure-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第1部分：添加预配连接器应用并配置与 SuccessFactors 的连接

**配置 SuccessFactors 写回：**

1. 转到 <https://portal.azure.com>

2. 在左侧导航栏中选择“Azure Active Directory”。

3. 依次选择“企业应用程序”、“所有应用程序”。

4. 依次选择“添加应用程序”、“所有”类别。

5. 搜索**SuccessFactors 写回**，并从库中添加该应用。

6. 添加应用并显示应用详细信息屏幕后，请选择“预配”

7. 将**设置** **模式**更改为**自动**

8. 按如下所述完成“管理员凭据”部分：

   * **管理员用户名**–输入 SuccessFactors API 用户帐户的用户名，并追加公司 ID。 它采用以下格式： **username\@companyID**

   * **管理员密码–** 输入 SuccessFactors API 用户帐户的密码。 

   * **租户 URL –** 输入 SuccessFactors OData API 服务终结点的名称。 仅输入不带 http 或 https 的服务器的主机名。 此值应类似于： **api-server-name.successfactors.com**。

   * **通知电子邮件 -** 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。
    > [!NOTE]
    > 如果预配作业进入[隔离](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)状态，Azure AD 预配服务将发送电子邮件通知。

   * 单击“测试连接”按钮。 如果连接测试成功，请单击顶部的“保存”按钮。 如果失败，请仔细检查 SuccessFactors 凭据和 URL 是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 门户](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 成功保存凭据后，"**映射**" 部分会显示默认映射 "**同步 Azure Active Directory 用户 SuccessFactors** "

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：配置属性映射

在本部分中，你将配置用户数据如何从 SuccessFactors 流向 Active Directory。

1. 在 "**映射**" 下的 "设置" 选项卡上，单击 " **Azure Active Directory 用户同步到 SuccessFactors**"。

1. 在 "**源对象范围**" 字段中，可以通过定义一组基于属性的筛选器，选择要将 Azure AD 中的哪些用户组视为写回。 默认范围是“Azure AD 中的所有用户”。 
   > [!TIP]
   > 首次配置预配应用时，需要测试和验证属性映射和表达式，以确保它提供所需的结果。 Microsoft 建议使用 "**源对象范围**" 下的范围筛选器，通过 Azure AD 中的一些测试用户来测试映射。 验证确保映射正常工作后，可删除筛选器，也可逐渐扩大范围以包含更多用户。

1. "**目标对象操作**" 字段仅支持**更新**操作。

1. 在 "**属性映射**" 部分中，您只能更改用于链接 SuccessFactors 用户配置文件与 Azure AD 用户以及 Azure AD 中哪个属性用作电子邮件来源的匹配 ID。 
    >[!div class="mx-imgBorder"]
    >![Azure 门户](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >SuccessFactors 写回只支持 email 属性。 请不要使用 "**添加新映射**" 来添加新属性。 

1. 要保存映射，请单击“属性-映射”部分顶部的“保存”。

属性映射配置完成后，即可[启用并启动用户预配服务](#enable-and-launch-user-provisioning)。

## <a name="enable-and-launch-user-provisioning"></a>启用并启动用户预配

SuccessFactors 预配应用配置完成后，即可在 Azure 门户中打开预配服务。

> [!TIP]
> 默认情况下，启用预配服务时，它会为范围中的所有用户启动预配操作。 如果映射出错或存在 Workday 数据问题，则预配作业可能会失败并转入隔离状态。 要避免这种情况，最佳做法是先配置“源对象范围”筛选器并使用少量测试用户来测试属性映射，然后再为所有用户启动完全同步。 验证确保映射正常工作且获得所需结果后，可删除筛选器或逐渐扩大范围以包含更多用户。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。

2. 单击 **“保存”** 。

3. 此操作将启动初始同步，这可能会花费几小时的时间，具体取决于 SuccessFactors 租户中的用户数量。 您可以查看进度栏，以跟踪同步周期的进度。 

4. 无论何时，检查 Azure 门户中的“审核日志”选项卡都可以查看预配服务执行的操作。 审核日志列出预配服务执行的所有同步事件，例如，正在从 Workday 中读出哪些用户，随后将其添加或更新到 Active Directory。 

5. 完成初始同步后，系统会在“预配”选项卡中写入一份审核摘要报告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![设置进度栏](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何在 SuccessFactors 与 Azure Active Directory 之间配置单一登录](successfactors-tutorial.md)
* [了解如何将其他 SaaS 应用程序与 Azure Active Directory 进行集成](tutorial-list.md)
* [了解如何导出和导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)

