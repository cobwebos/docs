---
title: 教程：在 Azure Active Directory 中配置 SuccessFactors 入站预配Microsoft Docs
description: 了解如何从 SuccessFactors 配置入站预配
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374779"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>教程：配置 SAP SuccessFactors 以 Active Directory 用户预配（预览版）
本教程的目的是说明将用户从 SuccessFactors Employee Central 预配到 Active Directory （AD）和 Azure AD 时需要执行的步骤，并提供对 SuccessFactors 的电子邮件地址的可选写回。 此集成在公共预览版中，支持从 SuccessFactors 员工中心检索70多个以上的[用户属性](../app-provisioning/sap-successfactors-attribute-reference.md)。

>[!NOTE]
>如果要从 SuccessFactors 预配的用户需要本地 AD 帐户，并选择性地使用 Azure AD 帐户，请使用本教程。 如果 SuccessFactors 中的用户只需要 Azure AD 帐户（仅限云的用户），则请参阅[配置 SAP SuccessFactors 以 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)用户预配教程。 


## <a name="overview"></a>概述

[Azure Active Directory 用户预配服务](../app-provisioning/user-provisioning.md)与[SuccessFactors 员工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)集成，以便管理用户的标识生命周期。 

Azure AD 用户预配服务支持的 SuccessFactors 用户预配工作流可实现以下人力资源和标识生命周期管理方案的自动化：

* **招聘新员工**-将新员工添加到 SuccessFactors 时，会自动在 Active Directory、Azure Active Directory 365 和[Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)中创建用户帐户，并将电子邮件地址的回写到 SuccessFactors。

* **员工属性和配置文件更新**-在 SuccessFactors 中更新员工记录（例如，其名称、标题或经理）时，将在 Active Directory、365 Azure Active Directory 以及[Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)中自动更新其用户帐户。

* **员工**离职-当员工在 SuccessFactors 中终止时，会自动在 Active Directory、Azure Active Directory 365 和[Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)中禁用其用户帐户。

