---
title: 教程：在 Azure Active Directory 中配置 SuccessFactors 入站预配Microsoft Docs
description: 了解如何配置从 SuccessFactors 到 Azure AD 的入站预配
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063216"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>教程：配置 SAP SuccessFactors 以 Azure AD 用户预配（预览版）
本教程的目的是演示将辅助角色数据从 SuccessFactors Employee Central 预配到 Azure Active Directory 中所需执行的步骤，并提供电子邮件地址的可选回写到 SuccessFactors。 此集成在公共预览版中，支持从 SuccessFactors 员工中心检索70多个以上的[用户属性](../app-provisioning/sap-successfactors-attribute-reference.md)。 

>[!NOTE]
>如果要从 SuccessFactors 预配的用户是仅限云的用户，而不需要本地 AD 帐户，请使用本教程。 如果用户仅需要本地 AD 帐户或同时需要 AD 和 Azure AD 帐户，请参阅[配置 SAP SuccessFactors 以 Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview)用户预配教程。 

## <a name="overview"></a>概述

[Azure Active Directory 用户预配服务](../app-provisioning/user-provisioning.md)与[SuccessFactors 员工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)集成，以便管理用户的标识生命周期。 

Azure AD 用户预配服务支持的 SuccessFactors 用户预配工作流可实现以下人力资源和标识生命周期管理方案的自动化：

* **招聘新员工**-将新员工添加到 SuccessFactors 时，会自动在 Azure Active Directory 和 Azure AD 支持的 Office 365 和[其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)中创建用户帐户，并将电子邮件地址的回写到 SuccessFactors。

