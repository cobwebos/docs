---
title: 教程：使用 Azure Active Directory 为 Workday 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Workday 和取消其预配。
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 0a025ad7857594b3117b1703a0e19ae47407d0fd
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90018095"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>教程：针对自动用户预配来配置 Workday

本教程的目的是演示将辅助角色配置文件从 Workday 预配到本地 Active Directory (AD) 需要执行的步骤。

>[!NOTE]
>如果要从 Workday 预配的用户需要本地 AD 帐户和 Azure AD 帐户，请使用本教程。 
>* 如果 Workday 用户仅需要 Azure AD 帐户 (仅限云的用户) ，则请参阅 [配置 Workday 以 Azure AD](workday-inbound-cloud-only-tutorial.md) 用户预配教程。 
>* 若要配置从 Azure AD 到 Workday 的属性的写回（如电子邮件地址、用户名和电话号码），请参阅 [配置 workday 写回](workday-writeback-tutorial.md)的教程。


## <a name="overview"></a>概述

[Azure Active Directory 用户预配服务](../app-provisioning/user-provisioning.md)与 [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 集成，以便能够预配用户帐户。 Azure AD 用户预配服务支持的 Workday 用户预配工作流可将以下人力资源和标识生命周期管理方案自动化：

* **招聘新员工** -将新员工添加到 Workday 时，会自动在 Active Directory、Azure Active Directory 中创建用户帐户，并选择性地 Microsoft 365 和 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)中创建用户帐户，并将 IT 托管的联系人信息写回到 Workday。