* **员工 rehires** -当员工在 SuccessFactors 中 rehired 时，可以根据你的喜好自动重新激活或重新预配其旧帐户（取决于你的偏好） Active Directory、Azure Active Directory 和（可选） Office 365 和[Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

Active Directory 用户预配解决方案的这一 SuccessFactors 非常适合用于：

* 需要为 SuccessFactors 用户预配预先构建的基于云的解决方案的组织

* 需要从 SuccessFactors 到 Active Directory 的直接用户预配的组织

* 要求使用从[SuccessFactors Employee Central （EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)获取的数据设置用户的组织

* 需要加入、移动和离开用户才能同步到一个或多个 Active Directory 林、域和 Ou，且仅基于[SuccessFactors 员工中心（EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)中检测到的更改信息的组织

* 使用 Office 365 收发电子邮件的组织

## <a name="solution-architecture"></a>解决方案体系结构

本节介绍适用于常见混合环境的端到端用户预配解决方案体系结构。 有两个相关的流：

* **权威 HR 数据流–从 SuccessFactors 到本地 Active Directory：** 在此流工作人员事件（例如新员工、转移、终止）中，第一次发生于 cloud SuccessFactors Employee Central，然后事件数据通过 Azure AD 和预配代理流入本地 Active Directory。 根据事件的不同，可能会导致在 AD 中创建/更新/启用/禁用操作。
* **电子邮件写回流–从本地 Active Directory 到 SuccessFactors：** 帐户创建在 Active Directory 中完成后 Azure AD，会通过 Azure AD Connect sync 同步，并将电子邮件属性写回 SuccessFactors。

  ![概述](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>端到端用户数据流

1. HR 团队在 SuccessFactors 员工中心执行工作人员交易（Joiners/搬运工/离开者或新聘用/转让/终止）
2. Azure AD 预配服务运行从 SuccessFactors EC 计划的标识同步，并识别需要处理的更改，以便与本地 Active Directory 同步。
3. Azure AD 预配服务使用包含 AD 帐户创建/更新/启用/禁用操作的请求负载调用本地 Azure AD Connect 预配代理。
4. Azure AD Connect 预配代理使用服务帐户添加/更新 AD 帐户数据。
5. Azure AD Connect 同步引擎运行增量同步以获取 AD 中的更新。
6. Active Directory 域服务更新会与 Azure Active Directory 同步。
7. 如果已配置[SuccessFactors 写回应用程序](sap-successfactors-writeback-tutorial.md)，则它会根据所使用的匹配属性将回发电子邮件属性写入 SuccessFactors。

## <a name="planning-your-deployment"></a>计划部署

将云 HR 驱动型用户预配从 SuccessFactors 配置为 AD 需要进行大量的规划，涵盖不同方面：
* 设置 Azure AD Connect 预配代理 
* 要部署的 AD 用户预配应用的 SuccessFactors 数
* 匹配 ID、属性映射、转换和范围筛选器

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

  >[!NOTE]
  >有关此预配应用检索到的属性的完整列表，请参阅[SuccessFactors 特性引用](../app-provisioning/sap-successfactors-attribute-reference.md)

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>配置从 SuccessFactors 到 Active Directory 的用户预配

本部分提供将用户帐户从 SuccessFactors 预配到集成范围内的每个 Active Directory 域的步骤。

* [添加预配连接器应用并下载预配代理](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [安装和配置本地预配代理](#part-2-install-and-configure-on-premises-provisioning-agents)
* [配置与 SuccessFactors 和 Active Directory 的连接](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [配置属性映射](#part-4-configure-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>第1部分：添加预配连接器应用并下载预配代理

**若要将 SuccessFactors 配置为 Active Directory 预配：**

1. 转到 <https://portal.azure.com>

2. 在左侧导航栏中选择“Azure Active Directory”。

3. 依次选择“企业应用程序”、“所有应用程序”。

4. 依次选择“添加应用程序”、“所有”类别。

5. 搜索**SuccessFactors 以 Active Directory 用户预配**，并从库中添加该应用。

6. 添加应用并显示应用详细信息屏幕后，请选择“预配”

7. 将**设置** **模式**更改为**自动**

8. 单击显示的信息横幅以下载设置代理。 
   > [!div class="mx-imgBorder"]
   > ![下载代理](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "下载代理屏幕")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>第2部分：安装和配置本地设置代理

若要预配到本地 Active Directory，必须在具有 .NET 4.7.1 + Framework 的服务器上安装预配代理，并对所需 Active Directory 域进行网络访问。

> [!TIP]
> 可以使用[此处](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)提供的说明来检查服务器上的 .NET framework 版本。
> 如果服务器未安装 .NET 4.7.1 或更高版本，可从[此处](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)下载。  

将下载的代理安装程序传输到服务器主机并按照以下步骤完成代理配置。

1. 登录到要在其中安装新代理的 Windows Server。

1. 启动设置代理安装程序，同意条款，然后单击 "**安装**" 按钮。

   ![安装屏幕](./media/workday-inbound-tutorial/pa_install_screen_1.png "安装屏幕")
   
1. 完成安装后，将启动向导并显示“连接 Azure AD”屏幕。 单击“身份验证”按钮以连接到 Azure AD 实例。

   ![连接 Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "连接 Azure AD")
   
1. 使用全局管理员凭据对 Azure AD 实例进行身份验证。

   ![管理员身份验证](./media/workday-inbound-tutorial/pa_install_screen_3.png "管理员身份验证")

   > [!NOTE]
   > Azure AD 管理员凭据仅用于连接到 Azure AD 租户。 代理不会将凭据存储在本地服务器上。

1. 使用 Azure AD 成功进行身份验证后，将显示“连接 Active Directory 域服务”屏幕。 在此步骤中，请输入 AD 域名并单击“添加目录”按钮。

   ![添加目录](./media/workday-inbound-tutorial/pa_install_screen_4.png "添加目录")
  
1. 系统现将提示输入连接到 AD 域所需的凭据。 在同一屏幕上，可以使用“选择域控制器优先级”以指定代理应用于发送预配请求的域控制器。

   ![域凭据](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. 配置域之后，安装程序会显示已配置的域的列表。 在此屏幕上，可以重复步骤 #5 和 #6 以添加更多域，或单击“下一步”以继续执行代理注册。

   ![配置的域](./media/workday-inbound-tutorial/pa_install_screen_6.png "配置的域")

   > [!NOTE]
   > 如果有多个 AD 域（例如 na.contoso.com、emea.contoso.com），请将每个域单独添加到列表中。
   > 仅添加父域（如 contoso.com）是不够的。 必须向代理注册每个子域。
   
1. 查看配置详细信息，然后单击“确认”以注册该代理。
  
   ![确认屏幕](./media/workday-inbound-tutorial/pa_install_screen_7.png "确认屏幕")
   
1. 配置向导将显示代理注册的进度。
  
   ![代理注册](./media/workday-inbound-tutorial/pa_install_screen_8.png "代理注册")
   
1. 代理注册成功后，可以单击“退出”以退出向导。
  
   ![退出屏幕](./media/workday-inbound-tutorial/pa_install_screen_9.png "退出屏幕")
   
1. 请打开“服务”管理单元来验证是否已安装代理并确保其正在运行，并查找名为“Microsoft Azure AD Connect 预配代理”的服务
  
   ![服务](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>第3部分：在预配应用程序中，配置与 SuccessFactors 和 Active Directory 的连接
在此步骤中，我们将在 Azure 门户中建立与 SuccessFactors 和 Active Directory 的连接。 

1. 在 Azure 门户中，返回到在[第1部分](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)中创建 Active Directory 用户预配应用程序的 SuccessFactors

1. 按如下所述完成“管理员凭据”部分：

   * **管理员用户名**–输入 SuccessFactors API 用户帐户的用户名，并追加公司 ID。 它采用以下格式： **username\@companyID**

   * **管理员密码–** 输入 SuccessFactors API 用户帐户的密码。 

   * **租户 URL –** 输入 SuccessFactors OData API 服务终结点的名称。 仅输入不带 http 或 https 的服务器的主机名。 此值应如下所示： **< api-successfactors >** 。

   * **Active Directory 林 -** 向代理注册时使用的 Active Directory 域的“名称”。 使用下拉列表选择用于预配的目标域。 此值通常为如下所示的字符串：contoso.com

   * Active Directory 容器 - 输入默认情况下代理应在其中创建用户帐户的容器 DN。
        示例： *OU = Users，dc = contoso，dc = com*
        > [!NOTE]
        > 如果未在属性映射中配置 parentDistinguishedName 属性，则此设置仅对用户帐户创建起作用。 此设置不用于用户搜索或更新操作。 整个域子树属于搜索操作的范围。

   * **通知电子邮件 -** 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。
    > [!NOTE]
    > 如果预配作业进入[隔离](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)状态，Azure AD 预配服务将发送电子邮件通知。

   * 单击“测试连接”按钮。 如果连接测试成功，请单击顶部的“保存”按钮。 如果该操作失败，请仔细检查在代理设置上配置的 SuccessFactors 凭据和 AD 凭据是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 门户](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * 成功保存凭据后，"**映射**" 部分会显示 "将**SuccessFactors 用户同步到本地**" 默认映射 Active Directory

### <a name="part-4-configure-attribute-mappings"></a>第4部分：配置属性映射

在本部分中，你将配置用户数据如何从 SuccessFactors 流向 Active Directory。

1. 在 "**映射**" 下的 "设置" 选项卡上，单击 "**将 SuccessFactors 用户同步到本地 Active Directory**。

1. 在 "**源对象范围**" 字段中，可以通过定义一组基于属性的筛选器，选择要在 SuccessFactors 中设置为 AD 的用户的范围。 默认作用域为 "SuccessFactors 中的所有用户"。 示例筛选器：

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
   > 设置引擎的默认行为是禁用/删除超出作用域的用户。 这可能不是你 SuccessFactors 的 AD 集成所需的。 若要替代此默认行为，请参阅[跳过删除超出范围的用户帐户的](../app-provisioning/skip-out-of-scope-deletions.md)文章
  
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
> 默认情况下，启用预配服务时，它会为范围中的所有用户启动预配操作。 如果映射或 SuccessFactors 数据出现错误，则设置作业可能会失败并进入隔离状态。 要避免这种情况，最佳做法是先配置“源对象范围”筛选器并使用少量测试用户来测试属性映射，然后再为所有用户启动完全同步。 验证确保映射正常工作且获得所需结果后，可删除筛选器或逐渐扩大范围以包含更多用户。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。

2. 单击 **“保存”** 。

3. 此操作将启动初始同步，这可能会花费几小时的时间，具体取决于 SuccessFactors 租户中的用户数量。 您可以查看进度栏，以跟踪同步周期的进度。 

4. 无论何时，检查 Azure 门户中的“审核日志”选项卡都可以查看预配服务执行的操作。 审核日志会列出预配服务执行的所有单个同步事件，例如从 SuccessFactors 读取哪些用户，然后添加或更新为 Active Directory。 

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
