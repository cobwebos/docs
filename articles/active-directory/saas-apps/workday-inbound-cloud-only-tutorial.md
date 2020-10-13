---
title: 教程：在 Azure Active Directory 中配置 Workday 入站预配Microsoft Docs
description: 了解如何配置从 Workday 到 Azure AD 的入站预配
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 7d47c21da1279271b12933a2e4642abcce622600
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90015477"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>教程：配置 Workday 以 Azure AD 用户预配
本教程的目的是说明将工作人员数据从 Workday 预配到 Azure Active Directory 需要执行的步骤。 

>[!NOTE]
>如果要从 Workday 预配的用户是不需要本地 AD 帐户的仅限云的用户，请使用本教程。 如果用户仅需要本地 AD 帐户或同时需要 AD 和 Azure AD 帐户，请参阅 [配置 Workday 以 Active Directory](workday-inbound-tutorial.md) 用户预配教程。 

## <a name="overview"></a>概述

[Azure Active Directory 用户预配服务](../app-provisioning/user-provisioning.md)与 [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 集成，以便能够预配用户帐户。 Azure AD 用户预配服务支持的 Workday 用户预配工作流可将以下人力资源和标识生命周期管理方案自动化：

* **招聘新员工** -将新员工添加到 Workday 时，会自动在 Azure Active Directory 中创建一个用户帐户，并选择性地 Microsoft 365 和 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)，并将电子邮件地址写回到 Workday。

* **员工属性和配置文件更新** -在 Workday (（例如，其名称、标题或经理) 中更新雇员记录时，将自动更新其用户帐户 Azure Active Directory，并选择性地 Microsoft 365 和 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

* **员工** 离职-当员工在 Workday 中终止时，将在 Azure Active Directory 中自动禁用其用户帐户，并选择性地 Microsoft 365 和 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

