---
title: 教程：使用 Azure Active Directory 为 Workday 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Workday 和取消其预配。
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: asmalser
ms.openlocfilehash: 262c864a9e580ab5e2ebb0d4fc1e6ec16adeacb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334320"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>教程：为 Workday 配置自动用户预配

本教程旨在说明需要执行哪些步骤才能将用户从 Workday 导入 Active Directory 和 Azure Active Directory，并有选择性地将某些属性写回到 Workday。

## <a name="overview"></a>概述

[Azure Active Directory 用户预配服务](../active-directory-saas-app-provisioning.md)与 [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 集成，以便能够预配用户帐户。 Azure AD 使用此连接启用以下用户预配工作流：

* **将用户预配到 Active Directory** - 将 Workday 的选定用户集同步到一个或多个 Active Directory 林中。

* **将仅限云的用户预配到 Azure Active Directory** - 可以使用 [AAD Connect](../connect/active-directory-aadconnect.md) 将同时存在于 Active Directory 和 Azure Active Directory 中的混合用户预配到 Azure Active Directory。 但是，可以使用 Azure AD 用户预配服务将仅限云的用户从 Workday 直接预配到 Azure Active Directory。

* **将电子邮件地址写回到 Workday** - Azure AD 用户预配服务可将选定的 Azure AD 用户属性（例如电子邮件地址）写回到 Workday。

### <a name="what-human-resources-scenarios-does-it-cover"></a>该服务涵盖哪些人力资源方案？

Azure AD 用户预配服务支持的 Workday 用户预配工作流可将以下人力资源和标识生命周期管理方案自动化：

* **招聘新员工** - 将新员工添加到 Workday 后，Active Directory、Azure Active Directory、（可选）Office 365 和 [Azure AD 支持的其他 SaaS 应用程序](../active-directory-saas-app-provisioning.md)中会自动创建一个用户帐户，并将电子邮件地址写回到 Workday。

* **员工属性和个人资料更新** - 在 Workday 中更新员工记录（例如其姓名、职称或上司）后，Active Directory、Azure Active Directory、（可选）Office 365 和 [Azure AD 支持的其他 SaaS 应用程序](../active-directory-saas-app-provisioning.md)中会自动更新相应员工的用户帐户。

* **员工离职** - 当某个员工在 Workday 离职时，Active Directory、Azure Active Directory、（可选）Office 365 和 [Azure AD 支持的其他 SaaS 应用程序](../active-directory-saas-app-provisioning.md)中会自动禁用其用户帐户。

* **再次雇用员工** - 当 Workday 再次雇用某个员工时，Active Directory、Azure Active Directory、（可选）Office 365 和 [Azure AD 支持的其他 SaaS 应用程序](../active-directory-saas-app-provisioning.md)中可以自动重新激活或重新预配（取决于你的偏好）该员工的旧帐户。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

此 Workday 用户预配解决方案目前以公共预览版提供，非常适合以下对象：

* 需要使用预建的、基于云的解决方案进行 Workday 用户预配的组织

* 需要将用户预配从 Workday 定向到 Active Directory 或 Azure Active Directory 的组织

* 要求使用从 Workday HCM 模块获取的数据预配用户的组织（请参阅 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)）

* 要求只根据 Workday HCM 模块中检测到的更改信息，加入、移动用户或者使用户保持同步到一个或多个 Active Directory 林、域和 OU 的组织（请参阅 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)）

* 使用 Office 365 收发电子邮件的组织

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="planning-your-solution"></a>规划解决方案

在开始集成 Workday 之前，请检查以下先决条件，并阅读以下指导，了解如何将当前 Active Directory 体系结构和用户预配要求与 Azure Active Directory 提供的解决方案进行匹配。

### <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