* **员工属性和配置文件更新** -在 Workday (（例如，其名称、标题或经理) ）中更新雇员记录时，会自动在 Active Directory、Azure Active Directory 中更新其用户帐户，并选择性地更新 Microsoft 365 [支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

* **员工** 离职-当员工在 Workday 中终止时，他们的用户帐户会自动禁用 Active Directory、Azure Active Directory，并选择性地 Microsoft 365 和 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

* **员工 rehires** -当员工在 Workday 中 rehired 时，可以根据你的首选项) 自动重新激活或重新设置其旧帐户 (，具体取决于你的首选项 Active Directory、Azure Active Directory 和 [Microsoft 365 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

### <a name="whats-new"></a>新增功能
本部分捕获最近的 Workday 集成增强功能。 有关综合更新、计划的更改和存档的列表，请访问页面 [Azure Active Directory 中的新增功能？](../fundamentals/whats-new.md) 

* **5 月 2020-将电话号码写到 Workday 的能力：** 除了电子邮件和用户名外，现在可以将工作电话号码和移动电话号码从 Azure AD 写回到 Workday。 有关更多详细信息，请参阅 [写回应用教程](workday-writeback-tutorial.md)。

* **2020 年4月-支持 (WWS) API 的最新版本的 Workday Web Services：** 2001年3月和9月两年两次，Workday 提供丰富的功能更新，可帮助你满足业务目标和不断变化的劳动力需求。 为了跟上 Workday 提供的新功能，你现在可以直接指定你希望在连接 URL 中使用的 WWS API 版本。 有关如何指定 Workday API 版本的详细信息，请参阅 [配置 workday 连接](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)一节。 

* **Jan 2020-设置 AD accountExpires 属性的功能：** 现在可以使用函数 [NumFromDate](../app-provisioning/functions-for-customizing-application-data.md#numfromdate) 映射 Workday 日期字段，例如 *EndContractDate* 或 *StatusTerminationDate*。 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

此 Workday 用户预配解决方案非常适合以下对象：

* 需要使用预建的、基于云的解决方案进行 Workday 用户预配的组织

* 需要将用户预配从 Workday 定向到 Active Directory 或 Azure Active Directory 的组织

* 要求使用从 Workday HCM 模块获取的数据预配用户的组织（请参阅 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)）

* 要求只根据 Workday HCM 模块中检测到的更改信息，加入、移动用户或者使用户保持同步到一个或多个 Active Directory 林、域和 OU 的组织（请参阅 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)）

* 使用 Microsoft 365 电子邮件的组织

## <a name="solution-architecture"></a>解决方案体系结构

本节介绍适用于常见混合环境的端到端用户预配解决方案体系结构。 有两个相关的流：

* **权威 HR 数据流–从 Workday 到本地 Active Directory：** 在此流工作人员事件 (例如，新员工、转移、终止) 首先出现在 cloud Workday HR 租户中，然后通过 Azure AD 和预配代理将事件数据流入本地 Active Directory。 根据事件的不同，可能会导致在 AD 中创建/更新/启用/禁用操作。
* **写回流–从本地 Active Directory 到 Workday：** 帐户创建在 Active Directory 中完成后，会通过 Azure AD Connect 与 Azure AD 同步，并且可以将电子邮件、用户名和电话号码等信息写回到 Workday。

![概述](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>端到端用户数据流

1. HR 团队在 Workday HCM 中执行工作人员事务（入职者/换岗者/离职者或新雇员/换岗/离职）
2. Azure AD 预配服务运行来自 Workday HR 的标识的计划同步，并确定需要进行处理以供与本地 Active Directory 域服务同步的更改。
3. Azure AD 预配服务使用包含 Azure AD 帐户创建/更新/启用/禁用操作的请求有效负载调用本地 Azure AD Connect 预配代理。
4. Azure AD Connect 预配代理使用服务帐户添加/更新 AD 帐户数据。
5. Azure AD Connect / AD Sync 引擎运行增量同步以获取 AD 中的更新。
6. Active Directory 域服务更新会与 Azure Active Directory 同步。
7. 如果配置了 [Workday 写回](workday-writeback-tutorial.md) 应用，则会将电子邮件、用户名和电话号码等属性写入 Workday。

## <a name="planning-your-deployment"></a>计划部署

将 Workday 配置为 Active Directory 用户预配需要相当多的规划，涵盖不同方面：
* 设置 Azure AD Connect 预配代理 
* 要部署的 AD 用户预配应用的 Workday 数
* 选择正确的匹配标识符、属性映射、转换和范围筛选器

有关综合指导原则和建议的最佳做法，请参阅 [云 HR 部署计划](../app-provisioning/plan-cloud-hr-provision.md) 。 

## <a name="configure-integration-system-user-in-workday"></a>在 Workday 中配置集成系统用户

所有 Workday 预配连接器的一项常见要求是，需使用 Workday 集成系统用户的凭据连接到 Workday 人力资源 API。 本部分介绍如何在 Workday 中创建集成系统用户，它包含以下部分：

* [创建集成系统用户](#creating-an-integration-system-user)
* [创建集成安全组](#creating-an-integration-security-group)
* [配置域安全策略权限](#configuring-domain-security-policy-permissions)
* [配置业务流程安全策略权限](#configuring-business-process-security-policy-permissions)
* [激活安全策略更改](#activating-security-policy-changes)

> [!NOTE]
> 可以绕过此过程并改用 Workday 全局管理员帐户作为系统集成帐户。 在演示中这样做不会有任何问题，但是，对于生产部署，我们不建议这样做。

### <a name="creating-an-integration-system-user"></a>创建集成系统用户

**创建集成系统用户：**

1. 使用管理员帐户登录到 Workday 租户。 在“Workday 应用程序”的搜索框中，输入“创建用户”，然后单击“创建集成系统用户” 。

   >[!div class="mx-imgBorder"] 
   >![创建用户](./media/workday-inbound-tutorial/wd_isu_01.png "创建用户")
2. 通过为新的集成系统用户提供用户名和密码，完成“创建集成系统用户”任务。  
  
   * 使“下次登录时需要新密码”选项处于未选中状态，因为此用户将以编程方式登录。
   * 将“会话超时(分钟)”保留为其默认值 0，这将阻止用户会话过早超时。
   * 选择选项“不允许 UI 会话”，因为它提供了额外的安全层，可防止拥有集成系统密码的用户登录 Workday。

   > [!div class="mx-imgBorder"]
   > ![创建集成系统用户](./media/workday-inbound-tutorial/wd_isu_02.png "创建集成系统用户")

### <a name="creating-an-integration-security-group"></a>创建集成安全组

在此步骤中，你将在 Workday 中创建不受约束或受约束的集成系统安全组，并将在上一步中创建的集成系统用户分配给该组。

**创建安全组：**

1. 在搜索框中输入“创建安全组”，然后单击“创建安全组”。

   > [!div class="mx-imgBorder"]
   > ![创建安全组](./media/workday-inbound-tutorial/wd_isu_03.png "创建安全组")
2. 完成“创建安全组”任务。 

   * Workday 中有两种类型的安全组：
     * **不受约束：** 安全组的所有成员均可访问受该安全组保护的所有数据实例。
     * **受约束：** 所有安全组成员均可对该安全组能访问的部分数据实例（行）进行基于上下文的访问。
   * 请咨询 Workday 集成合作伙伴，从而为集成选择适当的安全组类型。
   * 在知道组类型之后，请从“租户安全组类型”的下拉列表中选择“集成系统安全组(不受约束)”或“集成系统安全组(受约束)”  。

     > [!div class="mx-imgBorder"]
     >![创建安全组](./media/workday-inbound-tutorial/wd_isu_04.png "创建安全组")

3. 安全组创建成功后，将显示可以为安全组分配成员的页面。 将在上一步中新建的集成系统用户添加到此安全组中。 如果要使用受约束的安全组，则还需要选择相应的组织范围。

   >[!div class="mx-imgBorder"]
   >![编辑安全组](./media/workday-inbound-tutorial/wd_isu_05.png "编辑安全组")

### <a name="configuring-domain-security-policy-permissions"></a>配置域安全策略权限

此步骤将向安全组授予员工数据的“域安全性”策略权限。

**配置域安全策略权限：**

1. 在搜索框中输入“域安全配置”，然后单击链接“域安全配置报表” 。  
   >[!div class="mx-imgBorder"]
   >![域安全策略](./media/workday-inbound-tutorial/wd_isu_06.png "域安全策略")  
2. 在“域”文本框中，搜索以下域，并将其逐个添加到筛选器中。  
   * 外部帐户预配
   * 工作人员数据：*工作人员*
   * 工作人员数据：公职人员报表
   * 个人数据：工作联系人信息
   * 工作人员数据：所有职位
   * 工作人员数据：当前人员配备信息
   * 工作人员数据：工作人员个人资料中的职称
   * *Workday 帐户*
   
     >[!div class="mx-imgBorder"]
     >![域安全策略](./media/workday-inbound-tutorial/wd_isu_07.png "域安全策略")  

     >[!div class="mx-imgBorder"]
     >![域安全策略](./media/workday-inbound-tutorial/wd_isu_08.png "域安全策略") 

     单击“确定”。

3. 在显示的报表中，选择“外部帐户预配”旁边显示的省略号 (...)，然后单击菜单选项“域”->“编辑安全策略权限” 
   >[!div class="mx-imgBorder"]
   >![域安全策略](./media/workday-inbound-tutorial/wd_isu_09.png "域安全策略")  

4. 在“编辑域安全策略权限”页面上，向下滚动至“集成权限”部分 。 单击“+”符号，将集成系统组添加到具有“获取”和“放置”集成权限的安全组列表中 。
   >[!div class="mx-imgBorder"]
   >![编辑权限](./media/workday-inbound-tutorial/wd_isu_10.png "编辑权限")  

5. 单击“+”符号，将集成系统组添加到具有“获取”和“放置”集成权限的安全组列表中 。

   >[!div class="mx-imgBorder"]
   >![编辑权限](./media/workday-inbound-tutorial/wd_isu_11.png "编辑权限")  

6. 针对剩余的每个安全策略重复上述步骤 3-5：

   | Operation | 域安全策略 |
   | ---------- | ---------- |
   | “获取”和“放置” | 工作人员数据：公职人员报表 |
   | “获取”和“放置” | 个人数据：工作联系人信息 |
   | 获取 | 工作人员数据：工作节点 |
   | 获取 | 工作人员数据：所有职位 |
   | 获取 | 工作人员数据：当前人员配备信息 |
   | 获取 | 工作人员数据：工作人员个人资料中的职称 |
   | “获取”和“放置” | Workday 帐户 |

### <a name="configuring-business-process-security-policy-permissions"></a>配置业务流程安全策略权限

此步骤将向安全组授予员工数据的“业务流程安全性”策略权限。 

> [!NOTE]
> 仅在设置 Workday 写回应用连接器时，才需要执行此步骤。

**配置业务流程安全策略权限：**

1. 在搜索框中输入“业务流程策略”，然后单击链接“编辑业务流程安全策略”任务 。  

   >[!div class="mx-imgBorder"]
   >![业务流程安全策略](./media/workday-inbound-tutorial/wd_isu_12.png "业务流程安全策略")  

2. 在“业务流程类型”文本框中，搜索“联系人”并选择“工作联系人更改”业务流程，然后单击“确定” 。

   >[!div class="mx-imgBorder"]
   >![业务流程安全策略](./media/workday-inbound-tutorial/wd_isu_13.png "业务流程安全策略")  

3. 在“编辑业务流程安全策略”页面上，滚动到“更改工作联系人信息 (Web 服务)”部分 。
    

4. 选择新的集成系统安全组并将其添加到可以发起 Web 服务请求的安全组列表中。 

   >[!div class="mx-imgBorder"]
   >![业务流程安全策略](./media/workday-inbound-tutorial/wd_isu_15.png "业务流程安全策略")  

5. 单击“完成”。 

### <a name="activating-security-policy-changes"></a>激活安全策略更改

**激活安全策略更改：**

1. 在搜索框中输入“激活”，然后单击链接“激活挂起的安全策略更改”。
   >[!div class="mx-imgBorder"]
   >![激活](./media/workday-inbound-tutorial/wd_isu_16.png "激活")

1. 通过输入用于审核的注释，然后单击“确定”，开始“激活挂起的安全策略更改”任务。
1. 选中“确认”复选框，然后单击“确定”，完成下一屏幕上的任务。

   >[!div class="mx-imgBorder"]
   >![激活挂起的安全性](./media/workday-inbound-tutorial/wd_isu_18.png "激活挂起的安全性")  

## <a name="configure-active-directory-service-account"></a>配置 Active Directory 服务帐户

本节介绍安装和配置 Azure AD Connect 预配代理所需的 AD 服务帐户权限。

### <a name="permissions-required-to-run-the-provisioning-agent-installer"></a>运行预配代理安装程序所需的权限
确定了将承载预配代理的 Windows Server 后，使用本地管理员或域管理员凭据登录到服务器主机。 代理安装过程会创建安全的密钥存储凭据文件，并更新主机服务器上的服务配置文件配置。 这需要在托管代理的服务器上拥有管理员访问权限。 

### <a name="permissions-required-to-configure-the-provisioning-agent-service"></a>配置预配代理服务所需的权限
使用以下步骤设置可用于设置代理操作的服务帐户。 
1.  在 AD 域控制器上，打开 *Active Directory 用户和计算机* "管理单元。 
2.  创建新的域用户 (示例： *provAgentAdmin*)   
3.  右键单击 OU 或域名，然后选择将打开 "*控制委派向导*" 的 "*委派控制*"。 

> [!NOTE] 
> 如果要限制预配代理仅在特定 OU 中创建和读取用户以进行测试，则建议在测试运行期间将控件委托给适当的 OU 级别。

4. 在欢迎屏幕上单击 " **下一步** "。 
5. 在 " **选择用户或组** " 屏幕上，添加在步骤2中创建的域用户。 单击“下一步”。
   >[!div class="mx-imgBorder"]
   >![添加屏幕](./media/workday-inbound-tutorial/delegation-wizard-01.png "添加屏幕")

6. 在 " **要委派的任务** " 屏幕上，选择以下任务： 
   * 创建、删除和管理用户帐户
   * 读取所有用户信息

   >[!div class="mx-imgBorder"]
   >![任务屏幕](./media/workday-inbound-tutorial/delegation-wizard-02.png "任务屏幕")

7. 单击 " **下一步** " 并 **保存** 配置


## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>配置从 Workday 到 Active Directory 的用户预配

此部分按步骤介绍如何将用户帐户从 Workday 预配到集成范围内的每个 Active Directory 域。

* [添加预配连接器应用并下载预配代理](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [安装和配置本地预配代理](#part-2-install-and-configure-on-premises-provisioning-agents)
* [配置与 Workday 和 Active Directory 的连接](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [配置属性映射](#part-4-configure-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>第 1 部分：添加预配连接器应用并下载预配代理

**若要配置 Workday 到 Active Directory 的预配：**

1. 转到  <https://portal.azure.com> 。

2. 在 Azure 门户中，搜索并选择“Azure Active Directory”。

3. 依次选择“企业应用程序”、“所有应用程序”。 

4. 依次选择“添加应用程序”、“所有”类别。 

5. 搜索 **Workday 以 Active Directory 用户预配**，并从库中添加该应用。

6. 添加应用并显示应用详细信息屏幕后，请选择“预配”。

7. 将“预配模式”更改为“自动”  。

8. 单击显示的信息横幅以下载预配代理。 

   >[!div class="mx-imgBorder"]
   >![下载代理](./media/workday-inbound-tutorial/pa-download-agent.png "下载代理屏幕")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>第 2 部分：安装和配置本地预配代理

要预配到本地 Active Directory，必须在具有 .NET 4.7.1 Framework 或更高版本且可通过网络访问所需 Active Directory 域的服务器上安装预配代理。

> [!TIP]
> 可以使用[此处](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)提供的说明来检查服务器上的 .NET framework 版本。
> 如果服务器未安装 .NET 4.7.1 或更高版本，可从[此处](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)下载。  

将下载的代理安装程序传输到服务器主机，并按照以下步骤完成代理配置。

1. 登录到要安装新代理的 Windows Server。

1. 启动预配代理安装程序，同意条款并单击“安装”按钮。

   >[!div class="mx-imgBorder"]
   >![安装屏幕](./media/workday-inbound-tutorial/pa_install_screen_1.png "安装屏幕")
   
1. 完成安装后，将启动向导并显示“连接 Azure AD”屏幕。 单击“身份验证”按钮以连接到 Azure AD 实例。

   >[!div class="mx-imgBorder"]
   >![连接 Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "连接 Azure AD")
   
1. 使用混合标识管理员凭据对 Azure AD 实例进行身份验证。

   >[!div class="mx-imgBorder"]
   >![管理员身份验证](./media/workday-inbound-tutorial/pa_install_screen_3.png "管理员身份验证")

   > [!NOTE]
   > Azure AD 管理员凭据仅用于连接到 Azure AD 租户。 代理不会将凭据存储在本地服务器上。

1. 使用 Azure AD 成功进行身份验证后，将显示“连接 Active Directory 域服务”屏幕。 在此步骤中，请输入 AD 域名并单击“添加目录”按钮。

   >[!div class="mx-imgBorder"]
   >![添加目录](./media/workday-inbound-tutorial/pa_install_screen_4.png "添加目录")
  
1. 系统现将提示输入连接到 AD 域所需的凭据。 在同一屏幕上，可以使用“选择域控制器优先级”以指定代理应用于发送预配请求的域控制器。

   >[!div class="mx-imgBorder"]
   >![域凭据](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. 配置域之后，安装程序会显示已配置的域的列表。 在此屏幕上，可以重复步骤 #5 和 #6 以添加更多域，或单击“下一步”以继续执行代理注册。

   >[!div class="mx-imgBorder"]
   >![配置的域](./media/workday-inbound-tutorial/pa_install_screen_6.png "配置的域")

   > [!NOTE]
   > 如果有多个 AD 域（例如 na.contoso.com、emea.contoso.com），请将每个域单独添加到列表中。
   > 仅添加父域（如 contoso.com）是不够的。 必须向代理注册每个子域。
   
1. 查看配置详细信息，然后单击“确认”以注册该代理。
  
   >[!div class="mx-imgBorder"]
   >![确认屏幕](./media/workday-inbound-tutorial/pa_install_screen_7.png "确认屏幕")
   
1. 配置向导将显示代理注册的进度。
  
   >[!div class="mx-imgBorder"]
   >![代理注册](./media/workday-inbound-tutorial/pa_install_screen_8.png "代理注册")
   
1. 代理注册成功后，可以单击“退出”以退出向导。

   >[!div class="mx-imgBorder"]
   >![退出屏幕](./media/workday-inbound-tutorial/pa_install_screen_9.png "退出屏幕")
   
1. 请打开“服务”管理单元来验证是否已安装代理并确保其正在运行，并查找名为“Microsoft Azure AD Connect 预配代理”的服务

   >[!div class="mx-imgBorder"]
   >![服务中运行的 Microsoft Azure AD 连接预配代理的屏幕截图](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>第 3 部分：在预配应用中，配置与 Workday 和 Active Directory 的连接
在此步骤中，我们将在 Azure 门户中建立与 Workday 和 Active Directory 的连接。 

1. 在 Azure 门户中，返回到在[第 1 部分](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)中创建的从 Workday 到 Active Directory 用户预配应用

1. 按如下所述完成“管理员凭据”部分：

   * **Workday 用户名** - 输入 Workday 集成系统帐户的用户名并附加租户域名。 内容应该如下所示：username\@tenant_name

   * **Workday 密码 -** 输入 Workday 集成系统帐户的密码

   * **Workday Web 服务 API URL –** 输入租户的 Workday web 服务终结点的 URL。 URL 确定连接器使用的 Workday Web Services API 的版本。 
   
     | URL 格式 | 使用的 WWS API 版本 | 需要 XPATH 更改 |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21。1 | 否 |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21。1 | 否 |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #。# | 是 |

      > [!NOTE]
     > 如果 URL 中未指定任何版本信息，则应用将使用 Workday Web 服务 (WWS) v 21.1，而不需要对应用附带的默认 XPATH API 表达式进行任何更改。 若要使用特定的 WWS API 版本，请在 URL 中指定版本号 <br>
     > 示例： `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> 如果你使用的是 WWS API v 30.0 +，则在启用预配作业之前， **请在 "** **属性映射-> 高级选项-> 编辑用于 Workday 的编辑属性列表** " 中引用 [管理配置](#managing-your-configuration) 和 [Workday 属性引用](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)部分。  

   * **Active Directory 林 -** 向代理注册时使用的 Active Directory 域的“名称”。 使用下拉列表选择用于预配的目标域。 此值通常为如下所示的字符串：contoso.com

   * Active Directory 容器 - 输入默认情况下代理应在其中创建用户帐户的容器 DN。
        示例：OU=Standard Users,OU=Users,DC=contoso,DC=test
        
     > [!NOTE]
     > 如果未在属性映射中配置 parentDistinguishedName 属性，则此设置仅对用户帐户创建起作用。 此设置不用于用户搜索或更新操作。 整个域子树属于搜索操作的范围。

   * **通知电子邮件 -** 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。

     > [!NOTE]
     > 如果预配作业进入[隔离](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)状态，Azure AD 预配服务将发送电子邮件通知。

   * 单击“测试连接”按钮。 如果连接测试成功，请单击顶部的“保存”按钮。 如果连接测试失败，请仔细检查代理设置上配置的 Workday 凭据和 AD 凭据是否有效。

     >[!div class="mx-imgBorder"]
     >![Azure 门户](./media/workday-inbound-tutorial/wd_1.png)

   * 凭据成功保存后，“映射”部分或显示默认映射“将 Workday 工作人员同步到本地 Active Directory” 

### <a name="part-4-configure-attribute-mappings"></a>第 4 部分：配置属性映射

在本部分配置用户数据如何从 Workday 流入 Active Directory。

1. 在“预配”选项卡的“映射”下，单击“将 Workday 工作人员同步到本地 Active Directory” 。

1. 在“源对象范围”字段中，可以通过定义一组基于属性的筛选器，选择 Workday 中要预配到 AD 的用户集范围。 默认范围是“Workday 中的所有用户”。 示例筛选器：

   * 示例：将范围限定为工作人员 ID 为 1000000 到 2000000 的用户（不包括 2000000）

      * 属性：WorkerID

      * 运算符：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 示例：仅限员工和非临时工

      * 属性：EmployeeID

      * 运算符：IS NOT NULL

   > [!TIP]
   > 首次配置预配应用时，需要测试和验证属性映射和表达式，以确保它提供所需的结果。 Microsoft 建议使用“源对象范围”下的范围筛选器来测试 Workday 中少量测试用户的映射。 验证确保映射正常工作后，可删除筛选器，也可逐渐扩大范围以包含更多用户。

   > [!CAUTION] 
   > 预配引擎的默认行为是禁用/删除超出范围的用户。 这可能不适合于 Workday 到 AD 集成。 若要替代此默认行为，请参阅[跳过删除超出范围的用户帐户](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. 在“目标对象操作”字段中，可全局筛选要对 Active Directory 执行的操作。 “创建”和“更新”是最常见的操作。 

1. 在“属性映射”部分中，可以定义 Workday 属性到 Active Directory 属性的各个映射。

1. 单击现有的属性映射可将其更新，单击屏幕底部的“添加新映射”可添加新的映射。 单个属性映射支持以下属性：

      * **映射类型**

         * **直接映射** - 将 Workday 属性的值按原样写入 AD 属性

         * **常量映射** - 将静态常量字符串值写入 AD 属性

         * **表达式** – 可以基于一个或多个 Workday 属性将自定义值写入 AD 属性。 [有关详细信息，请参阅这篇有关表达式的文章](../app-provisioning/functions-for-customizing-application-data.md)。

      * **源属性** – Workday 中的用户属性。 如果你查找的属性不存在，请参阅[自定义 Workday 用户属性列表](#customizing-the-list-of-workday-user-attributes)。

      * **默认值** – 可选。 如果源属性的值为空，映射将改为写入此值。
            最常见的配置是将此项留空。

      * **目标属性** - Active Directory 中的用户属性。

      * **使用此属性匹配对象** – 是否应使用此映射唯一标识 Workday 与 Active Directory 之间的用户。 该值通常是在 Workday 的“工作人员 ID”字段中设置的，它通常映射到 Active Directory 中的某个“员工 ID”属性。

      * 匹配优先级 – 可设置多个匹配属性。 当存在匹配时，会按照此字段定义的顺序进行评估。 一旦找到匹配项，就不再继续评估其他匹配属性。

      * **应用此映射**

         * 始终 – 对用户创建和更新操作均应用此映射

         * 仅创建期间 - 仅对用户创建操作应用此映射

1. 要保存映射，请单击“属性-映射”部分顶部的“保存”。
   >[!div class="mx-imgBorder"]
   >![Azure 门户](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>下面是 Workday 与 Active Directory 之间的一些属性映射示例，还显示一些常用的表达式

* 可根据一个或多个 Workday 源属性，使用映射到 parentDistinguishedName 属性的表达式将用户预配到不同的 OU。 以下示例根据用户所在的城市，将用户放入不同的 OU。

* Active Directory 中的 userPrincipalName 属性是使用重复数据删除函数 [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) 生成的，该函数会检查目标 AD 域中是否存在生成的值，且仅在值唯一时才设置它。  

* [此处提供了有关编写表达式的文档](../app-provisioning/functions-for-customizing-application-data.md)。 此部分中包括有关如何删除特殊字符的示例。

| WORKDAY 属性 | ACTIVE DIRECTORY 属性 |  匹配 ID？ | 创建/更新 |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **是** | 仅在创建时写入 |
| **PreferredNameData**    |  cn    |   |   仅在创建时写入 |
| SelectUniqueValue( Join("\@", Join(".",  \[FirstName\], \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))   | userPrincipalName     |     | 仅在创建时写入 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         仅在创建时写入 |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | 创建 + 更新 |
| **名字**   | givenName       |     |    创建 + 更新 |
| **姓氏**   |   sn   |     |  创建 + 更新 |
| **PreferredNameData**  |  displayName |     |   创建 + 更新 |
| **Company**         | company   |     |  创建 + 更新 |
| **SupervisoryOrganization**  | department  |     |  创建 + 更新 |
| **ManagerReference**   | manager  |     |  创建 + 更新 |
| **BusinessTitle**   |  title     |     |  创建 + 更新 | 
| **AddressLineData**    |  streetAddress  |     |   创建 + 更新 |
| **Municipality**   |   l   |     | 创建 + 更新 |
| **CountryReferenceTwoLetter**      |   co |     |   创建 + 更新 |
| **CountryReferenceTwoLetter**    |  c  |     |         创建 + 更新 |
| **CountryRegionReference** |  st     |     | 创建 + 更新 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  创建 + 更新 |
| **PostalCode**  |   postalCode  |     | 创建 + 更新 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 创建 + 更新 |
| **Fax**      | facsimileTelephoneNumber     |     |    创建 + 更新 |
| **Mobile**  |    mobile       |     |       创建 + 更新 |
| **LocalReference** |  preferredLanguage  |     |  创建 + 更新 |                                               
| **Switch (\[ 市政府 \] ，"OU = Default USERS，DC = CONTOSO，DC = com"，"达拉斯"，"OU = 达拉斯，Ou = USERS，dc = CONTOSO，dc = com"，"奥斯汀"，"OU = 奥斯汀，OU = USERS，dc = CONTOSO，dc = com"，"西雅图"，"Ou = 西雅图，Ou = USERS，dc = CONTOSO，dc = com"，"伦敦"，"Ou = 伦敦，Ou = USERS，dc = CONTOSO，dc = com" ) **  | parentDistinguishedName     |     |  创建 + 更新 |

属性映射配置完成后，即可[启用并启动用户预配服务](#enable-and-launch-user-provisioning)。

## <a name="enable-and-launch-user-provisioning"></a>启用并启动用户预配

Workday 预配应用配置完成后，可在 Azure 门户中启用预配服务。

> [!TIP]
> 默认情况下，启用预配服务时，它会为范围中的所有用户启动预配操作。 如果映射出错或存在 Workday 数据问题，则预配作业可能会失败并转入隔离状态。 要避免这种情况，最佳做法是先配置“源对象范围”筛选器并使用少量测试用户来测试属性映射，然后再为所有用户启动完全同步。 验证确保映射正常工作且获得所需结果后，可删除筛选器或逐渐扩大范围以包含更多用户。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。  

2. 单击“ **保存**”。

3. 此操作将启动初始同步；该过程会耗时数小时，具体时间取决于 Workday 租户中的用户数。 

4. 无论何时，检查 Azure 门户中的“审核日志”选项卡都可以查看预配服务执行的操作。 审核日志列出预配服务执行的所有同步事件，例如，正在从 Workday 中读出哪些用户，随后将其添加或更新到 Active Directory。 要了解如何查看审核日志并解决预配错误，请参阅“疑难解答”部分。

5. 完成初始同步后，系统会在“预配”选项卡中写入一份审核摘要报告，如下所示。
   > [!div class="mx-imgBorder"]
   > ![设置进度栏](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)



## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

* **解决方案功能问题**
  * [处理来自 Workday 的新员工时，解决方案如何在 Active Directory 中为新用户帐户设置密码？](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [解决方案是否支持在预配操作完成后发送电子邮件通知？](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [如何管理新员工的密码传递并安全地提供密码重置机制？](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [解决方案是否在 Azure AD 云端或预配代理层中缓存 Workday 用户个人资料？](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [解决方案是否支持将本地 AD 组分配给用户？](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [解决方案使用哪些 Workday API 来查询和更新 Workday 工作人员个人资料？](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [能否使用两个 Azure AD 租户配置 Workday HCM 租户？](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [为何在已部署 Azure AD Connect 的情况下不支持“Workday 到 Azure AD”用户预配应用？](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [如何建议改进功能或请求与 Workday 和 Azure AD 集成相关的新功能？](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **预配代理问题**
  * [预配代理的正式版是什么？](#what-is-the-ga-version-of-the-provisioning-agent)
  * [如何知道我的预配代理的版本？](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft 是否会自动拉取预配代理更新？](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [能否在运行 Azure AD Connect 的同一服务器上安装预配代理？](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [如何将预配代理配置为使用代理服务器来处理出站 HTTP 通信？](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [如何确保预配代理能够与 Azure AD 租户进行通信且防火墙不阻止代理所需的端口？](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [如何取消注册与我的预配代理相关联的域？](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [如何卸载预配代理？](#how-do-i-uninstall-the-provisioning-agent)
  
* **Workday 到 AD 属性映射和配置问题**
  * [如何备份或导出 Workday 预配属性映射和架构的工作副本？](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [我在 Workday 和 Active Directory 中具有自定义属性。我要如何配置解决方案以使用我的自定义属性？](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [能否将用户的照片从 Workday 预配到 Active Directory？](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [如何在用户同意公开使用后使用 Workday 中的手机号码？](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [如何根据用户的部门/国家/地区/城市特性在 AD 中设置显示名的格式并处理区域差异问题？](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [如何使用 SelectUniqueValue 生成 samAccountName 属性的唯一值？](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [如何删除带音调符号的字符并将其转换为普通的英文字母？](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>解决方案功能问题

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>处理来自 Workday 的新员工时，解决方案如何在 Active Directory 中为新用户帐户设置密码？

当本地预配代理收到新建 AD 帐户的请求时，它会自动生成一个复杂的随机密码，旨在满足 AD 服务器定义的密码复杂性要求，并将其设置到用户对象上。 此密码未记录在任何位置。

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>解决方案是否支持在预配操作完成后发送电子邮件通知？

不支持，当前版本不支持在预配操作完成后发送电子邮件通知。

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>如何管理新员工的密码传递并安全地提供密码重置机制？

在新的 AD 帐户预配中，收尾步骤之一是传递分配给用户 AD 帐户的临时密码。 许多企业仍在使用传统方法来将临时密码传递给用户的经理，后者随后将密码移交给新员工/临时工。 此过程存在固有的安全漏洞，可使用 Azure AD 功能实现更好的方法。

在雇佣过程中，HR 团队通常会运行人员背景检查并核验新员工的移动电话号码。 借助 Workday 到 AD 用户预配集成，可基于该项事实进行构建，并在第 1 天就为用户部署自助式密码重置功能。 实现方法是使用 Azure AD Connect 将新员工的“Mobile Number”属性从 Workday 传播到 AD，然后再从 AD 传播到 Azure AD。 Azure AD 中显示“移动电话号码”之后，即可为用户帐户启用[自助式密码重置 (SSPR)](../authentication/howto-sspr-authenticationdata.md)，这样新员工在第 1 天新就可使用已注册且已验证的移动电话号码进行身份验证。

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>解决方案是否在 Azure AD 云端或预配代理层中缓存 Workday 用户个人资料？

没有，该解决方案不维护用户个人资料的缓存。 Azure AD 预配服务只充当数据处理器，它从 Workday 读取数据并写入目标 Active Directory 或 Azure AD。 有关用户隐私和数据保留期的详细信息，请参阅[管理个人数据](#managing-personal-data)部分。

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>解决方案是否支持将本地 AD 组分配给用户？

目前不支持此功能。 建议的解决方法是部署 PowerShell 脚本，该脚本在 Microsoft 图形 API 终结点中[查询审核日志数据](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta)，并使用该数据触发组分配等方案。 可将此 PowerShell 脚本附加到任务计划程序，并将其部署到运行预配代理的框中。  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>解决方案使用哪些 Workday API 来查询和更新 Workday 工作人员个人资料？

该解决方案当前使用以下 Workday API：

* “管理员凭据”部分中使用的“Workday Web 服务 API URL”格式确定 Get_Workers 使用的 API 版本 
  * 如果 URL 格式为 https://\#\#\#\#\.workday\.com/ccx/service/tenantName，则使用 API v21.1。 
  * 如果 URL 格式为 https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources，则使用 API v21.1 
  * 如果 URL 格式为 https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources/v\#\#\.\#，则使用指定的 API 版本。 （例如：如果指定了 v34.0，则会使用它。）  
   
* Workday 电子邮件写回功能使用 Change_Work_Contact_Information (v30.0) 
* Workday 用户名写回功能使用 Update_Workday_Account (v31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>能否使用两个 Azure AD 租户配置 Workday HCM 租户？

可以，支持该项配置。 下面是用于配置此方案的大致步骤：

* 部署预配代理 1 并将其注册到 Azure AD 租户 1。
* 部署预配代理 2 并将其注册到 Azure AD 租户 2。
* 根据每个预配代理要管理的“子域”，使用域来配置每个代理。 一个代理可以处理多个域。
* 在 Azure 门户中，在每个租户中将 Workday 设置为 AD 用户预配应用，并使用各自的域进行配置。

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>为何在已部署 Azure AD Connect 的情况下不支持“Workday 到 Azure AD”用户预配应用？

在混合模式下使用 Azure AD 时（该模式同时包含云用户和本地用户），重点是要明确定义“授权来源”。 通常，混合方案要求部署 Azure AD Connect。 部署 Azure AD Connect 之后，本地 AD 就是授权来源。 如果在混合模式中引入“Workday 到 Azure AD”连接器，则可能导致 Workday 属性值覆盖由 Azure AD Connect 设置的值。 因此，启用 Azure AD Connect 时，不支持使用“Workday 到 Azure AD”预配应用。 在这种情况下，建议使用“Workday 到 AD 用户”预配应用，它用于将用户引入本地 AD，然后再使用 Azure AD Connect 将其同步到 Azure AD。

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>如何建议改进功能或请求与 Workday 和 Azure AD 集成相关的新功能？

你的反馈非常宝贵，它可帮助我们制定未来版本和增强功能的开发方向。 我们乐于收到任何反馈，请在 [Azure AD 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory)提交创意或改进意见。 要了解与 Workday 集成相关的具体反馈，请选择“SaaS 应用程序”类别，再使用关键词“Workday”进行搜索，以查找与 Workday 相关的现有反馈 。

> [!div class="mx-imgBorder"]
> ![UserVoice SaaS 应用](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

在建议新想法时，请查看是否已有人提出了类似功能的建议。 如果是，则你可投票支持该功能或增强功能请求。 你还可对特定用例发表评论，以表明你的支持态度并指出该功能在哪些方面对你有价值。

### <a name="provisioning-agent-questions"></a>预配代理问题

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>预配代理的正式版是什么？

有关预配代理的最新 GA 版本，请参阅 [Azure AD Connect 预配代理：版本发行历史记录](../app-provisioning/provisioning-agent-release-version-history.md) 。  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>如何知道我的预配代理的版本？

* 登录到安装有预配代理的 Windows Server。
* 转到“控制面板” -> “卸载或更改程序”菜单 
* 查找与条目“Microsoft Azure AD Connect 预配代理”相对应的版本

  >[!div class="mx-imgBorder"]
  >![Azure 门户](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft 是否会自动拉取预配代理更新？

是的，如果 Windows 服务 **Microsoft Azure AD 连接代理更新程序** 已启动并正在运行，则 Microsoft 会自动更新设置代理。

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>能否在运行 Azure AD Connect 的同一服务器上安装预配代理？

是，可在运行 Azure AD Connect 的同一服务器上安装预配代理。

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>配置时，预配代理会提示输入 Azure AD 管理员凭据。 代理会将凭据存储在本地服务器上吗？

配置过程中，预配代理会提示输入 Azure AD 管理员凭据，它们仅用于连接到 Azure AD 租户。 它不会将凭据存储在本地服务器上。 但它确实会保留凭据，以用于连接到本地 Windows 密码保管库中的本地 Active Directory 域。

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>如何将预配代理配置为使用代理服务器来处理出站 HTTP 通信？

预配代理支持使用出站代理。 可通过编辑代理配置文件“C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config”对其进行配置。在文件的末尾添加以下行，恰好添加在尾随的 `</configuration>` 标记前面。
将变量 [proxy-server] 和 [proxy-port] 替换为代理服务器名称和端口值。

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>如何确保预配代理能够与 Azure AD 租户进行通信且防火墙不阻止代理所需的端口？

还可以检查是否所有 [必需的端口](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports) 都已打开。

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>能否将一个预配代理配置为预配多个 AD 域？

是，只要代理可感知到各自的域控制器，就可将一个预配代理配置为处理多个 AD 域。 Microsoft 建议设置一个包含 3 个预配代理的组，它提供同一组 AD 域来确保高可用性并提供故障转移支持。

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>如何取消注册与我的预配代理相关联的域？

* 在 Azure 门户中，获取 Azure AD 租户的租户 ID。
* 登录到运行预配代理的 Windows Server。
* 以 Windows 管理员身份打开 PowerShell。
* 切换到包含注册脚本的目录，并运行以下命令，将 \[租户 ID\] 参数替换为租户 ID 的值。

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* 在显示的代理列表中，从其 resourceName 等于你的 AD 域名的资源中复制“`id`”字段的值。
* 将该 ID 值粘贴到此命令中，并在 PowerShell 中执行该命令。

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* 重新运行代理配置向导。
* 需要重新配置之前分配到此域的其他所有代理。

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>如何卸载预配代理？

* 登录到安装有预配代理的 Windows Server。
* 转到“控制面板” -> “卸载或更改程序”菜单 
* 卸载以下程序：
  * Microsoft Azure AD Connect 预配代理
  * Microsoft Azure AD Connect 代理更新程序
  * Microsoft Azure AD Connect 预配代理包

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Workday 到 AD 属性映射和配置问题

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>如何备份或导出 Workday 预配属性映射和架构的工作副本？

可使用 Microsoft Graph API 来导出 Workday 用户预配配置。 有关详细信息，请参考[导出和导入 Workday 用户预配属性映射配置](#exporting-and-importing-your-configuration)部分中的步骤。

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>我在 Workday 和 Active Directory 中具有自定义属性。 我要如何配置解决方案以使用我的自定义属性？

该解决方案支持自定义 Workday 和 Active Directory 属性。 要将自定义属性添加到映射架构，请打开“属性映射”边栏选项卡，再向下滚动以展开“显示高级选项”部分 。 

![编辑属性列表](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

要添加自定义 Workday 属性，请选择“编辑 Workday 属性列表”选项；要添加自定义 AD 属性，请选择“编辑本地 Active Directory 属性列表”选项 。

另请参阅：

* [自定义 Workday 用户属性列表](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>如何将解决方案配置为根据 Workday 更改仅更新 AD 中的属性，而不创建任何新的 AD 帐户？

可通过设置“属性映射”边栏选项卡中的“目标对象操作”来实现此配置，如下所示 ：

![Update 操作](./media/workday-inbound-tutorial/wd_target_update_only.png)

仅针对从 Workday 流向 AD 的更新操作勾选“更新”复选框。 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>能否将用户的照片从 Workday 预配到 Active Directory？

该解决方案目前不支持设置二进制文件属性，例如 Active Directory 中的 thumbnailPhoto 和 jpegPhoto 。

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>如何在用户同意公开使用后使用 Workday 中的手机号码？

* 请转到 Workday 预配应用的“预配”边栏选项卡。
* 单击“属性映射” 
* 在“映射”下，选择“将 Workday 工作人员同步到本地 Active Directory”（或“将 Workday 工作人员同步到 Azure AD”）  。
* 在“属性映射”页面上，向下滚动并选中“显示高级选项”框。  单击“编辑 Workday 属性列表”
* 在打开的边栏选项卡中，找到“Mobile”属性并单击该行，从而编辑 API 表达式![移动 GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* 将 API 表达式替换为下列的新表达式，后者仅当 Workday 中的“公开使用标志”设置为“True”时，才会检索工作手机号码。

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* 保存属性列表。
* 保存属性映射。
* 清除当前状态并重启完全同步。

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>如何根据用户的部门/国家/地区/城市特性在 AD 中设置显示名的格式并处理区域差异问题？

通常要求在 AD 中配置 displayName，这样它才会提供用户所属部门和国家/地区的相关信息。 例如，如果 John Smith 就职于美国市场营销部门，则你希望他的 displayName 显示为“Smith, John (Marketing-US)” 。

下面介绍了如何应对有关构建 CN 或 displayName 来包含公司、业务单位、城市或国家/地区等属性的此类要求 。

* 每个 Workday 属性均通过基础 XPATH API 表达式进行检索，后者可在“属性映射”->“高级部分”->“编辑 Workday 属性列表”中进行配置。 下面是 Workday 的 PreferredFirstName、PreferredLastName、Company 和 SupervisoryOrganization 属性的默认 XPATH API 表达式   。

     | Workday 属性 | API XPath 表达式 |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Company | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   咨询 Workday 团队，确认上述 API 表达式对 Workday 租户配置而言有效。 如有必要，可按[自定义 Workday 用户属性列表](#customizing-the-list-of-workday-user-attributes)部分中所述进行编辑。

* 同样地，使用以下 XPATH 检索 Workday 中的国家/地区信息：wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference

     下面是“Workday 属性列表”部分中提供的 5 个与国家/地区相关的属性。

     | Workday 属性 | API XPath 表达式 |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  咨询 Workday 团队，确认上述 API 表达式对 Workday 租户配置而言有效。 如有必要，可按[自定义 Workday 用户属性列表](#customizing-the-list-of-workday-user-attributes)部分中所述进行编辑。

* 要构建正确的属性映射表达式，请确定哪个 Workday 属性“权威地”表示用户的名字、姓氏、国家/地区和部门。 假设属性分别是 PreferredFirstName、PreferredLastName、CountryReferenceTwoLetter 和 SupervisoryOrganization   。 可使用此方法为 AD 的 displayName 属性构建表达式（如下所示），以获取显示名称，例如“Smith, John (Marketing-US)” 。

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    一旦具有正确的表达式，即可编辑属性映射表并修改 displayName 属性映射，如下所示： ![DisplayName 映射](./media/workday-inbound-tutorial/wd_displayname_map.png)

* 扩充上面的示例，假设你想要将 Workday 中的城市名称转换为速记值，再用它来构建显示名称，例如“Smith, John (CHI)”或“Doe, Jane (NYC)”，然后就可通过采用 Workday“Municipality”属性作为判定变量的 Switch 表达式来检索该结果  。

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    另请参阅：
  * [Switch 函数语法](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Join 函数语法](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Append 函数语法](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>如何使用 SelectUniqueValue 生成 samAccountName 属性的唯一值？

假设要结合使用 Workday 中的 FirstName 和 LastName 属性为 samAccountName 属性生成唯一值  。 下面是最初可使用的表达式：

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

上述表达式的工作原理是：如果用户是 John Smith，它首先尝试生成 JSmith；如果 JSmith 已存在，它将生成 JoSmith；如果这也存在，则它将生成 JohSmith。 该表达式还确保所生成的值满足与 samAccountName 相关的长度限制和特殊字符限制。

另请参阅：

* [Mid 函数语法](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Replace 函数语法](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue 函数语法](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>如何删除带音调符号的字符并将其转换为普通的英文字母？

使用函数 [Normalize Diacritics ](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) 删除用户名字和姓氏中的特殊字符，同时为用户构建电子邮件地址或 CN 值。

## <a name="troubleshooting-tips"></a>故障排除提示

本部分提供具体指导，其中介绍如何使用 Azure AD 审核日志和 Windows Server 事件查看器日志来排查 Workday 集成方面的预配问题。 它基于[教程：针对自动用户帐户预配进行报告](../app-provisioning/check-status-user-account-provisioning.md)中介绍的一般性疑难解答步骤和概念进行编制

本部分涵盖了疑难解答的以下方面：

* [设置 Windows 事件查看器来排查代理方面的问题](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [设置 Azure 门户审核日志来排查服务方面的问题](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [了解 AD 用户帐户创建操作的日志](#understanding-logs-for-ad-user-account-create-operations)
* [了解经理更新操作的日志](#understanding-logs-for-manager-update-operations)
* [解决常见错误](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>设置 Windows 事件查看器来排查代理方面的问题

* 登录到部署有预配代理的 Windows Server 计算机
* 打开“Windows Server 事件查看器”桌面应用。
* 选择“Windows 日志”>“应用程序”。
* 使用“筛选当前日志…” 选项，以查看在源 AAD.Connect.ProvisioningAgent 下记录的所有事件，然后通过如下指定筛选器“-5”来排除事件 ID 为“5”的事件。

  ![Windows 事件查看器](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* 单击“确定”，然后按“日期和时间”列对结果视图进行排序 。

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>设置 Azure 门户审核日志来排查服务方面的问题

* 启动 [Azure 门户](https://portal.azure.com)，并导航到 Workday 预配应用程序的“审核日志”部分。
* 使用“审核日志”页面上的“列”按钮，以仅显示视图中的下述列（日期、活动、状态、状态原因）。 此配置可确保仅着重于与疑难解答相关的数据。

  ![“审核日志”列](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* 使用“目标”和“数据范围”查询参数来筛选视图 。 
  * 将“目标”查询参数设置为 Workday 工作人员对象的“工作人员 ID”或“员工 ID”。
  * 将“日期范围”设置为要调查是否存在预配错误或问题的相应时间段。

  ![审核日志筛选器](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>了解 AD 用户帐户创建操作的日志

当检测到 Workday 中存在新员工时（假设使用员工 ID 21023），Azure AD 预配服务会尝试为该员工创建一个新的 AD 用户帐户，并在此过程中如下创建 4 个审核日志记录，：

  [![审核日志创建操作](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

单击任意审核日志记录时，会打开“活动详细信息”页面。 下面是“活动详细信息”页面上显示的每个日志记录类型的相关内容。

* **Workday 导入**记录：此日志记录显示从 Workday 提取的工作人员信息。 请使用日志记录的“其他详细信息”部分中的信息来排查从 Workday 提取数据时遇到的问题。 下面显示了一个示例记录，还显示了有关如何解释每个字段的指针。

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD 导入**记录：此日志记录显示从 AD 中提取的帐户信息。 由于初始用户创建期间没有 AD 帐户，因此活动状态原因将指示未在 Active Directory 中找到具有 Matching ID 属性值的帐户。 请使用日志记录的“其他详细信息”部分中的信息来排查从 Workday 提取数据时遇到的问题。 下面显示了一个示例记录，还显示了有关如何解释每个字段的指针。

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  要查找与此 AD 导入操作对应的预配代理日志记录，请打开 Windows 事件查看器日志，并使用“查找…” 菜单选项来查找包含 Matching ID/Joining Property 属性值的日志项（在本例中为 21023）。

  ![查找](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  查找“事件ID = 9”的条目，它将提供代理用于检索 AD 帐户的 LDAP 搜索筛选器。 你可验证这是否是适合用于检索唯一用户条目的搜索筛选器。

  ![LDAP 搜索](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  后面紧跟“事件 ID = 2”的记录会捕获搜索操作的结果及返回状态（即是否返回任何结果）。

  ![LDAP 结果](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **同步规则操作**记录：此日志记录显示属性映射规则和已配置的范围筛选器的结果，还显示要用于处理传入 Workday 事件的预配操作。 请使用日志记录的“其他详细信息”部分中的信息来排查同步操作问题。 下面显示了一个示例记录，还显示了有关如何解释每个字段的指针。

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  如果属性映射表达式或传入的 Workday 数据存在问题（例如，所需属性的值为空或为 null），则此阶段将失败，且 ErrorCode 将提供失败详细信息。

* **AD 导出**记录：此日志记录显示 AD 帐户创建操作的结果及在该过程中设置的属性值。 请使用日志记录的“其他详细信息”部分中的信息来排查帐户创建操作问题。 下面显示了一个示例记录，还显示了有关如何解释每个字段的指针。 在“其他详细信息”部分中，“EventName”设置为“EntryExportAdd”，“JoiningProperty”设置为 Matching ID 属性的值，“SourceAnchor”设置为与记录关联的 WorkdayID (WID)，而“TargetAnchor”设置为新建用户的 AD“ObjectGuid”属性的值。 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  要查找与此 AD 导出操作对应的预配代理日志记录，请打开 Windows 事件查看器日志，并使用“查找…” 菜单选项来查找包含 Matching ID/Joining Property 属性值的日志项（在本例中为 21023）。  

  查找与“事件 ID = 2”的导出操作的时间戳对应的 HTTP POST 记录。 此记录将包含预配服务发送到预配代理的属性值。

  [![SCIM 添加](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  紧接在上述事件之后，应该还有一个事件，它捕获“创建 AD 帐户”操作的响应。 此事件返回在 AD 中新创建的 objectGuid，且它在预配服务中设置为 TargetAnchor 属性。

  [![SCIM 添加](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>了解经理更新操作的日志

manager 属性是 AD 中的引用属性。 预配服务不会将 manager 属性设置为用户创建操作的一部分。 而是在为用户创建 AD 帐户之后，将 manager 属性设置为“更新”操作的一部分。 扩充上述示例，假设在 Workday 中激活了员工 ID 为“21451”的新员工，且新员工的经理 (21023) 已拥有 AD 帐户。 在该场景中，搜索用户 21451 的审核日志会显示 5 个条目。

  [![经理更新](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

前 4 条记录与在用户创建操作期间介绍的记录类似。 第 5 条记录是与 manager 属性更新相关的导出。 日志记录显示 AD 帐户经理更新操作的结果，该操作通过经理的 objectGuid 属性执行。

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>解决常见错误

本部分介绍 Workday 用户预配方面的常见错误及其解决方法。 错误按如下分组：

* [预配代理错误](#provisioning-agent-errors)
* [连接错误](#connectivity-errors)
* [AD 用户帐户创建错误](#ad-user-account-creation-errors)
* [AD 用户帐户更新错误](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>预配代理错误

|#|错误场景 |可能的原因|推荐的解决方案|
|--|---|---|---|
|1.| 安装预配代理时出错，显示错误消息：服务“Microsoft Azure AD Connect Provisioning Agent”(AADConnectProvisioningAgent) 启动失败。请确保有足够的特权来启动系统。 | 如果尝试在域控制器上安装预配代理，而组策略阻止服务启动，通常就会显示此错误。  如果正在运行代理的旧版本，并且在启动新的安装项之前未安装代理，则也会看见此消息。| 在非 DC 服务器上安装预配代理。 请务必在安装新代理之前卸载旧版本的代理。|
|2.| Windows 服务“Microsoft Azure AD Connect Provisioning Agent”处于“正在启动”状态，且未切换到“正在运行”状态 。 | 在安装期间，代理向导会在服务器上创建一个本地帐户（NT 服务 \\AADConnectProvisioningAgent），这是用于启动服务的登录帐户。 如果 Windows Server 上的安全策略阻止从本地帐户运行服务，则将遇到此错误。 | 请打开服务控制台。 右键单击 Windows 服务“Microsoft Azure AD Connect 预配代理”，然后在“登录”选项卡中指定域管理员的帐户来运行该服务。 重启服务。 |
|3.| 在“连接 Active Directory”步骤中向 AD 域配置预配代理时，向导需要很长时间才能尝试加载 AD 架构且最终会超时。 | 如果由于防火墙问题导致向导无法连接 AD 域控制器服务器，则通常会显示此错误。 | 在“连接 Active Directory”向导屏幕上，当为 AD 域提供凭据时，有一个名为“选择域控制器优先级”的选项 。 使用此选项可选择与代理服务器位于同一站点的域控制器，并确保防火墙规则均未阻止通信。 |

#### <a name="connectivity-errors"></a>连接错误

如果预配服务无法连接到 Workday 或 Active Directory，则可能导致预配进入隔离状态。 请使用下表来排查连接问题。

|#|错误场景 |可能的原因|推荐的解决方案|
|--|---|---|---|
|1.| 单击“测试连接”时，收到错误消息：连接到 Active Directory 时出错。请确保本地预配代理正在运行且已配置了正确的 Active Directory 域。 | 如果预配代理当前未运行或 Azure AD 与预配代理之间有防火墙阻止通信，则通常会显示此错误。 如果代理向导中未配置域，也可能显示此错误。 | 请打开 Windows 服务器上的服务控制台来确定代理正在运行。 打开预配代理向导并确认向代理注册了正确的域。  |
|2.| 预配作业在周末（周五至周六）进入隔离状态，且我们收到一封电子邮件通知，其中指出同步出错。 | 导致此错误的常见原因之一是所计划的 Workday 停机时间。 如果要使用 Workday 实现租户，则请注意，Workday 安排了在周末（通常是从周五晚上到周六早上）对其实现租户停机，在此期间，Workday 预配应用可能会进入隔离状态，因而无法连接到 Workday。 一旦 Workday 实现租户重新上线，该应用即恢复正常状态。 在极少数情况下，如果因租户刷新而出现集成系统用户密码更改，或者帐户处于锁定或过期状态，则也可能看到此错误。 | 请咨询 Workday 管理员或集成合作伙伴，了解 Workday 何时安排停机时间，从而忽略停机期间出现的警报消息，并在 Workday 实例重新联机后确认其是否可用。  |


#### <a name="ad-user-account-creation-errors"></a>AD 用户帐户创建错误

|#|错误场景 |可能的原因|推荐的解决方案|
|--|---|---|---|
|1.| 审核日志中的导出操作失败，并显示消息*错误:OperationsError-SvcErr:出现操作错误。没有为目录服务配置高级引用。因此，目录服务无法向此林外的对象发出引用。* | 如果未正确设置 Active Directory 容器 OU，或者用于 parentDistinguishedName 的表达式映射存在问题，则通常会显示此错误 。 | 请检查 Active Directory 容器 OU 参数是否有拼写错误。 如果在属性映射中使用 parentDistinguishedName，请确保它始终计算得出 AD 域中的已知容器。 检查审核日志中的导出事件，查看所生成的值。 |
|2.| 审核日志中的导出操作失败，并显示错误代码“SystemForCrossDomainIdentityManagementBadResponse”和消息*错误:ConstraintViolation-AtrErr:请求中的值无效。属性的值不在可接受的值范围内。\n错误详细信息:CONSTRAINT_ATT_TYPE - company*。 | 虽然此错误特定于 company 属性，但 CN 等其他属性也可能出现此错误 。 由于 AD 强制架构约束而出现此错误。 默认情况下，AD 中的属性（例如 company 和 CN）的字符数上限为 64 个字符 。 如果 Workday 中的值超过 64 个字符，则将看到此错误消息。 | 请检查审核日志中的导出事件，查看错误消息中报告的属性的值。 考虑使用 [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) 函数截断来自 Workday 的值，或者将映射更改为不具有类似长度约束的 AD 属性。  |

#### <a name="ad-user-account-update-errors"></a>AD 用户帐户更新错误

在 AD 用户帐户的更新过程中，预配服务会从 Workday 和 AD 读取信息，运行属性映射规则并确定是否需要任何更改。 相应地，会触发更新事件。 如果上述任一步骤失败，都会记录到审核日志中。 请使用下表来排查常见的更新错误。

|#|错误场景 |可能的原因|推荐的解决方案|
|--|---|---|---|
|1.| 审核日志中的同步规则操作失败，并显示消息“EventName = EntrySynchronizationError 且 ErrorCode = EndpointUnavailable”。 | 如果因本地预配代理遇到处理错误而导致预配服务无法从 Active Directory 检索用户个人资料数据，则会显示此错误。 | 请检查预配代理事件查看器日志，查找指示读取操作问题的错误事件（按事件 ID 2 进行筛选）。 |
|2.| AD 中的某些用户未更新 AD 中的 manager 属性。 | 最可能导致此错误的原因是，使用了范围规则且用户的经理不在范围内。 如果未在目标 AD 域中找到经理的匹配 ID 属性（例如 EmployeeID）或者它未设置为正确的值，则也可能遇到此问题。 | 请查看范围筛选器并将经理用户添加到范围中。 检查 AD 中经理的个人资料，以确保匹配的 ID 属性存在相应的值。 |

## <a name="managing-your-configuration"></a>管理配置

此部分介绍如何进一步扩展、自定义和管理由 Workday 驱动的用户预配配置。 它涵盖以下主题：

* [自定义 Workday 用户属性列表](#customizing-the-list-of-workday-user-attributes)  
* [导出和导入配置](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>自定义 Workday 用户属性列表

适用于 Active Directory 和 Azure AD 的 Workday 预配应用都包含一个默认的 Workday 用户属性列表，可以从中进行选择。 但是，这些列表并不详尽。 Workday 支持数百个可能的用户属性，这些属性可能是 Workday 租户中的标准属性或唯一属性。

Azure AD 预配服务支持自定义列表或 Workday 属性，以包含人力资源 API 的 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 操作中公开的所有属性。

要执行此更改，必须使用 [Workday Studio](https://community.workday.com/studio-download) 来提取 XPath 表达式（它表示要使用的属性），然后在 Azure 门户中使用高级属性编辑器将其添加到预配配置。

**检索 Workday 用户属性的 XPath 表达式：**

1. 下载并安装 [Workday Studio](https://community.workday.com/studio-download)。 需要使用 Workday 社区帐户来访问安装程序。

2. 从 [Workday Web 服务目录](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)下载特定于计划使用的 WWS API 版本的 Workday Human_Resources WSDL 文件

3. 启动 Workday Studio。

4. 在命令栏中，选择“Workday”>“在测试器中测试 Web 服务”选项。

5. 选择“外部”，然后选择在步骤 2 中下载的 Human_Resources WSDL 文件。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. 将“位置”字段设置为 `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`，不过，请将“IMPL-CC”替换为实际实例类型，并将“TENANT”替换为实际租户名称。

7. 将“操作”设置为 **Get_Workers** 

8.    单击“请求/响应”窗格下面的“配置”链接设置 Workday 凭据。 选中“身份验证”，并输入 Workday 集成系统帐户的用户名和密码。 请务必将用户名格式设置为 name\@tenant，并保留选中“WS-Security UsernameToken”选项。
   ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. 选择“确定”。

10. 在“请求”窗格中，粘贴以下 XML。 将“Employee_ID”设置为 Workday 租户中某个真实用户的员工 ID。 将“wd:version”设置为计划使用的 WWS 版本。 选择一个已填充想要提取的属性的用户。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. 单击“发送请求”（绿色箭头）执行该命令。 如果成功，“响应”窗格中应会显示响应。 检查响应，确保其中包含输入的用户 ID 数据且不包含错误。

12. 如果成功，请复制“响应”窗格中的 XML 并将其保存为 XML 文件。

13. 在 Workday Studio 的命令栏中，选择“文件”>“打开文件...”，并打开已保存的 XML 文件。 此操作将在 Workday Studio XML 编辑器中打开该文件。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. 在文件树中，浏览 **/env:Envelope > env:Body > wd:Get_Workers_Response > wd:Response_Data > wd:Worker**，查找用户数据。

15. 在“wd：Worker”下，找到要添加的属性并将其选中。

16. 复制“文档路径”字段中选定属性的 XPath 表达式。

17. 从复制的表达式中删除“/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/”前缀。

18. 如果复制的表达式中的最后一项为节点（例如“/ wd:Birth_Date”），则在表达式的末尾追加“/text()”。 如果最后一项是属性（例如“/@wd: type”），则不需要执行此操作。

19. 结果应类似于 `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`。 此值就是要复制到 Azure 门户的内容。

**将自定义 Workday 用户属性添加到预配配置：**

1. 启动 [Azure 门户](https://portal.azure.com)，并根据本教程前面所述，导航到 Workday 预配应用程序的“预配”部分。

2. 将“预配状态”设置为“关闭”，选择“保存”。   此步骤有助于确保仅在准备就绪时才让更改生效。

3. 在“映射”下，选择“将 Workday 工作人员同步到本地 Active Directory”（或“将 Workday 工作人员同步到 Azure AD”）  。

4. 在下一屏幕中滚动到底部，选择“显示高级选项”。

5. 选择“编辑 Workday 的属性列表”。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. 滚动到属性列表的底部查看输入字段。

7. 对于“名称”，请输入属性的显示名称。

8. 对于“类型”，请选择对应于属性的类型（最常用的选项是“字符串”）。 

9. 对于“API 表达式”，请输入从 Workday Studio 复制的 XPath 表达式。 示例： `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. 选择“添加属性”。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. 选择上面的“保存”，然后在对话框中选择“是”。  如果“属性映射”屏幕仍然打开，请将其关闭。

12. 返回“预配”主选项卡，再次选择“将 Workday 工作人员同步到本地 Active Directory”（或“将 Workday 工作人员同步到 Azure AD”）。  

13. 选择“添加新映射”。

14. 此时，新属性应会显示在“源属性”列表中。

15. 根据需要添加新属性的映射。

16. 完成后，请记得将“预配状态”改回“打开”并保存设置。 

### <a name="exporting-and-importing-your-configuration"></a>导出和导入配置

请参阅[导出和导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)一文

## <a name="managing-personal-data"></a>管理个人数据

Active Directory 的 Workday 预配解决方案要求在本地 Windows Server上安装预配代理；该代理会在 Windows 事件日志中创建日志，其中可能包含个人数据（具体取决于 Workday 到 AD 属性映射）。 为了遵守用户隐私义务，可通过设置 Windows 计划任务来清除事件日志，从而确保 48 小时之前的事件日志中不保留任何数据。

Azure AD 预配服务属于 GDPR 的数据处理器类别。 该服务作为数据处理器管道，向关键合作伙伴和最终客户提供数据处理服务。 Azure AD 预配服务不会生成用户数据，并且对所收集到的个人数据及其使用方式没有独立控制权。 Azure AD 预配服务基于现有企业数据进行数据检索、聚合、分析和报告。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

在数据保留方面，Azure AD 预配服务不会生成报告，也不会执行分析或提供 30 天之前的见解。 因此，Azure AD 预配服务不存储、处理或保留 30 天以前的任何数据。 此设计符合 GDPR 法规要求、Microsoft 隐私符合性规定和 Azure AD 数据保留策略。

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何在 Workday 和 Azure Active Directory 之间配置单一登录](workday-tutorial.md)
* [了解如何将其他 SaaS 应用程序与 Azure Active Directory 进行集成](tutorial-list.md)
* [了解如何使用 Microsoft Graph API 来管理预配配置](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