* **员工 rehires** -员工在 Workday 中 rehired 时，可以根据你的首选项) 自动重新激活或重新设置其旧帐户 (，具体取决于你的首选项 Azure Active Directory Microsoft 365 和 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

此 Workday Azure Active Directory 用户预配解决方案非常适合于：

* 需要使用预建的、基于云的解决方案进行 Workday 用户预配的组织

* 需要从 Workday 到 Azure Active Directory 的直接用户预配的组织

* 要求使用从 Workday 获取的数据预配用户的组织

* 使用 Microsoft 365 电子邮件的组织

## <a name="solution-architecture"></a>解决方案体系结构

本部分介绍仅限云的用户的端到端用户预配解决方案体系结构。 有两个相关的流：

* **权威 HR 数据流–从 Workday 到 Azure Active Directory：** 在此流工作人员事件 (例如，新员工、转移、终止) 在 Workday 中第一次发生，然后事件数据流入 Azure Active Directory。 根据事件，可能会导致在 Azure AD 中创建/更新/启用/禁用操作。
* **写回流–从本地 Active Directory 到 Workday：** 帐户创建在 Active Directory 中完成后，会通过 Azure AD Connect 与 Azure AD 同步，并且可以将电子邮件、用户名和电话号码等信息写回到 Workday。

  ![概述](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>端到端用户数据流

1. HR 团队执行工作事务 (Joiners/搬运工/离开者，或 Workday 员工中心) 的新招聘/转让/终止
2. Azure AD 预配服务运行 Workday EC 计划的标识同步，并标识需要处理的更改，以便与本地 Active Directory 同步。
3. Azure AD 预配服务确定更改，并在 Azure AD 中为用户调用创建/更新/启用/禁用操作。
4. 如果配置了 [Workday 写回](workday-writeback-tutorial.md) 应用，则它将从 Azure AD 检索诸如电子邮件、用户名和电话号码之类的属性。 
5. Azure AD 预配服务在 Workday 中设置电子邮件、用户名和电话号码。

## <a name="planning-your-deployment"></a>计划部署

将从 Workday 到 Azure AD 的云 HR 驱动的用户预配配置为涵盖不同方面（例如：

* 确定匹配 ID 
* 属性映射
* 属性转换 
* 范围筛选器

有关这些主题的综合指导原则，请参阅 [云 HR 部署计划](../app-provisioning/plan-cloud-hr-provision.md) 。 

## <a name="configure-integration-system-user-in-workday"></a>在 Workday 中配置集成系统用户

请参阅 [配置集成系统用户](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) 以创建有权检索辅助数据的 Workday 集成系统用户帐户部分。 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>配置从 Workday 到 Azure AD 的用户预配

下面各部分按步骤介绍如何针对仅限云的部署将用户预配从 Workday 配置到 Azure AD。

* [添加 Azure AD 预配连接器应用并与 Workday 建立连接](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [配置 Workday 与 Azure AD 属性映射](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：添加 Azure AD 预配连接器应用并与 Workday 建立连接

**若要为仅限云的用户配置 Workday 到 Azure Active Directory 的预配：**

1. 转到  <https://portal.azure.com> 。

2. 在 Azure 门户中，搜索并选择“Azure Active Directory”。

3. 依次选择“企业应用程序”、“所有应用程序”。 

4. 依次选择“添加应用程序”、“所有”类别。 

5. 搜索 **Workday 以 Azure AD 用户预配**，并从库中添加该应用。

6. 添加应用并显示应用详细信息屏幕后，请选择“预配”。

7. 将“预配模式”更改为“自动”  。

8. 按如下所述完成“管理员凭据”部分：

   * **Workday 用户名** - 输入 Workday 集成系统帐户的用户名并附加租户域名。 此值应类似于：username@contoso4

   * **Workday 密码 -** 输入 Workday 集成系统帐户的密码

   * **Workday Web 服务 API URL –** 输入租户的 Workday web 服务终结点的 URL。 URL 确定连接器使用的 Workday Web Services API 的版本。 
   
     | URL 格式 | 使用的 WWS API 版本 | 需要 XPATH 更改 |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21。1 | 否 |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21。1 | 否 |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #。# | 是 |

      > [!NOTE]
     > 如果 URL 中未指定任何版本信息，则应用将使用 Workday Web 服务 (WWS) v 21.1，而不需要对应用附带的默认 XPATH API 表达式进行任何更改。 若要使用特定的 WWS API 版本，请在 URL 中指定版本号 <br>
     > 示例：`https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> 如果你使用的是 WWS API v 30.0 +，则在启用预配作业之前， **请在 "** **属性映射-> 高级选项-> 编辑用于 Workday 的编辑属性列表** " 中引用 [管理配置](workday-inbound-tutorial.md#managing-your-configuration) 和 [Workday 属性引用](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)部分。  

   * **通知电子邮件 -** 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。

   * 单击“测试连接”按钮。

   * 如果连接测试成功，请单击顶部的“保存”按钮。 如果测试失败，请仔细检查 Workday 中的 Workday URL 和凭据是否有效。

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>第 2 部分：配置 Workday 与 Azure AD 属性映射

在本部分配置仅限云的用户的用户数据如何从 Workday 流入 Azure Active Directory。

1. 在“预配”选项卡中的“映射”下面，单击“将工作人员同步到 Azure AD”。 

2. 在“源对象范围”字段中，可通过定义一组基于属性的筛选器，选择 Workday 中要预配到 Azure AD 的用户集范围。 默认范围是“Workday 中的所有用户”。 示例筛选器：

   * 示例：将范围限定为工作人员 ID 为 1000000 到 2000000 的用户

      * 属性：WorkerID

      * 运算符：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 示例：仅限临时工和非正式员工

      * 属性：ContingentID

      * 运算符：IS NOT NULL

3. 在“目标对象操作”字段中，可全局筛选对 Azure AD 执行的操作。 最常见的操作是“创建”和“更新” 。

4. 在“属性映射”部分中，可以定义 Workday 属性到 Active Directory 属性的各个映射。

5. 单击现有的属性映射可将其更新，单击屏幕底部的“添加新映射”可添加新的映射。 单个属性映射支持以下属性：

   * **映射类型**

      * **直接映射** - 将 Workday 属性的值按原样写入 AD 属性

      * **常量映射** - 将静态常量字符串值写入 AD 属性

      * **表达式** – 可以基于一个或多个 Workday 属性将自定义值写入 AD 属性。 [有关详细信息，请参阅这篇有关表达式的文章](../app-provisioning/functions-for-customizing-application-data.md)。

   * **源属性** – Workday 中的用户属性。 如果你查找的属性不存在，请参阅[自定义 Workday 用户属性列表](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)。

   * **默认值** – 可选。 如果源属性的值为空，映射将改为写入此值。
            最常见的配置是将此项留空。

   * **目标属性** – Azure AD 中的用户属性。

   * **使用此属性匹配对象** - 是否应使用此属性唯一标识 Workday 与 Azure AD 之间的用户。 此值通常是在 Workday 的“工作人员 ID”字段中设置的，它通常映射到 Azure AD 中的“员工 ID”属性（新属性）或扩展属性。

   * 匹配优先级 – 可设置多个匹配属性。 当存在匹配时，会按照此字段定义的顺序进行评估。 一旦找到匹配，就不会进一步评估其他匹配属性。

   * **应用此映射**

     * 始终 – 对用户创建和更新操作均应用此映射

     * 仅创建期间 - 仅对用户创建操作应用此映射

6. 若要保存映射，请单击“属性映射”部分顶部的“保存”。


## <a name="enable-and-launch-user-provisioning"></a>启用并启动用户预配

Workday 预配应用配置完成后，可在 Azure 门户中启用预配服务。

> [!TIP]
> 默认情况下，启用预配服务时，它会为范围中的所有用户启动预配操作。 如果映射出错或存在 Workday 数据问题，则预配作业可能会失败并转入隔离状态。 要避免这种情况，最佳做法是先配置“源对象范围”筛选器并使用少量测试用户来测试属性映射，然后再为所有用户启动完全同步。 验证确保映射正常工作且获得所需结果后，可删除筛选器或逐渐扩大范围以包含更多用户。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。  

2. 单击“ **保存**”。

3. 此操作将启动初始同步；该过程会耗时数小时，具体时间取决于 Workday 租户中的用户数。 您可以查看进度栏，以跟踪同步周期的进度。 

4. 无论何时，检查 Azure 门户中的“审核日志”选项卡都可以查看预配服务执行的操作。 审核日志会列出预配服务执行的所有单个同步事件，例如，哪些用户要从 Workday 中注销，然后添加或更新为 Azure Active Directory。 

5. 完成初始同步后，系统会在“预配”选项卡中写入一份审核摘要报告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![设置进度栏](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>后续步骤

* [了解有关支持的 Workday 属性以进行入站预配的详细信息](../app-provisioning/workday-attribute-reference.md)
* [了解如何配置 Workday 写回](workday-writeback-tutorial.md)
* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何在 Workday 和 Azure Active Directory 之间配置单一登录](workday-tutorial.md)
* [了解如何将其他 SaaS 应用程序与 Azure Active Directory 进行集成](tutorial-list.md)
* [了解如何导出和导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)