* 拥有全局管理员访问权限的有效 Azure AD Premium P1 订阅
* 用于测试和集成目的的 Workday 实现租户
* 在 Workday 中拥有管理员权限，可创建系统集成用户，并可做出更改以便出于测试目的测试员工数据
* 若要将用户预配到 Active Directory，需要使用一台已加入域的、运行 Windows Service 2012 或更高版本的服务器来托管[本地同步代理](https://go.microsoft.com/fwlink/?linkid=847801)
* 已安装 [Azure AD Connect](../connect/active-directory-aadconnect.md)，用于在 Active Directory 与 Azure AD 之间同步

### <a name="solution-architecture"></a>解决方案体系结构

Azure AD 提供一组丰富的预配连接器来帮助解决 Workday 与 Active Directory、Azure AD、SaaS 应用及其他服务之间的预配和标识生命周期管理。 要使用哪些功能以及如何设置解决方案根据组织的环境和要求而有所不同。 首先需要盘点组织中存在和部署的以下各项的数量：

* 正在使用多少个 Active Directory 林？
* 正在使用多少个 Active Directory 域？
* 正在使用多少个 Active Directory 组织单位 (OU)？
* 正在使用多少个 Azure Active Directory 租户？
* 是否需要将某些用户同时预配到 Active Directory 和 Azure Active Directory（例如“混合”用户）？
* 是否需要将某些用户预配到 Azure Active Directory，但不需要将其预配到 Active Directory（例如“仅限云”用户）？
* 是否需要将用户电子邮件地址写回到 Workday？

获得这些问题的答案后，便可以遵照以下指导规划 Workday 预配部署。

#### <a name="using-provisioning-connector-apps"></a>使用预配连接器应用

Azure Active Directory 支持适用于 Workday 的预先集成预配连接器以及其他众多的 SaaS 应用程序。

单个预配连接器将与单个源系统的 API 对接，帮助将数据预配到单个目标系统。 Azure AD 支持的大多数预配连接器都适用于单个源和目标系统（例如 Azure AD 到 ServiceNow），通过从 Azure AD 应用库中添加相关应用（例如 ServiceNow）即可设置这些连接器。

Azure AD 中的预配连接器实例与应用实例之间存在一对一的关系：

| 源系统 | 目标系统 |
| ---------- | ---------- |
| Azure AD 租户 | SaaS 应用程序 |

但是，在使用 Workday 和 Active Directory 时，需要考虑多个源和目标系统：

| 源系统 | 目标系统 | 说明 |
| ---------- | ---------- | ---------- |
| Workday | Active Directory 林 | 每个林被视为不同的目标系统 |
| Workday | Azure AD 租户 | 根据仅限云的用户的需要 |
| Active Directory 林 | Azure AD 租户 | 此流目前由 AAD Connect 处理 |
| Azure AD 租户 | Workday | 用于写回电子邮件地址 |

为方便在多个源和目标系统上实施这些工作流，Azure AD 提供了多个预配连接器应用（可从 Azure AD 应用库添加）：

![AAD 应用库](./media/workday-inbound-tutorial/WD_Gallery.PNG)

* **Workday 到 Active Directory 的预配** - 此应用有助于将用户帐户从 Workday 预配到单个 Active Directory 林。 如果你有多个林，可以针对需要预配到的每个 Active Directory 林，从 Azure AD 应用库添加此应用的一个实例。

* **Workday 到 Azure AD 的预配** - 应该使用 AAD Connect 工具将 Active Directory 用户同步到 Azure Active Directory，同时，可以借助此应用将仅限云的用户从 Workday 预配到单个 Azure Active Directory 租户。

* **Workday 写回** - 此应用有助于将用户的电子邮件地址从 Azure Active Directory 写回到 Workday。

> [!TIP]
> 普通的“Workday”应用用于在 Workday 与 Azure Active Directory 之间设置单一登录。 

如何设置和配置这些特殊的预配连接器应用，属于本教程余下部分的主题。 要选择配置哪些应用将取决于需要预配到哪些系统，以及环境中有多少个 Active Directory 林和 Azure AD 租户。

![概述](./media/workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>在 Workday 中配置系统集成用户
所有 Workday 预配连接器的一个常见要求是需要使用 Workday 系统集成帐户的凭据连接到 Workday Human Resources API。 本部分介绍如何在 Workday 中创建系统集成者帐户。

> [!NOTE]
> 可以绕过此过程并改用 Workday 全局管理员帐户作为系统集成帐户。 在演示中这样做不会有任何问题，但是，对于生产部署，我们不建议这样做。

### <a name="create-an-integration-system-user"></a>创建集成系统用户

**创建集成系统用户：**

1. 使用管理员帐户登录到 Workday 租户。 在“Workday 工作台”的搜索框中，输入“创建用户”，然后单击“创建集成系统用户”。

    ![Create user](./media/workday-inbound-tutorial/IC750979.png "创建用户")
2. 通过为新的集成系统用户提供用户名和密码，完成“创建集成系统用户”任务。  
 * 使“下次登录时需要新密码”选项处于未选中状态，因为此用户将以编程方式登录。
 * 将“会话超时(分钟)”保留为其默认值 0，这将阻止用户会话过早超时。

    ![创建集成系统用户](./media/workday-inbound-tutorial/IC750980.png "创建集成系统用户")

### <a name="create-a-security-group"></a>创建安全组
需要创建不受约束的集成系统安全组并为其分配用户。

**创建安全组：**

1. 在搜索框中输入“创建安全组”，然后单击“创建安全组”。

    ![创建安全组](./media/workday-inbound-tutorial/IC750981.png "创建安全组")
2. 完成“创建安全组”任务。  
3. 从“租用安全组类型”下拉列表中选择“集成系统安全组(不受约束)”。
4. 创建将在其中显式添加成员的安全组。

    ![创建安全组](./media/workday-inbound-tutorial/IC750982.png "创建安全组")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>将集成系统用户分配给安全组

**分配集成系统用户：**

1. 在搜索框中输入“编辑安全组”，然后单击“编辑安全组”。

    ![编辑安全组](./media/workday-inbound-tutorial/IC750983.png "编辑安全组")
1. 按名称搜索新的集成安全组并选中。

    ![编辑安全组](./media/workday-inbound-tutorial/IC750984.png "编辑安全组")
2. 将新的集成系统用户添加到新的安全组。 

    ![系统安全组](./media/workday-inbound-tutorial/IC750985.png "系统安全组")  

### <a name="configure-security-group-options"></a>配置安全组选项
此步骤授予域安全策略对安全组中工作人员数据的权限。

**配置安全组选项：**

1. 在搜索框中输入“域安全策略”，然后单击链接“功能区域的域安全策略”。  

    ![域安全策略](./media/workday-inbound-tutorial/IC750986.png "域安全策略")  
2. 搜索“系统”并选择“系统”功能区域。  单击“确定”。  

    ![域安全策略](./media/workday-inbound-tutorial/IC750987.png "域安全策略")  
3. 在系统功能区域的安全策略列表中，展开“安全管理”并选择域安全策略“外部帐户预配”。  

    ![域安全策略](./media/workday-inbound-tutorial/IC750988.png "域安全策略")  
1. 单击“编辑权限”，然后在“编辑权限”对话框页上将新安全组添加到具有 **Get** 和 **Put** 集成权限的安全组列表中。

    ![编辑权限](./media/workday-inbound-tutorial/IC750989.png "编辑权限")  

1. 针对剩余的每个安全策略重复上述步骤 1-4：

| Operation | 域安全策略 |
| ---------- | ---------- | 
| “获取”和“放置” | 工作人员数据：公职人员报告 |
| “获取”和“放置” | 工作人员数据：工作联系信息 |
| Get | 工作人员数据：所有职位 |
| Get | 工作人员数据：当前人员配备信息 |
| Get | 工作人员数据：工作人员个人资料中的职称 |


### <a name="activate-security-policy-changes"></a>激活安全策略更改

**激活安全策略更改：**

1. 在搜索框中输入“激活”，然后单击链接“激活挂起的安全策略更改”。

    ![激活](./media/workday-inbound-tutorial/IC750992.png "激活") 
2. 通过输入用于审核的注释，然后单击“确定”，开始“激活挂起的安全策略更改”任务。 

    ![激活挂起的安全性](./media/workday-inbound-tutorial/IC750993.png "激活挂起的安全性")  
1. 选中“确认”复选框，然后单击“确定”，完成下一屏幕上的任务。

    ![激活挂起的安全性](./media/workday-inbound-tutorial/IC750994.png "激活挂起的安全性")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>配置从 Workday 到 Active Directory 的用户预配
遵照以下说明，配置从 Workday 到需要预配到的每个 Active Directory 林的用户帐户预配。

### <a name="planning"></a>规划

在配置目标为 Active Directory 林的用户预配之前，请考虑以下问题。 这些问题的答案将会确定如何设置范围筛选器和属性映射。 

* **需要将 Workday 中的哪些用户预配到此 Active Directory 林？**

   * *示例：Workday“Company”属性包含值“Contoso”，并且“Worker_Type”属性包含“Regular”的用户*

* **如何将用户路由到不同的组织单位 (OU)？**

   * *示例：将用户路由到与 Workday“Municipality”和“Country_Region_Reference”属性中定义的办公地点相对应的 OU*

* **如何在 Active Directory 中填充以下属性？**

   * 公用名 (cn)
      * *示例：使用人力资源部门设置的 Workday User_ID 值*
      
   * 员工 ID (employeeId)
      * *示例：使用 Workday Worker_ID 值*
      
   * SAM 帐户名 (sAMAccountName)
      * *示例：使用 Workday User_ID 值，该值已通过 Azure AD 预配表达式筛选，以删除非法字符*
      
   * 用户主体名称 (userPrincipalName)
      * *示例：使用 Workday User_ID 值，并通过 Azure AD 预配表达式在其后追加一个域名*

* **如何匹配 Workday 与 Active Directory 之间的用户？**

  * *示例：将具有特定 Workday“Worker_ID”值的用户与“employeeID”值相同的 Active Directory 用户进行匹配。如果在 Active Directory 中找不到该 Worker_ID 值，则创建新用户。*
  
* **Active Directory 林是否已包含使匹配逻辑正常工作所需的用户 ID？**

  * *示例：如果这是新的 Workday 部署，则我们强烈建议在 Active Directory 中预先填充正确的 Workday Worker_ID 值（或所选的唯一 ID 值），以便尽量简化匹配逻辑。*
    
    
### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：添加预配连接器应用并与 Workday 建立连接

**若要配置 Workday 到 Active Directory 的预配：**

1.  转到 <https://portal.azure.com>

2.  在左侧导航栏中选择“Azure Active Directory”。

3.  依次选择“企业应用程序”、“所有应用程序”。

4.  依次选择“添加应用程序”、“所有”类别。

5.  搜索“Workday 到 Active Directory 的预配”，然后从库中添加该应用。

6.  添加应用并显示应用详细信息屏幕后，请选择“预配”

7.  将“预配模式”更改为“自动”

8.  按如下所述完成“管理员凭据”部分：

   * **管理员用户名** – 输入 Workday 集成系统帐户的用户名，并附加租户域名。 **此值应类似于：username@contoso4**

   * **管理员密码** – 输入 Workday 集成系统帐户的密码

   * **租户 URL** – 输入租户的 Workday Web 服务终结点的 URL。 此值应类似于：https://wd3-impl-services1.workday.com/ccx/service/contoso4，其中，contoso4 需替换为正确的租户名，wd3-impl 需替换为正确的环境字符串。

   * **Active Directory 林** – Get-ADForest PowerShell cmdlet 返回的 Active Directory 林“名称”。 这通常是如下所示的字符串：*contoso.com*

   * **Active Directory 容器** – 输入包含 AD 林中所有用户的容器字符串。 示例：*OU=Standard Users,OU=Users,DC=contoso,DC=test*

   * **通知电子邮件** – 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。

   * 单击“测试连接”按钮。 如果连接测试成功，请单击顶部的“保存”按钮。 如果测试失败，请仔细检查 Workday 中的 Workday 凭据是否有效。 

![Azure 门户](./media/workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：配置属性映射 

在本部分配置用户数据如何从 Workday 流入 Active Directory。

1.  在“预配”选项卡中的“映射”下面，单击“将 Workday 工作人员同步到本地”。

2.  在“源对象范围”字段中，可以通过定义一组基于属性的筛选器，选择 Workday 中要预配到 AD 的用户集范围。 默认范围是“Workday 中的所有用户”。 示例筛选器：

   * 示例：将范围限定为工作人员 ID 为 1000000 到 2000000 的用户

      * 属性：WorkerID

      * 运算符：REGEX 匹配

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 示例：仅限员工和非临时工 

      * 属性：EmployeeID

      * 运算符：IS NOT NULL

3.  在“目标对象操作”字段中，可以全局筛选允许对 Active Directory 执行的操作。 “创建”和“更新”是最常见的操作。

4.  在“属性映射”部分中，可以定义 Workday 属性到 Active Directory 属性的各个映射。

5. 单击现有的属性映射可将其更新，单击屏幕底部的“添加新映射”可添加新的映射。 单个属性映射支持以下属性：

      * **映射类型**

         * **直接** – 将 Workday 属性的值按原样写入 AD 属性

         * **常量** – 将静态常量字符串值写入 AD 属性

         * **表达式** – 可以基于一个或多个 Workday 属性将自定义值写入 AD 属性。 [有关详细信息，请参阅这篇有关表达式的文章](../active-directory-saas-writing-expressions-for-attribute-mappings.md)。

      * **源属性** – Workday 中的用户属性。 如果你查找的属性不存在，请参阅[自定义 Workday 用户属性列表](#customizing-the-list-of-workday-user-attributes)。

      * **默认值** – 可选。 如果源属性的值为空，映射将改为写入此值。
            最常见的配置是将此项留空。

      * **目标属性** – Active Directory 中的用户属性。

      * **使用此属性匹配对象** – 是否应使用此映射唯一标识 Workday 与 Active Directory 之间的用户。 通常在 Workday 的“工作人员 ID”字段中设置此值，此值通常映射到 Active Directory 中的“员工 ID”属性之一。

      * **匹配优先顺序** – 可设置多个匹配属性。 当存在匹配时，会按照此字段定义的顺序进行评估。 一旦找到匹配，就不会进一步评估其他匹配属性。

      * **应用此映射**
       
         * **始终** – 在用户的创建和更新操作上均应用此映射

         * **仅创建期间** – 在用户的创建操作上应用此映射

6. 若要保存映射，请单击“属性映射”部分顶部的“保存”。

![Azure 门户](./media/workday-inbound-tutorial/WD_2.PNG)

**下面是 Workday 与 Active Directory 之间的一些示例属性映射，以及一些常用的表达式**

-   可以根据一个或多个 Workday 源属性，使用可映射到 parentDistinguishedName 属性的表达式将用户预配到不同的 OU。 以下示例根据用户所在的城市，将用户放入不同的 OU。

-   Active Directory 中的 userPrincipalName 属性是通过将 Workday 用户 ID 与域后缀相串联来生成的

-   [此处提供了有关编写表达式的文档](../active-directory-saas-writing-expressions-for-attribute-mappings.md)。 其中包括有关如何删除特殊字符的示例。

  
| WORKDAY 属性 | ACTIVE DIRECTORY 属性 |  匹配 ID？ | 创建/更新 |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **是** | 仅在创建时写入 |
| **UserID**    |  cn    |   |   仅在创建时写入 |
| **Join("@", [UserID], "contoso.com")**   | userPrincipalName     |     | 仅在创建时写入 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         仅在创建时写入 |
| **Switch(\[Active\], , "0", "True", "1",)** |  accountDisabled      |     | 创建 + 更新 |
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
| **CountryRegionReference** |  号     |     | 创建 + 更新 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  创建 + 更新 |
| **PostalCode**  |   postalCode  |     | 创建 + 更新 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 创建 + 更新 |
| **Fax**      | facsimileTelephoneNumber     |     |    创建 + 更新 |
| **Mobile**  |    mobile       |     |       创建 + 更新 |
| **LocalReference** |  preferredLanguage  |     |  创建 + 更新 |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  创建 + 更新 |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>第 3 部分：配置本地同步代理

若要预配到本地 Active Directory，必须在所需 Active Directory 林中某台已加入域的服务器上安装一个代理。 完成该过程需要域管理员（或企业管理员）凭据。

**[可在此处下载本地同步代理](https://go.microsoft.com/fwlink/?linkid=847801)**

安装代理后，请运行以下 Powershell 命令为环境配置代理。

**命令 #1**

> cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Modules\AADSyncAgent" Agent\\Modules\\AADSyncAgent

> Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Modules\AADSyncAgent\AADSyncAgent.psd1"

**命令 #2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* 输入：对于“目录名称”，请输入在第 \#2 部分中所输入的 AD 林名称
* 输入：Active Directory 林的管理员用户名和密码

**命令 #3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* 输入：Azure AD 租户的全局管理员用户名和密码

>[!IMPORTANT]
>目前有一个已知的问题：如果全局管理员凭据使用自定义域（例如：admin@contoso.com），则这些凭据不起作用。 解决方法是创建并使用包含 onmicrosoft.com 域的全局管理员帐户（例如：admin@contoso.onmicrosoft.com）

>[!IMPORTANT]
>目前有一个已知的问题：如果全局管理员凭据使用多重身份验证，则这些凭据不起作用。 要解决这个问题，可以禁用全局管理员的多重身份验证。


**命令 #4**

> Get-AdSyncAgentProvisioningTasks

* 操作：确认已返回数据。 此命令会自动发现 Azure AD 租户中的 Workday 预配应用。 示例输出：

> Name          : My AD Forest
>
> Enabled       : True
>
> DirectoryName : mydomain.contoso.com
>
> Credentialed  : False
>
> Identifier    : WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**命令 #5**

> Start-AdSyncAgentSynchronization -Automatic

**命令 #6**

> net stop aadsyncagent

**命令 #7**

> net start aadsyncagent

>[!TIP]
>除了 Powershell 中的“net”命令以外，还可以使用 **Services.msc** 启动和停止同步代理服务。 如果运行 Powershell 命令时出错，请确保 **Microsoft Azure AD Connect Provisioning Agent** 在 **Services.msc** 中运行。

![服务](./media/workday-inbound-tutorial/Services.png)  

**适用于欧盟客户的附加配置**

如果 Azure Active Directory 租户位于某个欧盟数据中心，请遵循以下附加步骤。

1. 打开 **Services.msc**，并停止 **Microsoft Azure AD Connect Provisioning Agent** 服务。
2. 转到代理安装文件夹（例如：C:\Program Files\Microsoft Azure AD Connect Provisioning Agent）。
3. 在文本编辑器中打开 **SyncAgnt.exe.config**。
4. 将 https://manage.hub.syncfabric.windowsazure.com/Management 替换为 https://eu.manage.hub.syncfabric.windowsazure.com/Management
5. 将 https://provision.hub.syncfabric.windowsazure.com/Provisioning 替换为 https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning
6. 保存 **SyncAgnt.exe.config** 文件。
7. 打开 **Services.msc**，并启动 **Microsoft Azure AD Connect Provisioning Agent** 服务。

**代理故障排除**

托管代理的 Windows Server 计算机上的 [Windows 事件日志](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)包含代理执行的所有操作的相关事件。 若要查看这些事件，请执行以下操作：
    
1. 打开 **Eventvwr.msc**。
2. 选择“Windows 日志”>“应用程序”。
3. 查看 **AADSyncAgent** 源下记录的所有事件。 
4. 检查错误和警告。

如果在 Powershell 命令中提供的 Active Directory 或 Azure Active Directory 凭据出现权限问题，会显示如下所示的错误： 
    
![事件日志](./media/workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>第 4 部分：启动服务
完成第 1-3 部分后，可以返回 Azure 门户并启动预配服务。

1.  在“预配”选项卡中，将“预配状态”设置为“打开”。

2. 单击“ **保存**”。

3. 随后将启动初始同步。根据 Workday 中的用户数，此过程花费的时间可能有所不同。

4. 无论何时，检查 Azure 门户中的“审核日志”选项卡都可以查看预配服务执行的操作。 审核日志列出预配服务执行的所有同步事件，例如，正在从 Workday 中读出哪些用户，随后将其添加或更新到 Active Directory。 **[有关如何读取审核日志的详细说明，请参阅预配报告指南](../active-directory-saas-provisioning-reporting.md)**

1.  在托管代理的 Windows Server 计算机上的 [Windows 事件日志](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)中查看任何新的错误或警告。 在服务器上启动“Eventvwr.msc”并选择“Windows 日志”>“应用程序”即可查看这些事件。 所有与预配相关的消息记录在 **AADSyncAgent** 源下面。

6. 完成某项操作后，系统会在“预配”选项卡中写入一份审核摘要报告，如下所示。

![Azure 门户](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>配置到 Azure Active Directory 的用户预配
如何配置到 Azure Active Directory 的预配取决于预配要求，下表对此做了详述。

| 场景 | 解决方案 |
| -------- | -------- |
| **需要将用户预配到 Active Directory 和 Azure AD** | 使用 **[AAD Connect](../connect/active-directory-aadconnect.md)** |
| **只需将用户预配到 Active Directory** | 使用 **[AAD Connect](../connect/active-directory-aadconnect.md)** |
| **只需将用户预配到 Azure AD（仅限云）** | 使用应用库中的“Workday 到 Azure Active Directory 的预配”应用 |

有关设置 Azure AD Connect 的说明，请参阅 [Azure AD Connect 文档](../connect/active-directory-aadconnect.md)。

以下部分介绍如何在 Workday 与 Azure AD 之间设置连接，以预配仅限云的用户。

> [!IMPORTANT]
> 仅当需要将仅限云的用户预配到 Azure AD 而不是本地 Active Directory 时，才可遵循以下过程。

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：添加 Azure AD 预配连接器应用并与 Workday 建立连接

**若要为仅限云的用户配置 Workday 到 Azure Active Directory 的预配：**

1.  转到 <https://portal.azure.com>。

2.  在左侧导航栏中选择“Azure Active Directory”。

3.  依次选择“企业应用程序”、“所有应用程序”。

4.  依次选择“添加应用程序”、“所有”类别。

5.  搜索“Workday 到 Azure AD 的预配”，然后从库中添加该应用。

6.  添加应用并显示应用详细信息屏幕后，请选择“预配”

7.  将“预配模式”更改为“自动”

8.  按如下所述完成“管理员凭据”部分：

   * **管理员用户名** – 输入 Workday 集成系统帐户的用户名，并附加租户域名。 此值应类似于：username@contoso4

   * **管理员密码** – 输入 Workday 集成系统帐户的密码

   * **租户 URL** – 输入租户的 Workday Web 服务终结点的 URL。 此值应类似于：https://wd3-impl-services1.workday.com/ccx/service/contoso4，其中，contoso4 需替换为正确的租户名，wd3-impl 需替换为正确的环境字符串。 如果不知道此 URL，请咨询 Workday 集成合作伙伴或支持代表，确定要使用的正确 URL。

   * **通知电子邮件** – 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。

   * 单击“测试连接”按钮。

   * 如果连接测试成功，请单击顶部的“保存”按钮。 如果测试失败，请仔细检查 Workday 中的 Workday URL 和凭据是否有效。

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：配置属性映射 

在本部分配置仅限云的用户的用户数据如何从 Workday 流入 Azure Active Directory。

1. 在“预配”选项卡中的“映射”下面，单击“将工作人员同步到 Azure AD”。

2. 在“源对象范围”字段中，可以通过定义一组基于属性的筛选器，选择 Workday 中要预配到 Azure AD 的用户集范围。 默认范围是“Workday 中的所有用户”。 示例筛选器：

   * 示例：将范围限定为工作人员 ID 为 1000000 到 2000000 的用户

      * 属性：WorkerID

      * 运算符：REGEX 匹配

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 示例：仅限临时工和非普通员工

      * 属性：ContingentID

      * 运算符：IS NOT NULL

3. 在“目标对象操作”字段中，可以全局筛选允许对 Azure AD 执行的操作。 “创建”和“更新”是最常见的操作。

4. 在“属性映射”部分中，可以定义将单个 Workday 属性映射到 Active Directory 属性的方式。

5. 单击现有的属性映射可将其更新，单击屏幕底部的“添加新映射”可添加新的映射。 单个属性映射支持以下属性：

   * **映射类型**

      * **直接** – 将 Workday 属性的值按原样写入 AD 属性

      * **常量** – 将静态常量字符串值写入 AD 属性

      * **表达式** – 可以基于一个或多个 Workday 属性将自定义值写入 AD 属性。 [有关详细信息，请参阅这篇有关表达式的文章](../active-directory-saas-writing-expressions-for-attribute-mappings.md)。

   * **源属性** – Workday 中的用户属性。 如果你查找的属性不存在，请参阅[自定义 Workday 用户属性列表](#customizing-the-list-of-workday-user-attributes)。

   * **默认值** – 可选。 如果源属性的值为空，映射将改为写入此值。
            最常见的配置是将此项留空。

   * **目标属性** – Azure AD 中的用户属性。

   * **使用此属性匹配对象** – 是否应使用此映射唯一标识 Workday 与 Azure AD 之间的用户。 通常在 Workday 的“工作人员 ID”字段中设置此值，此值通常映射到 Azure AD 中的“员工 ID”属性（新）或扩展属性。

   * 匹配优先级 – 可设置多个匹配属性。 当存在匹配时，会按照此字段定义的顺序进行评估。 一旦找到匹配，就不会进一步评估其他匹配属性。

   * **应用此映射**

     * **始终** – 在用户的创建和更新操作上均应用此映射

     * **仅创建期间** – 在用户的创建操作上应用此映射

6. 若要保存映射，请单击“属性映射”部分顶部的“保存”。

### <a name="part-3-start-the-service"></a>第 3 部分：启动服务
完成第 1-2 部分后，可以启动预配服务。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。

2. 单击“ **保存**”。

3. 随后将启动初始同步。根据 Workday 中的用户数，此过程花费的时间可能有所不同。

4. 可在“审核日志”选项卡中查看单个同步事件。**[有关如何读取审核日志的详细说明，请参阅预配报告指南](../active-directory-saas-provisioning-reporting.md)**

5. 完成某项操作后，系统会在“预配”选项卡中写入一份审核摘要报告，如下所示。

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>配置向 Workday 写回电子邮件地址
遵照以下说明，配置将用户电子邮件地址从 Azure Active Directory 写回到 Workday。

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：添加预配连接器应用并与 Workday 建立连接

**若要配置 Workday 到 Active Directory 的预配：**

1. 转到 <https://portal.azure.com>

2. 在左侧导航栏中选择“Azure Active Directory”。

3. 依次选择“企业应用程序”、“所有应用程序”。

4. 依次选择“添加应用程序”、“所有”类别。

5. 搜索“Workday 写回”，然后从库中添加该应用。

6. 添加应用并显示应用详细信息屏幕后，请选择“预配”

7. 将“预配模式”更改为“自动”

8. 按如下所述完成“管理员凭据”部分：

   * **管理员用户名** – 输入 Workday 集成系统帐户的用户名，并附加租户域名。 此值应类似于：username@contoso4

   * **管理员密码** – 输入 Workday 集成系统帐户的密码

   * **租户 URL** – 输入租户的 Workday Web 服务终结点的 URL。 此值应类似于：https://wd3-impl-services1.workday.com/ccx/service/contoso4，其中，contoso4 需替换为正确的租户名，wd3-impl 需替换为正确的环境字符串（如果需要）。

   * **通知电子邮件** – 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。

   * 单击“测试连接”按钮。 如果连接测试成功，请单击顶部的“保存”按钮。 如果测试失败，请仔细检查 Workday 中的 Workday URL 和凭据是否有效。

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：配置属性映射 

在本部分配置用户数据如何从 Workday 流入 Active Directory。

1. 在“预配”选项卡中的“映射”下面，单击“将 Azure AD 用户同步到 Workday”。

2. 在“源对象范围”字段中，可以有选择性地在 Azure Active Directory 中筛选要将其电子邮件地址写回到 Workday 的用户集。 默认范围是“Azure AD 中的所有用户”。 

3. 在“属性映射”部分中，可以定义 Workday 属性到 Active Directory 属性的各个映射。 默认情况下，电子邮件地址存在映射。 但是，必须更新匹配 ID，使 Azure AD 中的用户与其在 Workday 中的相应条目匹配。 常用的匹配方法是将 Workday 工作人员 ID 或员工 ID 同步到 Azure AD 中的 extensionAttribute1-15，然后使用 Azure AD 中的此属性来重新匹配 Workday 中的用户。

4. 若要保存映射，请单击“属性映射”部分顶部的“保存”。

### <a name="part-3-start-the-service"></a>第 3 部分：启动服务
完成第 1-2 部分后，可以启动预配服务。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。

2. 单击“ **保存**”。

3. 随后将启动初始同步。根据 Workday 中的用户数，此过程花费的时间可能有所不同。

4. 可在“审核日志”选项卡中查看单个同步事件。**[有关如何读取审核日志的详细说明，请参阅预配报告指南](../active-directory-saas-provisioning-reporting.md)**

5. 完成某项操作后，系统会在“预配”选项卡中写入一份审核摘要报告，如下所示。

## <a name="customizing-the-list-of-workday-user-attributes"></a>自定义 Workday 用户属性列表
适用于 Active Directory 和 Azure AD 的 Workday 预配应用都包含一个默认的 Workday 用户属性列表，可以从中进行选择。 但是，这些列表并不详尽。 Workday 支持数百个可能的用户属性，这些属性可能是 Workday 租户中的标准属性或唯一属性。 

Azure AD 预配服务支持自定义列表或 Workday 属性，以包含人力资源 API 的 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 操作中公开的所有属性。

为此，必须使用 [Workday Studio](https://community.workday.com/studio-download) 来提取表示要使用的属性的 XPath 表达式，然后在 Azure 门户中使用高级属性编辑器将其添加到预配配置。

**检索 Workday 用户属性的 XPath 表达式：**

1. 下载并安装 [Workday Studio](https://community.workday.com/studio-download)。 需要使用 Workday 社区帐户来访问安装程序。

2. 从此 URL 中下载 Workday Human_Resources WDSL 文件：https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. 启动 Workday Studio。

4. 在命令栏中，选择“Workday”>“在测试器中测试 Web 服务”选项。

5. 选择“外部”，然后选择在步骤 2 中下载的 Human_Resources WSDL 文件。

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio1.PNG)

6. 将“位置”字段设置为 `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`，不过，请将“IMPL-CC”替换为实际实例类型，并将“TENANT”替换为实际租户名称。

7. 将“操作”设置为 **Get_Workers**

8.  单击“请求/响应”窗格下面的“配置”链接设置 Workday 凭据。 选中“身份验证”，并输入 Workday 集成系统帐户的用户名和密码。 请务必将用户名格式设置为 name@tenant，并保留选中“WS-Security UsernameToken”选项。

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio2.PNG)

9. 选择“确定”。

10. 在“请求”窗格中，粘贴以下 XML，并将“Employee_ID”设置为 Workday 租户中某个真实用户的员工 ID。 选择一个已填充想要提取的属性的用户。

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
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

13. 在 Workday Studio 的命令栏中，选择“文件”>“打开文件...”，并打开已保存的 XML 文件。 这会在 Workday Studio XML 编辑器中打开该文件。

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio3.PNG)

14. 在文件树中，浏览“/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker”找到用户的数据。 

15. 在“wd: Worker”下，找到要添加的属性并将其选中。

16. 复制“文档路径”字段中选定属性的 XPath 表达式。

1. 从复制的表达式中删除“/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/”前缀。

18. 如果复制的表达式中的最后一项是节点（例如“/wd: Birth_Date”），请在表达式的末尾追加 **/text()**。 如果最后一项是属性（例如“/@wd: type”），则不需要执行此操作。

19. 结果应类似于 `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`。 这就是要复制到 Azure 门户中的内容。


**将自定义 Workday 用户属性添加到预配配置：**

1. 启动 [Azure 门户](https://portal.azure.com)，并根据本教程前面所述，导航到 Workday 预配应用程序的“预配”部分。

2. 将“预配状态”设置为“关闭”，选择“保存”。 这有助于确保只有在准备就绪时，才让更改生效。

3. 在“映射”下，选择“将工作人员同步到本地”（或“将工作人员同步到 Azure AD”）。

4. 在下一屏幕中滚动到底部，选择“显示高级选项”。

5. 选择“编辑 Workday 的属性列表”。

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. 滚动到属性列表的底部查看输入字段。

7. 对于“名称”，请输入属性的显示名称。

8. 对于“类型”，请选择对应于属性的类型（最常用的选项是“字符串”）。

9. 对于“API 表达式”，请输入从 Workday Studio 复制的 XPath 表达式。 示例：`wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. 选择“添加属性”。

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. 选择上面的“保存”，然后在对话框中选择“是”。 如果“属性映射”屏幕仍然打开，请将其关闭。

12. 返回“预配”主选项卡，再次选择“将工作人员同步到本地”（或“将工作人员同步到 Azure AD”）。

13. 选择“添加新映射”。

14. 此时，新属性应会显示在“源属性”列表中。

15. 根据需要添加新属性的映射。

16. 完成后，请记得将“预配状态”改回“打开”并保存设置。

## <a name="known-issues"></a>已知问题

* 运行 **Add-ADSyncAgentAzureActiveDirectoryConfiguration** Powershell 命令时，目前有一个已知的问题：如果全局管理员凭据使用自定义域（例如：admin@contoso.com），则这些凭据不起作用。 解决方法是在 Azure AD 中创建并使用包含 onmicrosoft.com 域的全局管理员帐户（例如：admin@contoso.onmicrosoft.com）。

* 以前存在的，审核日志在位于欧盟的 Azure AD 租户中不显示的问题现已得到解决。 但是，需要对欧盟的 Azure AD 租户进行附加的代理配置。 有关详细信息，请参阅[第 3 部分：配置本地同步代理](#Part 3: Configure the on-premises synchronization agent)

## <a name="managing-personal-data"></a>管理个人数据

Active Directory 的 Workday 预配解决方案需要在已加入域的服务器上安装同步代理，并且此代理会在 Windows 事件日志中创建可包含个人身份信息的日志。

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../active-directory-saas-provisioning-reporting.md)
* [了解如何在 Workday 和 Azure Active Directory 之间配置单一登录](workday-tutorial.md)
* [了解如何将其他 SaaS 应用程序与 Azure Active Directory 进行集成](tutorial-list.md)
