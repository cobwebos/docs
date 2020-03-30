---
title: 教程：在 Azure 活动目录中配置成功因素入站预配 |微软文档
description: 了解如何从成功因子配置入站预配
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249680"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>教程：将 SAP 成功因子配置为活动目录用户预配（预览）
本教程的目标是通过可选的电子邮件地址写回成功因子，显示需要执行的步骤，以将用户从成功因素员工中心预配到活动目录 （AD） 和 Azure AD。 此集成处于公共预览版中，支持从 SuccessFactors 员工中心检索[70 多个用户属性](../app-provisioning/sap-successfactors-attribute-reference.md)。

>[!NOTE]
>如果要从 SuccessFactors 预配的用户需要本地 AD 帐户，并且可选需要 Azure AD 帐户，请使用本教程。 如果成功因子的用户只需要 Azure AD 帐户（仅限云的用户），请参阅有关[将 SAP 成功因子配置为 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)用户预配的教程。 


## <a name="overview"></a>概述

[Azure 活动目录用户预配服务](../app-provisioning/user-provisioning.md)与[成功因素员工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)集成，以便管理用户的标识生命周期。 

Azure AD 用户预配服务支持的 SuccessFactors 用户预配工作流支持以下人力资源和身份生命周期管理方案的自动化：

* **雇用新员工**- 将新员工添加到成功因子时，将自动在活动目录、Azure 活动目录和 Azure AD 支持的其他 Office 365[和其他 SaaS 应用程序中](../app-provisioning/user-provisioning.md)创建用户帐户，并将电子邮件地址写回到成功因子。

* **员工属性和配置文件更新**- 在成功因子中更新员工记录（如其名称、标题或管理器）时，他们的用户帐户将自动更新在活动目录、Azure 活动目录以及可选的 Office 365 和[Azure AD 支持的其他 SaaS 应用程序中](../app-provisioning/user-provisioning.md)。

* **员工终止**- 当员工在成功因子中终止时，其用户帐户将自动禁用在活动目录、Azure 活动目录中，以及 Azure AD 支持的其他 Office 365[和其他 SaaS 应用程序中](../app-provisioning/user-provisioning.md)。

