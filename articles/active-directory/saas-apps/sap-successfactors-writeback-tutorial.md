---
title: 教程：在 Azure Active Directory 中配置 SAP SuccessFactors 写回Microsoft Docs
description: 了解如何从 Azure AD 配置属性回写到 SAP SuccessFactors
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: bbd274f6b039ef4492068d939c755ab279c2830a
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92069972"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>教程：配置从 Azure AD 到 SAP SuccessFactors 的属性回写
本教程的目的是介绍将 Azure AD 的属性写回到 SAP SuccessFactors Employee Central 的步骤。 

## <a name="overview"></a>概述

你可以配置 SAP SuccessFactors 写回应用程序，以将 Azure Active Directory 中的特定属性写入 SAP SuccessFactors Employee Central。 SuccessFactors 写回预配应用支持将值分配给以下员工中心属性：

* 工作电子邮件
* 用户名
* 办公电话号码 (包括国家/地区代码、区号、数字和分机) 
* 业务电话号码主要标志
* 手机号码 (包括国家/地区代码、区号、数字) 
* 手机主标志 
* User custom01-custom15 属性
* loginMethod 特性

> [!NOTE]
> 此应用对 SuccessFactors 入站用户预配集成应用没有任何依赖关系。 你可以将其配置为独立于 [SuccessFactors 到本地 AD](sap-successfactors-inbound-provisioning-tutorial.md) 预配应用或 [SuccessFactors，以 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 预配应用。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

此 SuccessFactors 写回用户预配解决方案非常适合用于：

* 使用需要写回权威属性的 Microsoft 365 组织 (例如电子邮件地址、电话、用户名) 返回到 SuccessFactors Employee Central。

## <a name="configuring-successfactors-for-the-integration"></a>为集成配置 SuccessFactors

所有 SuccessFactors 预配连接器都需要具有相应权限的 SuccessFactors 帐户的凭据，才能调用 Employee Central OData Api。 本部分介绍在 SuccessFactors 中创建服务帐户并授予适当权限的步骤。 

* [在 SuccessFactors 中创建/识别 API 用户帐户](#createidentify-api-user-account-in-successfactors)
* [创建 API 权限角色](#create-an-api-permissions-role)
* [为 API 用户创建权限组](#create-a-permission-group-for-the-api-user)
* [向权限组授予权限角色](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在 SuccessFactors 中创建/识别 API 用户帐户
与 SuccessFactors 管理员团队或实施合作伙伴合作，在 SuccessFactors 中创建或标识将用于调用 OData Api 的用户帐户。 在 Azure AD 中配置预配应用时，将需要此帐户的用户名和密码凭据。 

### <a name="create-an-api-permissions-role"></a>创建 API 权限角色

1. 使用有权访问管理中心的用户帐户登录到 SAP SuccessFactors。
1. 搜索 " *管理权限角色*"，然后从搜索结果中选择 " **管理权限角色** "。

   ![管理权限角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. 从 "权限角色" 列表中，单击 " **新建**"。

   > [!div class="mx-imgBorder"]
   > ![创建新的权限角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. 为新的权限角色添加 **角色名称** 和 **描述** 。 名称和描述应表示角色用于 API 使用权限。

   > [!div class="mx-imgBorder"]
   > ![权限角色详细信息](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. 在 "权限设置" 下，单击 " **权限 ...**"，在权限列表中向下滚动，然后单击 " **管理集成工具**"。 选中 " **允许管理员通过基本身份验证访问 ODATA API**" 框。

   > [!div class="mx-imgBorder"]
   > ![管理集成工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. 在同一框中向下滚动，然后选择 " **员工中心 API**"。 添加权限，如下所示，使用 odata api 并使用 ODATA API 进行编辑。 如果计划对 SuccessFactors 方案使用同一帐户，请选择 "编辑" 选项。 

   > [!div class="mx-imgBorder"]
   > ![读取写入权限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. 单击“完成”。 单击 **“保存更改”** 。

### <a name="create-a-permission-group-for-the-api-user"></a>为 API 用户创建权限组

1. 在 SuccessFactors 管理中心，搜索 " *管理权限组*"，然后从搜索结果中选择 " **管理权限组** "。

   > [!div class="mx-imgBorder"]
   > ![管理权限组](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. 从 "管理权限组" 窗口中，单击 " **新建**"。

   > [!div class="mx-imgBorder"]
   > ![添加新组](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. 为新组添加组名称。 组名称应指示组适用于 API 用户。

   > [!div class="mx-imgBorder"]
   > ![权限组名称](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. 将成员添加到组。 例如，你可以从 "人员池" 下拉菜单中选择 " **用户名** "，然后输入将用于集成的 API 帐户的用户名。 

   > [!div class="mx-imgBorder"]
   > ![添加组成员](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. 单击 " **完成** " 以完成创建权限组。

### <a name="grant-permission-role-to-the-permission-group"></a>向权限组授予权限角色

1. 在 SuccessFactors 管理中心，搜索 " *管理权限角色*"，然后从搜索结果中选择 " **管理权限角色** "。
1. 从 " **权限角色" 列表**中，选择为 API 使用权限创建的角色。
1. 在 "将 **此角色授予 ...**" 下，单击 " **添加 ...** " 按钮。
1. 从下拉菜单中选择 " **权限组 ...** "，然后单击 " **选择 ...** " 以打开 "组" 窗口以搜索并选择上面创建的组。 

   > [!div class="mx-imgBorder"]
   > ![添加权限组](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. 查看权限角色授予权限组。 
   > [!div class="mx-imgBorder"]
   > ![权限角色和组详细信息](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. 单击 **“保存更改”** 。

## <a name="preparing-for-successfactors-writeback"></a>准备 SuccessFactors 写回

SuccessFactors 写回预配应用程序使用特定的 *代码* 值在 Employee Central 中设置电子邮件和电话号码。 这些 *代码* 值在属性映射表中设置为常量值，每个 SuccessFactors 实例都是不同的。 本部分提供了捕获这些 *代码* 值的步骤。

   > [!NOTE]
   > 请在 SuccessFactors 管理员完成本部分中的步骤。 

### <a name="identify-email-and-phone-number-picklist-names"></a>确定电子邮件和电话号码选择列表名称 

在 SAP SuccessFactors 中， *选择列表* 是一组可配置的选项，用户可以从中进行选择。 不同类型的电子邮件和电话号码 (例如，企业、个人、其他) 使用选择列表表示。 在此步骤中，我们将标识在你的 SuccessFactors 租户中配置的选择列表来存储电子邮件和电话号码值。 
 
1. 在 SuccessFactors 管理中心，搜索 " *管理业务配置*"。 

   > [!div class="mx-imgBorder"]
   > ![管理业务配置](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. 在 " **HRIS 元素**" 下，选择 " **emailInfo** "，然后单击 "**电子邮件类型**" 字段的*详细信息*。

   > [!div class="mx-imgBorder"]
   > ![获取电子邮件信息](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. 在 **电子邮件类型** 详细信息页上，记下与此字段关联的选择列表的名称。 默认情况下，它是 **ecEmailType**。 但你的租户可能会有所不同。 

   > [!div class="mx-imgBorder"]
   > ![确定电子邮件选择列表](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. 在 " **HRIS 元素**" 下，选择 " **phoneInfo** "，然后单击 "**电话类型**" 字段的*详细信息*。

   > [!div class="mx-imgBorder"]
   > ![获取电话信息](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. 在 " **电话类型** 详细信息" 页上，记下与此字段关联的选择列表的名称。 默认情况下，它是 **ecPhoneType**。 但你的租户可能会有所不同。 

   > [!div class="mx-imgBorder"]
   > ![标识电话选择列表](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>检索 emailType 的常量值

1. 在 SuccessFactors 管理中心，搜索并打开 " *选择列表中心*"。 
1. 使用上一节中捕获的电子邮件选择列表的名称 (例如 ecEmailType) 查找电子邮件选择列表。 

   > [!div class="mx-imgBorder"]
   > ![查找电子邮件类型选择列表](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. 打开活动的电子邮件选择列表。 

   > [!div class="mx-imgBorder"]
   > ![打开活动电子邮件类型选择列表](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. 在 "电子邮件类型选择列表" 页上，选择 " *企业* 电子邮件" 类型。

   > [!div class="mx-imgBorder"]
   > ![选择业务电子邮件类型](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. 记下与*业务*电子邮件关联的**选项 ID** 。 这是将在属性映射表中用于 *emailType* 的代码。

   > [!div class="mx-imgBorder"]
   > ![获取电子邮件类型代码](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > 在复制值时删除逗号字符。 例如，如果 **选项 ID** 值为 *8448*，则将 Azure AD 中的 *emailType* 设置为常量 number *8448* (，不) 逗号字符。 

### <a name="retrieve-constant-value-for-phonetype"></a>检索 phoneType 的常量值

1. 在 SuccessFactors 管理中心，搜索并打开 " *选择列表中心*"。 
1. 使用从上一节捕获的电话选择列表的名称查找电话选择列表。 

   > [!div class="mx-imgBorder"]
   > ![查找电话类型选择列表](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. 打开活动的手机选择列表。 

   > [!div class="mx-imgBorder"]
   > ![打开活动手机类型选择列表](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. 在 "电话类型选择列表" 页上，查看 " **选择列表值**" 下列出的不同电话类型。

   > [!div class="mx-imgBorder"]
   > ![查看电话类型](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. 记下与*业务*电话关联的**选项 ID** 。 这是将在属性映射表中用于 *businessPhoneType* 的代码。

   > [!div class="mx-imgBorder"]
   > ![获取业务电话号码](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. 记下*与手机关联*的**选项 ID** 。 这是将在属性映射表中用于 *cellPhoneType* 的代码。

   > [!div class="mx-imgBorder"]
   > ![获取单元格电话号码](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > 在复制值时删除逗号字符。 例如，如果 **选项 ID** 值为 *10606*，则将 Azure AD 中的 *cellPhoneType* 设置为常量 number *10606* (，不) 逗号字符。 


## <a name="configuring-successfactors-writeback-app"></a>配置 SuccessFactors 写回应用

本部分提供 

* [添加预配连接器应用并配置与 SuccessFactors 的连接](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [配置属性映射](#part-2-configure-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第1部分：添加预配连接器应用并配置与 SuccessFactors 的连接

**配置 SuccessFactors 写回：**

1. 转到 <https://portal.azure.com>

2. 在左侧导航栏中选择“Azure Active Directory”。****

3. 依次选择“企业应用程序”、“所有应用程序”。 

4. 依次选择“添加应用程序”、“所有”类别。 

5. 搜索 **SuccessFactors 写回**，并从库中添加该应用。

6. 添加应用并显示 "应用详细信息" 屏幕后，选择 "**预配**"

7. 将**设置****模式**更改为**自动**

8. 按如下所述完成“管理员凭据”部分：

   * **管理员用户名** –输入 SuccessFactors API 用户帐户的用户名，并追加公司 ID。 它采用以下格式： **username \@ companyID**

   * **管理员密码–** 输入 SuccessFactors API 用户帐户的密码。 

   * **租户 URL –** 输入 SuccessFactors OData API 服务终结点的名称。 仅输入不带 http 或 https 的服务器的主机名。 此值应类似于： `api4.successfactors.com` 。

   * **通知电子邮件 -** 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。
    > [!NOTE]
    > 如果预配作业进入[隔离](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)状态，Azure AD 预配服务将发送电子邮件通知。

   * 单击“测试连接”按钮。 如果连接测试成功，请单击顶部的“保存”按钮。 如果失败，请仔细检查 SuccessFactors 凭据和 URL 是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 门户](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 成功保存凭据后，" **映射** " 部分会显示默认映射。 如果属性映射不可见，请刷新页面。  

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：配置属性映射

在本部分中，你将配置用户数据如何从 SuccessFactors 流向 Active Directory。

1. 在 "**映射**" 下的 "设置" 选项卡上，单击 "**设置" Azure Active Directory 用户**

1. 在 " **源对象范围** " 字段中，可以通过定义一组基于属性的筛选器，选择应将 Azure AD 中的哪些用户组视为写回。 默认范围是“Azure AD 中的所有用户”。 
   > [!TIP]
   > 首次配置预配应用时，需要测试和验证属性映射和表达式，以确保它提供所需的结果。 Microsoft 建议使用 " **源对象范围** " 下的范围筛选器，通过 Azure AD 中的一些测试用户来测试映射。 验证确保映射正常工作后，可删除筛选器，也可逐渐扩大范围以包含更多用户。

1. " **目标对象操作** " 字段仅支持 **更新** 操作。

1. 在 " **属性映射** " 部分下的 "映射" 表中，您可以将以下 Azure Active Directory 特性映射到 SuccessFactors。 下表提供了有关如何映射回写属性的指导。 

   | \# | Azure AD 属性 | SuccessFactors 特性 | 备注 |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | 默认情况下，此属性是匹配的标识符。 你可以使用任何其他 Azure AD 属性，而不是使用雇员可在 SuccessFactors 中存储等于 personIdExternal 的值。    |
   | 2 | mail | 电子邮件 | 地图电子邮件属性源。 出于测试目的，可以将 userPrincipalName 映射到电子邮件。 |
   | 3 | 8448 | emailType | 此常数值是与业务电子邮件关联的 SuccessFactors ID 值。 更新此值以与 SuccessFactors 环境匹配。 有关设置此值的步骤，请参阅为 [EmailType 检索常数值](#retrieve-constant-value-for-emailtype) 部分。 |
   | 4 | true | emailIsPrimary | 使用此属性将业务电子邮件设置为 SuccessFactors 中的主电子邮件。 如果业务电子邮件不是主电子邮件，请将此标志设置为 false。 |
   | 5 | userPrincipalName | [custom01 – custom15] | 使用 " **添加新映射**"，可以选择将 userPrincipalName 或任何 Azure AD 属性写入 SuccessFactors 用户对象中提供的自定义属性。  |
   | 6 | 本地-samAccountName | username | 使用 " **添加新映射**"，可以选择将本地 samAccountName 映射到 SuccessFactors username 属性。 |
   | 7 | SSO | loginMethod | 如果为 [部分 SSO](https://apps.support.sap.com/sap/support/knowledge/en/2320766)设置 SuccessFactors 租户，然后使用 "添加新映射"，则可以选择将 loginMethod 设置为常量值 "SSO" 或 "PWD"。 |
   | 8 | telephoneNumber | businessPhoneNumber | 使用此映射可将 *telephoneNumber* 从 Azure AD 流到 SuccessFactors 业务/工作电话号码。 |
   | 9 | 10605 | businessPhoneType | 此常数值是与 business phone 关联的 SuccessFactors ID 值。 更新此值以与 SuccessFactors 环境匹配。 有关设置此值的步骤，请参阅为 [PhoneType 检索常数值](#retrieve-constant-value-for-phonetype) 部分。 |
   | 10 | true | businessPhoneIsPrimary | 使用此属性设置业务电话号码的主要标志。 有效值为 true 或 false。 |
   | 11 | mobile | cellPhoneNumber | 使用此映射可将 *telephoneNumber* 从 Azure AD 流到 SuccessFactors 业务/工作电话号码。 |
   | 12 | 10606 | cellPhoneType | 此常数值是与手机关联的 SuccessFactors ID 值。 更新此值以与 SuccessFactors 环境匹配。 有关设置此值的步骤，请参阅为 [PhoneType 检索常数值](#retrieve-constant-value-for-phonetype) 部分。 |
   | 13 | false | cellPhoneIsPrimary | 使用此属性设置手机号码的主要标志。 有效值为 true 或 false。 |
 
1. 验证并查看属性映射。 
 
    >[!div class="mx-imgBorder"]
    >![写回属性映射](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. 单击 " **保存** " 以保存映射。 接下来，我们将更新 JSON 路径 API 表达式，以便使用 SuccessFactors 实例中的 phoneType 代码。 
1. 选择 " **显示高级选项**"。 

    >[!div class="mx-imgBorder"]
    >![显示高级选项](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. 单击 " **编辑 SuccessFactors 的属性列表**"。 

   > [!NOTE] 
   > 如果 Azure 门户中未显示 " **编辑属性的属性列表** " 选项，请使用 URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* 来访问该页面。 

1. 此视图中的 " **API 表达式** " 列显示连接器使用的 JSON 路径表达式。 
1. 更新业务电话和手机的 JSON 路径表达式，以使用与你的环境相对应 (*businessPhoneType* 和 *CELLPHONETYPE*) 的 ID 值。 

    >[!div class="mx-imgBorder"]
    >![手机 JSON 路径更改](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. 单击 " **保存** " 以保存映射。

## <a name="enable-and-launch-user-provisioning"></a>启用并启动用户预配

SuccessFactors 预配应用配置完成后，即可在 Azure 门户中打开预配服务。

> [!TIP]
> 默认情况下，启用预配服务时，它会为范围中的所有用户启动预配操作。 如果映射或数据问题中存在错误，则设置作业可能会失败并进入隔离状态。 要避免这种情况，最佳做法是先配置“源对象范围”筛选器并使用少量测试用户来测试属性映射，然后再为所有用户启动完全同步。 验证确保映射正常工作且获得所需结果后，可删除筛选器或逐渐扩大范围以包含更多用户。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。  

1. 选择 " **作用域**"。 您可以选择以下选项之一： 
   * **同步所有用户和组**：如果你计划将 Azure AD 中所有用户的已映射属性写回到 SuccessFactors 中，则需要根据在**映射**  ->  **源对象范围**中定义的范围规则来写入此选项。 
   * **仅同步分配的用户和组**：如果你计划仅在 "**应用程序**  ->  **管理**  ->  **用户和组**" 菜单选项中为你分配到此应用程序的用户写回映射属性，请选择此选项。 这些用户还服从在**映射**  ->  **源对象范围**下定义的范围规则。

   > [!div class="mx-imgBorder"]
   > ![选择写回范围](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > SuccessFactors 写回预配应用不支持 "组分配"。 仅支持 "用户分配"。 

1. 单击“保存”  。

1. 此操作将启动初始同步，这可能会花费几小时的时间，具体取决于 Azure AD 租户中的用户数和为操作定义的作用域。 您可以查看进度栏，以跟踪同步周期的进度。 

1. 你可以随时查看 Azure 门户中的 " **配置日志** " 选项卡，以查看预配服务已执行的操作。 预配日志会列出预配服务执行的所有单个同步事件。 

1. 完成初始同步后，系统会在“预配”选项卡中写入一份审核摘要报告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![设置进度栏](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>支持的方案、已知问题和限制

请参阅 SAP SuccessFactors 集成参考指南中的 [写回方案部分](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) 。 

## <a name="next-steps"></a>后续步骤

* [深入了解 Azure AD 和 SAP SuccessFactors 集成参考](../app-provisioning/sap-successfactors-integration-reference.md)
* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何在 SuccessFactors 与 Azure Active Directory 之间配置单一登录](successfactors-tutorial.md)
* [了解如何将其他 SaaS 应用程序与 Azure Active Directory 进行集成](tutorial-list.md)
* [了解如何导出和导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)

