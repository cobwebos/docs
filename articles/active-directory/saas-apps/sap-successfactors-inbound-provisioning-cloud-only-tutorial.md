---
title: 教程：在 Azure 活动目录中配置成功因素入站预配 |微软文档
description: 了解如何配置从成功因子到 Azure AD 的入站预配
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063216"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>教程：将 SAP 成功因子配置为 Azure AD 用户预配（预览）
本教程的目标是显示需要执行的步骤，以将工作辅助因素员工中心的工作数据预配到 Azure 活动目录中，并选择性地将电子邮件地址写回到成功因子。 此集成处于公共预览版中，支持从 SuccessFactors 员工中心检索[70 多个用户属性](../app-provisioning/sap-successfactors-attribute-reference.md)。 

>[!NOTE]
>如果要从 SuccessFactors 预配的用户是不需要本地 AD 帐户的仅云用户，请使用本教程。 如果用户只需要本地 AD 帐户或 AD 和 Azure AD 帐户，请参阅有关将 SAP[成功因子配置为活动目录](sap-successfactors-inbound-provisioning-tutorial.md#overview)用户预配的教程。 

## <a name="overview"></a>概述

[Azure 活动目录用户预配服务](../app-provisioning/user-provisioning.md)与[成功因素员工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)集成，以便管理用户的标识生命周期。 

Azure AD 用户预配服务支持的 SuccessFactors 用户预配工作流支持以下人力资源和身份生命周期管理方案的自动化：

* **雇用新员工**- 当新员工添加到成功因子时，将自动在 Azure 活动目录中创建用户帐户，并可以选择 Office 365 和[Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)，并将电子邮件地址写回到成功因子。

* **员工属性和配置文件更新**- 在成功因子中更新员工记录（如其名称、标题或管理器）时，其用户帐户将自动更新 Azure 活动目录，并可以选择 Office 365[和 Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

* **员工终止**- 当员工在成功因子中终止时，其用户帐户将自动在 Azure 活动目录中禁用，并且可以选择 Office 365 和[Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

* **员工重新雇用**- 当员工在 SuccessFactors 中重新雇用时，其旧帐户可以自动重新激活或重新预配（具体取决于您的偏好）到 Azure 活动目录，并可以选择 Office 365[和 Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

此成功因素到 Azure 活动目录用户预配解决方案非常适合：

* 希望为成功因素用户预配构建的预构建基于云的解决方案的组织

* 需要从成功因子直接用户预配到 Azure 活动目录的组织

* 需要使用从[成功因素员工中心 （EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)获得的数据预配用户的组织

* 使用 Office 365 收发电子邮件的组织

## <a name="solution-architecture"></a>解决方案体系结构

本节介绍仅云用户的端到端用户预配解决方案体系结构。 有两个相关的流：

* **权威 HR 数据流 – 从成功因素到 Azure 活动目录：** 在此流工作线程事件（如新员工、转移、终止）首先发生在云成功因素员工中心，然后事件数据流入 Azure 活动目录。 根据事件的不同，它可能导致在 Azure AD 中创建/更新/启用/禁用操作。
* **电子邮件回写流程 – 从本地活动目录到成功因素：** 在 Azure 活动目录中完成帐户创建后，在 Azure AD 中生成的电子邮件属性值或 UPN 可以编写回成功因子。

  ![概述](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>端到端用户数据流

1. HR 团队在成功因素员工中心执行员工事务（加入者/移动者/离职者或新员工/调动/终止）
2. Azure AD 预配服务运行来自成功因子 EC 的标识的计划同步，并标识需要处理的更改才能与本地活动目录同步。
3. Azure AD 预配服务确定更改，并调用 Azure AD 中的用户的创建/更新/启用/禁用操作。
4. 如果配置了[成功因子回写应用](sap-successfactors-writeback-tutorial.md)，则从 Azure AD 检索用户的电子邮件地址。 
5. Azure AD 预配服务根据使用的匹配属性将电子邮件属性写回成功因子。

## <a name="planning-your-deployment"></a>计划部署

将云 HR 驱动的用户配置从成功因子配置到 Azure AD 需要大量规划，涵盖不同方面，例如：

* 确定匹配的 ID 
* 属性映射
* 属性转换 
* 范围筛选器

有关有关这些主题的全面指南，请参阅[云 HR 部署计划](../app-provisioning/plan-cloud-hr-provision.md)。 

## <a name="configuring-successfactors-for-the-integration"></a>为集成配置成功因素

所有成功因子预配连接器的一个常见要求是，它们需要具有正确权限的 SuccessFactors 帐户的凭据来调用成功因子 OData API。 本节介绍在成功因子中创建服务帐户并授予适当权限的步骤。 

* [在成功因素中创建/标识 API 用户帐户](#createidentify-api-user-account-in-successfactors)
* [创建 API 权限角色](#create-an-api-permissions-role)
* [为 API 用户创建权限组](#create-a-permission-group-for-the-api-user)
* [向权限组授予权限角色](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在成功因素中创建/标识 API 用户帐户
与您的成功因素管理团队或实施合作伙伴合作，在成功因子中创建或标识将用于调用 OData API 的用户帐户。 在 Azure AD 中配置预配应用时，需要此帐户的用户名和密码凭据。 

### <a name="create-an-api-permissions-role"></a>创建 API 权限角色

* 使用有权访问管理中心的用户帐户登录到 SAP 成功因子。
* 搜索*管理权限角色*，然后从搜索结果中选择 **"管理权限角色**"。
  ![管理权限角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* 从"权限角色列表"中，单击"**新建**"。
  > [!div class="mx-imgBorder"]
  > ![创建新的权限角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 为新的权限角色添加**角色名称**和**说明**。 名称和说明应指示该角色用于 API 使用权限。
  > [!div class="mx-imgBorder"]
  > ![权限角色详细信息](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* 在"权限设置"下，单击"**权限..."，** 然后向下滚动权限列表，然后单击"**管理集成工具**"。 选中"**允许管理员通过基本身份验证访问 OData API"** 复选框。
  > [!div class="mx-imgBorder"]
  > ![管理集成工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 在同一框中向下滚动并选择 **"员工中心 API**"。 添加权限，如下所示，以便使用 ODATA API 进行读取并使用 ODATA API 进行编辑。 如果您计划对"向成功因素"方案使用相同的帐户，请选择编辑选项。 
  > [!div class="mx-imgBorder"]
  > ![读取写入权限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* 单击“完成”****。 单击“保存更改”****。

### <a name="create-a-permission-group-for-the-api-user"></a>为 API 用户创建权限组

* 在成功因子管理中心中，搜索*管理权限组*，然后从搜索结果中选择 **"管理权限组**"。
  > [!div class="mx-imgBorder"]
  > ![管理权限组](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* 在"管理权限组"窗口中，单击"**新建**"。
  > [!div class="mx-imgBorder"]
  > ![添加新组](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 为新组添加组名称。 组名称应指示组适用于 API 用户。
  > [!div class="mx-imgBorder"]
  > ![权限组名称](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* 将成员添加到组。 例如，您可以从"人池"下拉菜单中选择**用户名**，然后输入将用于集成的 API 帐户的用户名。 
  > [!div class="mx-imgBorder"]
  > ![添加组成员](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* 单击 **"完成"** 以完成创建权限组。

### <a name="grant-permission-role-to-the-permission-group"></a>向权限组授予权限角色

* 在成功因子管理中心中，搜索*管理权限角色*，然后从搜索结果中选择 **"管理权限角色**"。
* 从**权限角色列表中**，选择为 API 使用权限创建的角色。
* 在 **"将此角色授予..."** 下，单击"**添加..."** 按钮。
* 从下拉菜单中选择 **"权限组..."，** 然后单击"**选择..."** 以打开"组"窗口以搜索并选择上面创建的组。 
  > [!div class="mx-imgBorder"]
  > ![添加权限组](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* 查看权限组授予的权限角色。 
  > [!div class="mx-imgBorder"]
  > ![权限角色和组详细信息](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* 单击“保存更改”****。

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>将用户预配从成功因子配置到 Azure AD

本节提供从成功因子到 Azure AD 的用户帐户预配步骤。

* [添加预配连接器应用并将连接配置为成功因素](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [配置属性映射](#part-2-configure-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第 1 部分：添加预配连接器应用并将连接配置为成功因素

**要将成功因素配置为 Azure AD 预配：**

1. 转到 <https://portal.azure.com>

2. 在左侧导航栏中选择“Azure Active Directory”。****

3. 依次选择“企业应用程序”、“所有应用程序”。********

4. 依次选择“添加应用程序”、“所有”类别。********

5. 搜索**成功因子到 Azure 活动目录用户预配**，并从库中添加该应用。

6. 添加应用并显示应用详细信息屏幕后，请选择“预配”****

7. 将“预配模式”更改为“自动”**** ********

8. 按如下所述完成“管理员凭据”部分：****

   * **管理员用户名**– 输入成功因素 API 用户帐户的用户名，并附上公司 ID。 它有格式：**\@用户名公司ID**

   * **管理员密码 |** 输入成功因素 API 用户帐户的密码。 

   * **租户 URL |** 输入成功因素 OData API 服务终结点的名称。 仅输入没有 http 或 https 的服务器主机名。 此值应如下所示 **：api-server-name.successfactors.com**。

   * **通知电子邮件 |** 输入您的电子邮件地址，并选中"如果发生故障发送电子邮件"复选框。
    > [!NOTE]
    > 如果预配作业进入[隔离](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)状态，Azure AD 预配服务将发送电子邮件通知。

   * 单击“测试连接”按钮。**** 如果连接测试成功，请单击顶部的 **"保存**"按钮。 如果失败，请仔细检查成功因子凭据和 URL 是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 门户](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * 成功保存凭据后，"**映射"** 部分将显示默认映射**将成功因子用户同步到 Azure 活动目录**

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：配置属性映射

在本节中，您将配置用户数据如何从成功因子流向活动目录。

1. 在 **"映射**"下的预配选项卡上，单击"**将成功因子用户同步到 Azure 活动目录**"。

1. 在 **"源对象范围"** 字段中，可以通过定义一组基于属性的筛选器，选择 SuccessFactors 中哪些用户集应位于预配到 Azure AD 的范围内。 默认范围为"成功因子中的所有用户"。 示例筛选器：

   * 示例：在 1000000 到 200000 之间（不包括 20000000）之间的用户 Id 外部范围

      * 属性：人 Id 外部

      * 运算符：REGEX 匹配

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 示例：仅限员工和非临时工

      * 属性：EmployeeID

      * 运算符：IS NOT NULL

   > [!TIP]
   > 首次配置预配应用时，需要测试和验证属性映射和表达式，以确保它提供所需的结果。 Microsoft 建议使用**源对象作用域**下的范围筛选器与成功因子中的几个测试用户一起测试映射。 验证确保映射正常工作后，可删除筛选器，也可逐渐扩大范围以包含更多用户。

   > [!CAUTION] 
   > 预配引擎的默认行为是禁用/删除超出范围的用户。 在"成功因素"到 Azure AD 集成中，这可能不可取。 要重写此默认行为，请参阅跳过[删除超出范围的用户帐户](../app-provisioning/skip-out-of-scope-deletions.md)的文章
  
1. 在“目标对象操作”字段中，可全局筛选要对 Active Directory 执行的操作****。 **创建**和**更新**是最常见的。

1. 在 **"属性映射"** 部分中，您可以定义各个成功因素属性如何映射到活动目录属性。

  >[!NOTE]
  >有关应用程序支持的成功因子属性的完整列表，请参阅[成功因素属性参考](../app-provisioning/sap-successfactors-attribute-reference.md)


1. 单击现有的属性映射可将其更新，单击屏幕底部的“添加新映射”可添加新的映射。**** 单个属性映射支持以下属性：

      * **映射类型**

         * **直接**= 将成功因子属性的值写入 AD 属性，无需更改

         * **常量**- 将静态常量字符串值写入 AD 属性

         * **表达式**– 允许您根据一个或多个成功因子属性为 AD 属性写入自定义值。 [有关详细信息，请参阅这篇有关表达式的文章](../app-provisioning/functions-for-customizing-application-data.md)。

      * **源属性**- 成功因子中的用户属性

      * **默认值** – 可选。 如果源属性的值为空，映射将改为写入此值。
            最常见的配置是将此项留空。

      * **目标属性**= 活动目录中的用户属性。

      * **匹配使用此属性的对象**– 是否应使用此映射在成功因子和活动目录之间唯一标识用户。 此值通常设置在成功因子的工作 ID 字段中，该字段通常映射到活动目录中的员工 ID 属性之一。

      * 匹配优先级 – 可设置多个匹配属性****。 当存在匹配时，会按照此字段定义的顺序进行评估。 一旦找到匹配项，就不再继续评估其他匹配属性。

      * **应用此映射**

         * **始终**= 在用户创建和更新操作上应用此映射

         * **仅在创建期间**- 仅在用户创建操作上应用此映射

1. 要保存映射，请单击属性映射部分顶部的 **"保存**"。

属性映射配置完成后，即可[启用并启动用户预配服务](#enable-and-launch-user-provisioning)。

## <a name="enable-and-launch-user-provisioning"></a>启用并启动用户预配

成功因子预配应用配置完成后，可以在 Azure 门户中打开预配服务。

> [!TIP]
> 默认情况下，启用预配服务时，它会为范围中的所有用户启动预配操作。 如果映射出错或存在 Workday 数据问题，则预配作业可能会失败并转入隔离状态。 要避免这种情况，最佳做法是先配置“源对象范围”筛选器并使用少量测试用户来测试属性映射，然后再为所有用户启动完全同步****。 验证确保映射正常工作且获得所需结果后，可删除筛选器或逐渐扩大范围以包含更多用户。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。************

2. 单击“保存”。****

3. 此操作将启动初始同步，这可能需要可变小时数，具体取决于成功因子租户中的用户数。 您可以检查进度条以跟踪同步周期的进度。 

4. 无论何时，检查 Azure 门户中的“审核日志”选项卡都可以查看预配服务执行的操作。**** 审核日志列出预配服务执行的所有同步事件，例如，正在从 Workday 中读出哪些用户，随后将其添加或更新到 Active Directory。 

5. 完成初始同步后，系统会在“预配”选项卡中写入一份审核摘要报告，如下所示****。

   > [!div class="mx-imgBorder"]
   > ![预配进度条](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>后续步骤

* [了解有关支持用于入站预配的成功因素属性](../app-provisioning/sap-successfactors-attribute-reference.md)
* [了解如何将电子邮件回写配置到成功因素](sap-successfactors-writeback-tutorial.md)
* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何在成功因子和 Azure 活动目录之间配置单一登录](successfactors-tutorial.md)
* [了解如何将其他 SaaS 应用程序与 Azure Active Directory 进行集成](tutorial-list.md)
* [了解如何导出和导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)