* **员工属性和配置文件更新**-在 SuccessFactors 中更新员工记录（例如，他们的姓名、标题或经理）时，将自动更新其用户帐户 Azure Active Directory 365 和[Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

* **员工**离职-当员工在 SuccessFactors 中终止时，他们的用户帐户会在 Azure Active Directory 和[Azure AD 支持的 Office 365 和其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)中自动禁用。

* **员工 rehires** -当员工在 SuccessFactors 中 rehired 时，可以根据你的喜好自动重新激活或重新预配其旧帐户（取决于你的偏好） Azure Active Directory 和（可选） Office 365 和[Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

Azure Active Directory 用户预配解决方案的这一 SuccessFactors 非常适合用于：

* 需要为 SuccessFactors 用户预配预先构建的基于云的解决方案的组织

* 需要从 SuccessFactors 到 Azure Active Directory 的直接用户预配的组织

* 要求使用从[SuccessFactors Employee Central （EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)获取的数据设置用户的组织

* 使用 Office 365 收发电子邮件的组织

## <a name="solution-architecture"></a>解决方案体系结构

本部分介绍仅限云的用户的端到端用户预配解决方案体系结构。 有两个相关的流：

* **权威 HR 数据流–从 SuccessFactors 到 Azure Active Directory：** 在此流工作人员事件（例如新员工、转移、终止）中，第一次发生于 cloud SuccessFactors Employee Central，然后将事件数据流流入 Azure Active Directory。 根据事件，可能会导致在 Azure AD 中创建/更新/启用/禁用操作。
* **电子邮件写回流–从本地 Active Directory 到 SuccessFactors：** 帐户创建完成后 Azure Active Directory，可将 Azure AD 中生成的电子邮件属性值或 UPN 写回 SuccessFactors。

  ![概述](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>端到端用户数据流

1. HR 团队在 SuccessFactors 员工中心执行工作人员交易（Joiners/搬运工/离开者或新聘用/转让/终止）
2. Azure AD 预配服务运行从 SuccessFactors EC 计划的标识同步，并识别需要处理的更改，以便与本地 Active Directory 同步。
3. Azure AD 预配服务确定更改，并在 Azure AD 中为用户调用创建/更新/启用/禁用操作。
4. 如果配置了[SuccessFactors 写回应用](sap-successfactors-writeback-tutorial.md)，则从 Azure AD 检索用户的电子邮件地址。 
5. Azure AD 预配服务根据使用的匹配属性将电子邮件属性写回 SuccessFactors。

## <a name="planning-your-deployment"></a>计划部署

将从 SuccessFactors 到 Azure AD 的云 HR 驱动的用户预配配置为涵盖不同方面（例如：

* 确定匹配 ID 
* 属性映射
* 属性转换 
* 范围筛选器

有关这些主题的综合指导原则，请参阅[云 HR 部署计划](../app-provisioning/plan-cloud-hr-provision.md)。 

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>配置从 SuccessFactors 到 Azure AD 的用户预配

本部分提供将用户帐户从 SuccessFactors 预配到 Azure AD 的步骤。

* [添加预配连接器应用并配置与 SuccessFactors 的连接](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [配置属性映射](#part-2-configure-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第1部分：添加预配连接器应用并配置与 SuccessFactors 的连接

**若要将 SuccessFactors 配置为 Azure AD 预配：**

1. 转到 <https://portal.azure.com>

2. 在左侧导航栏中选择“Azure Active Directory”。

3. 依次选择“企业应用程序”、“所有应用程序”。

4. 依次选择“添加应用程序”、“所有”类别。

5. 搜索**SuccessFactors 以 Azure Active Directory 用户预配**，并从库中添加该应用。

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
    >![Azure 门户](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * 成功保存凭据后，"**映射**" 部分会显示默认映射 "将**SuccessFactors 用户同步到 Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：配置属性映射

在本部分中，你将配置用户数据如何从 SuccessFactors 流向 Active Directory。

1. 在 "**映射**" 下的 "设置" 选项卡上，单击 "**同步 SuccessFactors 用户 Azure Active Directory**。

1. 在 "**源对象范围**" 字段中，可以通过定义一组基于属性的筛选器，选择要在 SuccessFactors 中设置为 Azure AD 的用户组。 默认作用域为 "SuccessFactors 中的所有用户"。 示例筛选器：

   * 示例：范围为 personIdExternal 介于1000000和2000000之间的用户（不包括2000000）

      * 属性： personIdExternal

      * 运算符：REGEX 匹配

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 示例：仅限员工和非临时工

      * 属性：EmployeeID

      * 运算符：IS NOT NULL

   > [!TIP]
   > 首次配置预配应用时，需要测试和验证属性映射和表达式，以确保它提供所需的结果。 Microsoft 建议使用**源对象范围**下的范围筛选器，通过 SuccessFactors 中的几个测试用户测试映射。 验证确保映射正常工作后，可删除筛选器，也可逐渐扩大范围以包含更多用户。

   > [!CAUTION] 
   > 设置引擎的默认行为是禁用/删除超出作用域的用户。 这在您的 SuccessFactors 中可能不适合 Azure AD 集成。 若要替代此默认行为，请参阅[跳过删除超出范围的用户帐户的](../app-provisioning/skip-out-of-scope-deletions.md)文章
  
1. 在“目标对象操作”字段中，可全局筛选要对 Active Directory 执行的操作。 “创建”和“更新”是最常见的操作。

1. 在 "**属性映射**" 部分中，可以定义单个 SuccessFactors 属性映射到 Active Directory 属性的方式。

  >[!NOTE]
  >有关应用程序支持的 SuccessFactors 特性的完整列表，请参阅[SuccessFactors 特性引用](../app-provisioning/sap-successfactors-attribute-reference.md)


1. 单击现有的属性映射可将其更新，单击屏幕底部的“添加新映射”可添加新的映射。 单个属性映射支持以下属性：

      * **映射类型**

         * **Direct** –将 SuccessFactors 属性的值写入 AD 属性，无更改

         * **常量映射** - 将静态常量字符串值写入 AD 属性

         * **Expression** –允许你根据一个或多个 SuccessFactors 属性将自定义值写入 AD 属性。 [有关详细信息，请参阅这篇有关表达式的文章](../app-provisioning/functions-for-customizing-application-data.md)。

      * **源属性**-SuccessFactors 中的用户属性

      * **默认值** – 可选。 如果源属性的值为空，映射将改为写入此值。
            最常见的配置是将此项留空。

      * **目标属性** - Active Directory 中的用户属性。

      * **使用此属性匹配对象**–是否应使用此映射唯一标识 SuccessFactors 与 Active Directory 之间的用户。 此值通常在 SuccessFactors 的 "Worker ID" 字段上设置，该字段通常映射到 Active Directory 中的 "员工 ID" 属性之一。

      * 匹配优先级 – 可设置多个匹配属性。 当存在匹配时，会按照此字段定义的顺序进行评估。 一旦找到匹配项，就不再继续评估其他匹配属性。

      * **应用此映射**

         * 始终 – 对用户创建和更新操作均应用此映射

         * 仅创建期间 - 仅对用户创建操作应用此映射

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

* [详细了解用于入站预配的支持 SuccessFactors 属性](../app-provisioning/sap-successfactors-attribute-reference.md)
* [了解如何配置电子邮件写回 SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何在 SuccessFactors 与 Azure Active Directory 之间配置单一登录](successfactors-tutorial.md)
* [了解如何将其他 SaaS 应用程序与 Azure Active Directory 进行集成](tutorial-list.md)
* [了解如何导出和导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)