* **员工重新雇用**- 当员工在 SuccessFactors 中重新雇用时，其旧帐户可以自动重新激活或重新预配（具体取决于您的偏好）到活动目录、Azure 活动目录以及可选的 Office 365 和[Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

此成功因子到活动目录用户预配解决方案非常适合：

* 希望为成功因素用户预配构建的预构建基于云的解决方案的组织

* 需要从成功因子直接用户预配到活动目录的组织

* 需要使用从[成功因素员工中心 （EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)获得的数据预配用户的组织

* 需要加入、移动和离开用户才能仅根据[成功因素员工中心 （EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)中检测到的更改信息将用户同步到一个或多个活动目录林、域和 O 的组织

* 使用 Office 365 收发电子邮件的组织

## <a name="solution-architecture"></a>解决方案体系结构

本节介绍适用于常见混合环境的端到端用户预配解决方案体系结构。 有两个相关的流：

* **权威 HR 数据流 – 从成功因素到本地活动目录：** 在此流工作线程事件（如新员工、转移、终止）首先发生在云成功因素员工中心，然后事件数据通过 Azure AD 和预配代理流入本地活动目录。 根据事件的不同，可能会导致在 AD 中创建/更新/启用/禁用操作。
* **电子邮件回写流程 – 从本地活动目录到成功因素：** 在活动目录中完成帐户创建后，将通过 Azure AD 连接同步与 Azure AD 同步，电子邮件属性可以写回成功因子。

  ![概述](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>端到端用户数据流

1. HR 团队在成功因素员工中心执行员工事务（加入者/移动者/离职者或新员工/调动/终止）
2. Azure AD 预配服务运行来自成功因子 EC 的标识的计划同步，并标识需要处理的更改才能与本地活动目录同步。
3. Azure AD 预配服务调用本地 Azure AD 连接预配代理，其中包含 AD 帐户创建/更新/启用/禁用操作的请求有效负载。
4. Azure AD Connect 预配代理使用服务帐户添加/更新 AD 帐户数据。
5. Azure AD 连接同步引擎运行增量同步以在 AD 中提取更新。
6. Active Directory 域服务更新会与 Azure Active Directory 同步。
7. 如果配置了[成功因子回写应用](sap-successfactors-writeback-tutorial.md)，它将基于使用的匹配属性将电子邮件属性写回成功因子。

## <a name="planning-your-deployment"></a>计划部署

将云 HR 驱动的用户配置从成功因子配置到 AD 需要大量规划，涵盖不同方面，例如：
* Azure AD 连接预配代理的设置 
* 要部署的 AD 用户预配应用的成功因子数
* 匹配 ID、属性映射、转换和范围筛选器

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

  >[!NOTE]
  >有关此预配应用检索的属性的完整列表，请参阅[成功因素属性引用](../app-provisioning/sap-successfactors-attribute-reference.md)

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>将用户预配从成功因子配置到活动目录

本节提供从成功因子到集成范围内每个活动目录域的用户帐户预配的步骤。

* [添加预配连接器应用并下载预配代理](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [安装和配置本地预配代理](#part-2-install-and-configure-on-premises-provisioning-agents)
* [配置与成功因子和活动目录的连接](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [配置属性映射](#part-4-configure-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>第 1 部分：添加预配连接器应用并下载预配代理

**要将成功因子配置为活动目录预配：**

1. 转到 <https://portal.azure.com>

2. 在左侧导航栏中选择“Azure Active Directory”。****

3. 依次选择“企业应用程序”、“所有应用程序”。********

4. 依次选择“添加应用程序”、“所有”类别。********

5. 搜索**成功因子到活动目录用户预配**，并从库中添加该应用程序。

6. 添加应用并显示应用详细信息屏幕后，请选择“预配”****

7. 将“预配模式”更改为“自动”**** ********

8. 单击显示的信息横幅以下载预配代理。 
   > [!div class="mx-imgBorder"]
   > ![下载代理](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "下载代理屏幕")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>第 2 部分：安装和配置本地预配代理

要预配到本地活动目录，必须在具有 .NET 4.7.1+ 框架的服务器上安装预配代理，并且对所需的活动目录域具有网络访问权限。

> [!TIP]
> 可以使用[此处](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)提供的说明来检查服务器上的 .NET framework 版本。
> 如果服务器未安装 .NET 4.7.1 或更高版本，可从[此处](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)下载。  

将下载的代理安装程序传输到服务器主机，然后按照以下步骤完成代理配置。

1. 登录到要安装新代理的 Windows 服务器。

1. 启动预配代理安装程序，同意条款，然后单击 **"安装**"按钮。

   ![安装屏幕](./media/workday-inbound-tutorial/pa_install_screen_1.png "安装屏幕")
   
1. 完成安装后，将启动向导并显示“连接 Azure AD”屏幕****。 单击“身份验证”按钮以连接到 Azure AD 实例****。

   ![连接 Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "连接 Azure AD")
   
1. 使用全局管理员凭据对 Azure AD 实例进行身份验证。

   ![管理员 Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "管理员 Auth")

   > [!NOTE]
   > Azure AD 管理员凭据仅用于连接到 Azure AD 租户。 代理不会将凭据存储在本地服务器上。

1. 使用 Azure AD 成功进行身份验证后，将显示“连接 Active Directory 域服务”屏幕****。 在此步骤中，请输入 AD 域名并单击“添加目录”按钮****。

   ![添加目录](./media/workday-inbound-tutorial/pa_install_screen_4.png "添加目录")
  
1. 系统现将提示输入连接到 AD 域所需的凭据。 在同一屏幕上，可以使用“选择域控制器优先级”以指定代理应用于发送预配请求的域控制器****。

   ![域凭据](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. 配置域之后，安装程序会显示已配置的域的列表。 在此屏幕上，可以重复步骤 #5 和 #6 以添加更多域，或单击“下一步”以继续执行代理注册****。

   ![已配置的域](./media/workday-inbound-tutorial/pa_install_screen_6.png "已配置的域")

   > [!NOTE]
   > 如果有多个 AD 域（例如 na.contoso.com、emea.contoso.com），请将每个域单独添加到列表中。
   > 仅添加父域（如 contoso.com）是不够的。 必须向代理注册每个子域。
   
1. 查看配置详细信息，然后单击“确认”以注册该代理****。
  
   ![确认屏幕](./media/workday-inbound-tutorial/pa_install_screen_7.png "确认屏幕")
   
1. 配置向导将显示代理注册的进度。
  
   ![代理注册](./media/workday-inbound-tutorial/pa_install_screen_8.png "代理注册")
   
1. 代理注册成功后，可以单击“退出”以退出向导****。
  
   ![退出屏幕](./media/workday-inbound-tutorial/pa_install_screen_9.png "退出屏幕")
   
1. 请打开“服务”管理单元来验证是否已安装代理并确保其正在运行，并查找名为“Microsoft Azure AD Connect 预配代理”的服务
  
   ![Services](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>第 3 部分：在预配应用中，配置与成功因子和活动目录的连接
在此步骤中，我们在 Azure 门户中建立与成功因子和活动目录的连接。 

1. 在 Azure 门户中，返回第[1 部分](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)中创建的"成功因素"到活动目录用户预配应用

1. 按如下所述完成“管理员凭据”部分：****

   * **管理员用户名**– 输入成功因素 API 用户帐户的用户名，并附上公司 ID。 它有格式：**\@用户名公司ID**

   * **管理员密码 |** 输入成功因素 API 用户帐户的密码。 

   * **租户 URL |** 输入成功因素 OData API 服务终结点的名称。 仅输入没有 http 或 https 的服务器主机名。 此值应如下所示 **：<api 服务器名称>.成功因素.com**。

   * **Active Directory 林 -** 向代理注册时使用的 Active Directory 域的“名称”。 使用下拉列表选择用于预配的目标域。 此值通常为如下所示的字符串：contoso.com**

   * Active Directory 容器 - 输入默认情况下代理应在其中创建用户帐户的容器 DN****。
        示例 *：OU=用户，DC=contoso，DC_com*
        > [!NOTE]
        > 如果未在属性映射中配置 parentDistinguishedName 属性，则此设置仅对用户帐户创建起作用**。 此设置不用于用户搜索或更新操作。 整个域子树属于搜索操作的范围。

   * **通知电子邮件 |** 输入您的电子邮件地址，并选中"如果发生故障发送电子邮件"复选框。
    > [!NOTE]
    > 如果预配作业进入[隔离](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)状态，Azure AD 预配服务将发送电子邮件通知。

   * 单击“测试连接”按钮。**** 如果连接测试成功，请单击顶部的 **"保存**"按钮。 如果失败，请仔细检查成功因素凭据和代理设置上配置的 AD 凭据是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 门户](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * 成功保存凭据后，"**映射"** 部分将显示默认映射**将成功因子用户同步到内部活动目录**

### <a name="part-4-configure-attribute-mappings"></a>第 4 部分：配置属性映射

在本节中，您将配置用户数据如何从成功因子流向活动目录。

1. 在 **"映射**"下的预配选项卡上，单击 **"将成功因子用户同步到本地活动目录**"。

1. 在 **"源对象范围"** 字段中，可以通过定义一组基于属性的筛选器，选择 SuccessFactors 中哪些用户组应位于预配 AD 的范围内。 默认范围为"成功因子中的所有用户"。 示例筛选器：

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
   > 预配引擎的默认行为是禁用/删除超出范围的用户。 这在您的成功因素到 AD 集成中可能并不可取。 要重写此默认行为，请参阅跳过[删除超出范围的用户帐户](../app-provisioning/skip-out-of-scope-deletions.md)的文章
  
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
> 默认情况下，启用预配服务时，它会为范围中的所有用户启动预配操作。 如果映射或成功因素数据问题有错误，则预配作业可能会失败并进入隔离状态。 要避免这种情况，最佳做法是先配置“源对象范围”筛选器并使用少量测试用户来测试属性映射，然后再为所有用户启动完全同步****。 验证确保映射正常工作且获得所需结果后，可删除筛选器或逐渐扩大范围以包含更多用户。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。************

2. 单击“保存”。****

3. 此操作将启动初始同步，这可能需要可变小时数，具体取决于成功因子租户中的用户数。 您可以检查进度条以跟踪同步周期的进度。 

4. 无论何时，检查 Azure 门户中的“审核日志”选项卡都可以查看预配服务执行的操作。**** 审核日志列出了预配服务执行的所有单个同步事件，例如哪些用户从成功因子中读取，然后随后添加或更新到活动目录。 

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
