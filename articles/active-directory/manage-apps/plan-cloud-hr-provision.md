---
title: 规划云 HR 应用程序以 Azure Active Directory 用户预配
description: 本文介绍将 cloud HR 系统（如 Workday 和 Sucessfactors）与 Azure Active Directory 集成的部署过程。 将 Azure AD 与云 HR 系统集成，会生成一个完整的标识生命周期管理系统。
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 6f72371077aab813cc22c9bbbe755fdfaa9ac00a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433822"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>规划云 HR 应用程序以 Azure Active Directory 用户预配

过去的 IT 人员依赖手动方法来创建、更新和删除员工，如上传 CSV 文件或自定义脚本来同步员工数据。 这些设置过程容易出错、不安全且难以管理。

为了无缝地管理员工、供应商或临时工作人员的端到端标识生命周期， [Azure Active Directory （Azure AD）用户预配服务](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)提供与基于云的人力资源（HR）应用程序（如 Workday 或 SuccessFactors）的集成。

Azure AD 使用此集成来启用以下 cloud HR 应用程序（应用）工作流：

- 将云 HR 应用的 AD 预配选择的用户集设置**为**一个或多个 ACTIVE DIRECTORY （AD）域。
- **将仅限云的用户预配到**不使用 AD 的 Azure AD 方案中，直接从云 HR 应用程序将用户设置为 Azure AD。
- **写回云 HR 应用。** -将电子邮件地址和用户名属性从 Azure AD 写入 cloud HR 应用。

> [!NOTE]
> 此部署计划指导如何在 Azure AD 用户预配的情况下部署云 HR 应用工作流。 有关将自动用户预配部署到 SaaS 应用的信息，请参阅[计划自动用户预配部署](https://aka.ms/deploymentplans/provisioning)。

## <a name="enabled-hr-scenarios"></a>启用的 HR 方案

Azure AD 用户预配服务实现了以下基于 HR 的标识生命周期管理方案的自动化：

- **招聘新员工**-将新员工添加到云 hr 应用时，会自动在 AD 中创建一个用户帐户，并 Azure AD，并提供将电子邮件地址和用户名属性写回到云 hr 应用的选项。
- **员工属性和配置文件更新**-在 cloud HR 应用中更新员工记录（例如，其名称、标题或经理）时，会在 AD 中自动更新其用户帐户，并 Azure AD。
- **员工**离职-当员工在云 HR 应用中终止时，他们的用户帐户会在 AD 中自动禁用并 Azure AD。
- **员工 rehires** -当员工在云 HR 应用中 rehired 时，可以自动将其旧帐户重新激活或重新预配到 AD 并 Azure AD。

## <a name="who-is-this-integration-best-suited-for"></a>此集成最适合于哪一种？

Cloud HR 应用与 Azure AD 用户预配的集成非常适合于以下组织：

- 需要预先构建的云 HR 用户预配解决方案
- 需要从 cloud HR 应用到 AD 的直接用户预配，或 Azure AD
- 要求使用从云 HR 应用获取的数据预配用户
- 需要根据在云 HR 应用中检测到的更改信息，加入、移动和离开用户以同步到一个或多个 AD 林、域和 Ou
- 使用 Office 365 发送电子邮件

## <a name="learn"></a>学习

用户预配为正在进行的标识管理创建了基础，并增强了依赖于权威标识数据的业务流程的质量。

### <a name="terms"></a>术语

本文使用以下术语：

- **源系统**-Azure AD 从其预配的用户（例如，Workday 和 SuccessFactors 等云 HR 应用程序的存储库。）
- **目标系统**-Azure AD 预配到的用户（例如 AD、Azure AD、Office365 或其他 SaaS 应用）的存储库。
- **Joiners-离开者 process** -一项术语，用于使用云 HR 应用作为记录系统的新员工、转移和终止。 当服务成功地将所需的属性预配到目标系统时，该过程完成。

### <a name="key-benefits"></a>主要优点

这种 HR 驱动的 IT 预配功能提供重要的业务好处，如下所示：

- **提高工作效率**-你现在可以自动分配用户帐户、Office365 许可证，并提供对密钥组的访问权限。 自动分配允许新员工立即访问其作业工具并提高工作效率。
- **管理风险**-可以通过基于员工状态或从云 HR 应用中流入数据的组成员身份自动执行更改，从而提高安全性。 自动更改可确保用户的标识和访问密钥应用在用户转换或离开组织时自动更新。
- **解决相容性和监管**-Azure AD 支持由源系统和目标系统的应用执行的用户预配请求的本地审核日志。 审核使你可以从单个屏幕跟踪对应用程序的访问权限。
- **管理成本**-自动预配通过避免与手动预配相关的低效和人为错误，降低了成本。 它减少了使用旧版和过时的平台随着时间推移而构建的自定义开发用户预配解决方案的需求。

### <a name="licensing"></a>许可

若要将云 HR 应用配置为 Azure AD 用户预配集成，需要一个有效的[Azure AD Premium 许可证](https://azure.microsoft.com/pricing/details/active-directory/)和一个适用于 Workday 或 SuccessFactors 的云 HR 应用的许可证。

还需要一个有效的 Azure AD Premium P1 或更高版本的订阅许可证，该许可证将源自 cloud HR 应用并预配到 AD 或 Azure AD。 云 HR 应用中所有不正确的许可证数可能会导致用户预配时出现错误。

### <a name="prerequisites"></a>必备组件

- Azure AD 全局管理员访问权限来配置 Azure AD Connect 预配代理。
- Cloud HR 应用的测试和生产实例。
- Cloud HR 应用中的管理员权限，用于创建系统集成用户并进行更改，以供测试之用。
- 对于 AD 用户预配，需要运行 Windows Server 2012 或更高版本的服务器来承载[Azure AD Connect 预配代理](https://go.microsoft.com/fwlink/?linkid=847801)。
- 用于在 AD 和 Azure AD 间同步用户的[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect) 。

### <a name="training-resources"></a>定型资源

| **资源** | **链接和说明** |
|:-|:-|
| 视频 | [什么是活动 Azure 目录中的用户预配？](https://youtu.be/_ZjARPpI6NI) |
| | [如何在 Active Azure Directory 中部署用户预配？](https://youtu.be/pKzyts6kfrw) |
| 教程 | 请参阅[教程列表，了解如何将 SaaS 应用与 Azure AD 集成](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) |
| | [教程：为 Workday 配置自动用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| 常见问题 | [自动用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [从 Workday 预配到 Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>解决方案体系结构

以下示例介绍了适用于常见混合环境的端到端用户预配解决方案体系结构，其中包括：

- **权威 hr 数据流–从 CLOUD hr 应用到 AD** -在此流中，hr 事件（Joiners-离开者进程）在云 hr 应用租户中启动。 Azure AD 预配服务和 Azure AD Connect 预配代理将用户数据从云 HR 应用租户预配到 AD 中。 根据事件的不同，可能会导致在 AD 中创建/更新/启用/禁用操作。
- **与 Azure AD 并将电子邮件和用户名从本地 AD 同步到云 HR 应用**-在 AD 中更新帐户后，该帐户将通过 Azure AD Connect 与 Azure AD 同步，并且电子邮件地址和用户名属性可以写回到云 HR 应用租户。

![工作流关系图](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>工作流的说明

该图中指示的关键步骤如下：  

1. **Hr 团队**在 cloud HR app 租户中执行事务。
2. **Azure AD 预配服务**运行来自 cloud HR 应用租户的计划周期，并标识需要处理以便与 AD 同步的更改。
3. **Azure AD 预配服务**使用包含 AD 帐户创建/更新/启用/禁用操作的请求负载调用 Azure AD Connect 预配代理。
4. **Azure AD Connect 预配代理**使用服务帐户来管理 AD 帐户数据。
5. **Azure AD Connect**运行增量[同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)以获取 AD 中的更新。
6. **AD**更新与 Azure AD 同步。
7. **Azure AD 预配服务**回写电子邮件属性和用户名从 Azure AD 到云 HR 应用租户。

## <a name="plan-the-deployment-project"></a>规划部署项目

在环境中确定此部署的策略时，请考虑你的组织需求。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，它们通常是由于对影响、结果和责任的预期不匹配而导致的。 若要避免这些问题，请[确保您的利益干系人参与](https://aka.ms/deploymentplans)到项目中，并通过记录利益干系人及其项目输入和责任来充分了解项目中的利益干系人角色。

你必须在 HR 组织中包含代表，以便提供现有 HR 业务流程的输入和工作人员标识 + 作业数据处理要求。

### <a name="plan-communications"></a>规划沟通

通信对于任何新服务的成功至关重要。 你应主动与用户进行交流，了解他们的体验将如何更改，何时发生更改，以及在遇到问题时如何获取支持。

### <a name="plan-a-pilot"></a>规划试点

在将解决方案部署到生产环境之前，将 HR 业务流程和标识工作流从云 HR 应用集成到目标系统需要进行大量的数据验证、数据转换、数据清理和端到端测试。

建议在将初始配置扩展到生产环境中的所有用户之前，在[试验环境](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot)中运行它。

## <a name="select-cloud-hr-provisioning-connector-apps"></a>选择云 HR 预配连接器应用

为了便于 Azure AD 在 cloud HR 应用和 AD 之间预配工作流，可以从 Azure AD 应用库添加多个预配连接器应用：

- **CLOUD hr 应用到 AD 用户预配**–此预配连接器应用便于将云 hr 应用的用户帐户预配到单个 AD 域。 如果有多个域，则可以从需要预配到的每个 AD 域的 Azure AD 应用库中添加此应用的一个实例。
- **要 Azure AD 用户预配的云 HR 应用**程序-虽然 Azure AD Connect 是应该用于将 AD 用户与 Azure AD 进行同步的工具，但此预配连接器应用可用于帮助将仅限云的用户从 cloud HR 应用预配到单个 Azure AD 租户。
- **CLOUD hr 应用写回**-此预配连接器应用便于将用户电子邮件地址的回写从 Azure AD 到云 HR 应用。

例如，下图列出了 Azure AD 应用库中可用的 Workday 连接器应用。

![Azure Active Directory 门户应用库](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>决策流程图

使用下面的决策流程图来确定哪些 cloud HR 预配应用与你的方案相关。

![决策流程图](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>设计 Azure AD Connect 预配代理部署拓扑

Cloud HR 应用和 AD 之间的预配集成需要以下四个组件：

- Cloud HR 应用租户
- 预配连接器应用
- Azure AD Connect 预配代理
- AD 域

Azure AD Connect 预配代理部署拓扑取决于你计划集成的云 HR 应用租户和 AD 子域的数量。 如果有多个 AD 域，则依赖于 AD 域是连续的还是不[连续](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)的。

根据你的决定，选择以下部署方案之一：

- 单个云 HR 应用租户-> 目标为受信任林中的单个或多个 AD 子域
- 单个云 HR 应用租户-> 在不连续的 AD 林中针对多个子域

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>单个云 HR 应用租户-> 受信任林中的单个或多个目标 AD 子域

建议采用以下生产配置：

|要求|建议|
|:-|:-|
|要部署的 Azure AD Connect 预配代理数|2（用于高可用性和故障转移）
|要配置的预配连接器应用数|每个子域一个应用|
|用于 Azure AD Connect 设置代理的服务器主机|Windows 2012 R2 +，并向地理位置的 AD 域控制器提供线路</br>可以与 Azure AD Connect 服务共存|

![流向本地代理](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>单个云 HR 应用租户-> 在不连续的 AD 林中针对多个子域

此方案涉及如何将 cloud HR 应用的用户设置为不连续 AD 林中的域。

建议采用以下生产配置：

|要求|建议|
|:-|:-|
|要在本地部署的 Azure AD Connect 预配代理数|每个非连续 AD 林2个|
|要配置的预配连接器应用数|每个子域一个应用|
|用于 Azure AD Connect 设置代理的服务器主机|Windows 2012 R2 +，并向地理位置的 AD 域控制器提供线路</br>可以与 Azure AD Connect 服务共存|

![单个云 HR 应用租户无交集 AD 林](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect 预配代理要求

云 HR 应用到 AD 用户预配解决方案要求在运行 Windows 2012 R2 或更高版本的服务器上部署一个或多个 Azure AD Connect 预配代理，至少具有 4 GB RAM 和 .NET 4.7.1 + 运行时。 确保主机服务器具有对目标 AD 域的网络访问权限。

为准备本地环境，Azure AD Connect 预配代理配置向导将代理注册到 Azure AD 租户，[打开端口](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports)，[允许访问 url](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls)，并支持[出站 HTTPS 代理配置](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)。

预配代理使用服务帐户与 AD 域通信。 在安装代理之前，我们建议你在 AD 用户和满足以下要求的计算机中创建服务帐户：

- 不过期的密码
- 用于读取、创建、删除和管理用户帐户的委派控制权限

您可以选择应处理预配请求的域控制器。 如果有多个地理上分散的域控制器，请将设置代理安装在与首选域控制器相同的站点中，以提高端到端解决方案的可靠性和性能。

为实现高可用性，你可以部署多个 Azure AD Connect 预配代理，并注册它来处理同一组本地 AD 域。

## <a name="plan-scoping-filters-and-attribute-mapping"></a>规划范围筛选器和属性映射

当你启用从 cloud HR 应用到 AD 或 Azure AD 的预配时，Azure 门户通过属性映射来控制属性值。

### <a name="define-scoping-filters"></a>定义范围筛选器

使用[范围筛选器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters)定义基于属性的规则，这些规则确定哪些用户应该从 cloud HR 应用程序设置为 AD 或 Azure AD。

启动 Joiners 进程时，请收集以下要求：

- 云 HR 应用是否用于在员工和临时工作人员中使用？
- 你是否打算使用 cloud HR 应用 Azure AD 用户预配来管理员工和临时工作人员？
- 你是否打算推出云 HR 应用，以便仅为云 HR 应用用户的子集 Azure AD 用户预配（例如，仅限员工）？

根据你的要求，在配置属性映射时，可以将 "源对象范围" 字段设置为 "**源对象范围**" 字段，以选择云 HR 应用中的哪些用户集应在设置为 AD 的范围内。 有关常用范围筛选器，请参阅云 HR 应用教程。

### <a name="determine-matching-attributes"></a>确定匹配属性

利用预配，可以匹配源系统和目标系统之间的现有帐户。 将 Cloud HR 应用与 Azure AD 预配服务集成时，可以[配置属性映射](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings)，以确定哪些用户数据应从 Cloud HR 应用流向 AD 或 Azure AD。

启动 Joiners 进程时，请收集以下要求：

- 此 cloud HR 应用中用于识别每个用户的唯一 ID 是什么？
- 从标识生命周期角度看，如何处理 rehires？ Rehires 是否保留其旧员工 ID？
- 是否提前处理将来的招聘并为他们创建 AD 帐户？
- 从标识生命周期角度来看，如何处理员工如何进行临时工作人员转换？
- 转换后的用户是保留其旧 AD 帐户还是获得新的 AD 帐户？

根据您的需求，Azure AD 通过直接的特性到属性映射、提供常量值或[为属性映射编写表达式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings)来支持。 这种灵活性使你可以全面控制将在目标应用属性中填充的内容。 你可以使用[MICROSOFT GRAPH API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration)和图形资源管理器将用户预配属性映射和架构导出到 JSON 文件，然后将其重新导入 Azure AD。

**默认情况**下，代表唯一员工 ID 的 cloud HR 应用中的属性用作*映射到 AD 中的唯一属性*的匹配属性。 例如，在 Workday 应用程序方案中， *workday* *WorkerID*属性映射到 AD*雇员 id*属性。

你可以设置多个匹配属性并分配匹配的优先级。 它们按匹配优先级计算。 一旦找到匹配，就不会进一步评估其他匹配属性。

你还可以[自定义默认的属性映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types)（如更改或删除现有属性映射），或者根据你的业务需求创建新的属性映射。 有关要映射的自定义属性的列表，请参阅云 HR 应用教程（如[Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)）。

### <a name="determine-user-account-status"></a>确定用户帐户状态

默认情况下，预配连接器应用会将**HR 用户配置文件状态**映射到 AD/Azure AD 中的**用户帐户状态**，以确定是要启用还是禁用该用户帐户。

启动 Joiners/离开者进程时，请收集以下要求：

| 流程 | 要求 |
| - | - |
| **Joiners** | 从标识生命周期角度看，如何处理 rehires？ Rehires 是否保留其旧员工 ID？ |
| | 是否会提前处理将来的招聘，并提前为他们创建 AD 帐户？ 是否在启用/禁用状态中创建这些帐户？ |
| | 从标识生命周期角度来看，如何处理员工如何进行临时工作人员转换？ |
| | 转换后的用户是保留其旧 AD 帐户还是获得新的 AD 帐户？ |
| **离开者** | 离职的终止处理方式是否与 AD 中的员工和临时工作人员的处理方式不同？ |
| | 处理用户终止会考虑哪些生效日期？ |
| | 员工和临时工作人员转换如何影响现有 AD 帐户？ |
| | 如何处理 AD 中的 "Rescind" 操作？ 如果将来创建的招聘作为加入方过程的一部分在 AD 中创建，则需要处理 Rescind 操作。 |

根据你的要求，你可以使用[Azure AD 表达式](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)自定义映射逻辑，以便基于数据点的组合启用或禁用 AD 帐户。

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>将 cloud HR 应用映射到 AD 用户属性

每个云 HR 应用都附带了默认的 cloud HR 应用到 AD 映射。

启动 Joiners/搬运工/离开者进程时，请收集以下要求：

| 流程 | 要求 |
| - | - |
| **Joiners** | AD 帐户创建过程是手动、自动还是部分自动？ |
| | 你是否打算将自定义属性从 Cloud HR 应用传播到 AD？ |
| **搬运工** | 每当云 HR 应用中发生 "移动" 操作时，你想要处理哪些属性？ |
| | 在用户更新时是否执行任何特定属性验证？ 如果是，请提供详细信息。 |
| **离开者** | 离职的终止处理方式是否与 AD 中的员工和临时工作人员的处理方式不同？ |
| | 处理用户终止会考虑哪些生效日期？ |
| | 员工和临时工作人员转换如何影响现有 AD 帐户？ |

根据你的要求，你可以修改映射以满足你的集成目标。 有关要映射的自定义属性的列表，请参阅特定云 HR 应用教程（如[Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)）。

### <a name="generate-unique-attribute-value"></a>生成唯一属性值

启动 Joiners 进程时，可能需要在设置具有唯一约束的 CN、samAccountName 和 UPN 等属性时生成唯一属性值。

Azure AD 函数[SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue)计算每个规则，然后检查为目标系统中的唯一性生成的值。 请参阅[为 userPrincipalName （UPN）属性生成唯一值](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute)。

> [!NOTE]
> 目前只有 "Workday 才能 Active Directory 用户预配" 支持此功能。 它不能与其他预配应用一起使用。

### <a name="configure-ad-ou-container-assignment"></a>配置 AD OU 容器分配

通常需要将 AD 用户帐户放入基于业务部门、位置和部门的容器。 当你启动一个移动进程，并且如果发生监督组织更改，你可能需要将该用户从一个 OU 移动到 AD 中的另一个 OU。

使用[Switch （）](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch)函数配置 OU 分配的业务逻辑，并将其映射到 AD 属性*parentDistinguishedName*。

例如，如果要基于 HR 属性 "市政府" 在 OU 中创建用户，则可以使用以下表达式。

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

在此表达式中，如果市政府值为达拉斯、德克萨斯州、西雅图或伦敦，则将在相应的 OU 中创建用户帐户。 如果没有匹配项，则将在默认 OU 中创建帐户。

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>规划新用户帐户的密码传递

当你启动 Joiners 进程时，你将需要设置和提供新用户帐户的临时密码。 利用 cloud HR Azure AD 用户预配，你可以在第1天向用户推出 Azure AD 的[自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)（SSPR）功能。

SSPR 是 IT 管理员允许用户重置其密码或解锁其帐户的简单方式。 可以将**Mobile Number**属性从 Cloud HR app 预配到 AD，并将其与 Azure AD 同步。 Azure AD 中的 "**移动电话号码**" 属性后，可以为用户帐户启用 SSPR，以便在第1天，新用户可以使用已注册和已验证的移动电话号码进行身份验证。

## <a name="plan-for-initial-cycle"></a>规划初始周期

首次运行 Azure AD 预配服务时，它会对 cloud HR 应用执行[初始循环](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#initial-cycle)，以创建 cloud hr 应用中所有用户对象的快照。 初始周期所用的时间直接取决于源系统中有多少用户。 使用超过100000个用户的某些云 HR 应用租户的初始周期可能需要较长时间。

**对于大型云 HR 应用租户（> 30000 用户），我们建议**你在渐进阶段中运行初始循环，并仅在验证在 AD 中为不同用户预配方案设置了正确属性后启动增量更新。 按照以下顺序进行操作：

1. 通过设置[范围筛选器](#plan-scoping-filters-and-attribute-mapping)，仅为一组有限的用户运行初始周期。
2. 验证为首次运行选择的用户设置的 AD 帐户预配和属性值。 如果结果满足您的预期，请展开范围筛选器以逐步包含更多用户，并验证第二次运行的结果。

对测试用户初始周期的结果感到满意后，即可启动[增量更新](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#incremental-cycles)。

## <a name="plan-testing-and-security"></a>规划测试和安全

在部署的每个阶段，从初始试点到启用用户预配，确保测试结果是否符合预期，并审核预配周期。

### <a name="plan-testing"></a>规划测试

将 cloud HR 应用配置为 Azure AD 用户预配后，你将需要运行测试用例来验证此解决方案是否满足组织的要求。

|方案|预期结果|
|:-|:-|
|Cloud HR 应用中的新员工雇用| -在 AD 中预配用户帐户。</br>-用户可以登录到 AD-域应用程序并执行所需的操作。</br>-如果配置了 AAD Connect 同步，则还会在 Azure AD 中创建用户帐户。
|用户在云 HR 应用中终止|-用户帐户在 AD 中处于禁用状态。</br>-用户无法登录到受 AD 保护的任何企业应用。
|已在云 HR 应用中更新用户监督组织|根据属性映射，用户帐户会在 AD 中从一个 OU 移动到另一个 OU。|
|HR 更新 Cloud HR 应用中用户的经理|AD 中的 "管理器" 字段将更新以反映新经理的姓名。|
|HR rehires 将员工转换为新角色。|行为取决于云 HR 应用的配置方式，以生成员工 Id：</br>-如果为 rehires 重复使用旧雇员 ID，则连接器将为用户启用现有 AD 帐户。</br>-如果 rehires 获取新的员工 ID，则连接器将为用户创建新的 AD 帐户。|
|HR 将员工转换为合同工作人员，反之亦然|将为新角色创建新的 AD 帐户，并在转换生效日期禁用旧帐户。|

使用上述结果来确定如何根据已建立的时间线将自动用户预配实现过渡到生产。

> [!TIP]
> 当使用生产数据刷新测试环境时，我们建议使用数据缩减和数据清理等技术来删除/掩盖敏感 PII （个人身份信息），以遵守隐私和安全标准。

### <a name="plan-security"></a>规划安全性

在部署新服务的过程中，通常需要安全审查。 如果需要安全评审或尚未执行安全检查，请参阅许多 Azure AD[白皮书](https://www.microsoft.com/download/details.aspx?id=36391)，其中提供了标识即服务的概述。

### <a name="plan-rollback"></a>计划回滚

如果云 HR 用户预配实现在生产环境中不能按预期方式工作，以下回滚步骤可以帮助你恢复到以前已知的良好状态：

1. 查看[预配摘要报告](check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal)和[预配日志](check-status-user-account-provisioning.md#provisioning-logs-preview)（请参阅[管理 cloud HR 应用用户预配](#manage-your-configuration)），确定对受影响的用户和/或组执行了哪些错误操作。
2. 可以通过预配审核日志或查看目标系统（Azure AD 或 AD）来确定受影响的用户和/或组的最后已知良好状态。
3. 与应用所有者合作，使用上一次已知的良好状态值更新直接受应用影响的用户和/或组。

## <a name="deploy-the-cloud-hr-app"></a>部署 cloud HR 应用

请选择符合解决方案要求的云 HR 应用。

**Workday** -参阅[教程：为 workday 配置自动用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment)，以执行以下步骤：将工作配置文件从 WORKDAY 导入到 AD 和 Azure AD，并提供电子邮件地址的可选回写，并将用户名写入 Workday。

## <a name="manage-your-configuration"></a>管理配置

Azure AD 可以通过审核日志和报告，进一步深入了解你的组织的用户预配使用情况和运行状况。

### <a name="gain-insights-from-reports-and-logs"></a>从报表和日志获取见解

在成功完成[初始循环](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#initial-cycle)后，Azure AD 预配服务将继续按特定于每个应用的教程中定义的时间间隔，无限期地运行回回增量更新，直到发生以下事件之一：

- 手动停止该服务，并使用[Azure 门户](https://portal.azure.com/)或使用适当的[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)命令触发了新的初始周期。
- 由于属性映射或范围筛选器发生更改，将触发新的初始周期。
- 预配过程会因为错误速率较高而进入隔离状态，并且在超过四周后会自动禁用。

若要查看这些事件以及预配服务执行的所有其他活动，请[了解如何查看日志并获取有关预配活动的报告](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning)。

#### <a name="azure-monitor-logs"></a>Azure Monitor 日志

预配服务执行的所有活动都记录在**Azure AD 审核日志**中。 可以将 Azure AD 审核日志路由到 Azure Monitor 日志以便进一步分析。 通过**Azure Monitor 日志（也称为 Log Analytics 工作区）** ，您可以查询数据以查找事件、分析趋势，并跨各种数据源执行关联。 观看此[视频](https://youtu.be/MP5IaCTwkQg)，了解在实际用户方案中使用 Azure Monitor 日志 Azure AD 日志的好处。

你可以[为 Azure AD 活动日志安装 Log analytics 视图](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)，以获取有关环境中预配事件的[预构建报表](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views)。

有关详细信息，请参阅[如何分析 Azure Monitor 日志中的 Azure AD 活动日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

### <a name="manage-personal-data"></a>管理个人数据

Windows server 上安装的 Azure AD Connect 预配代理在 Windows 事件日志中创建日志，该日志可能包含个人数据，具体取决于你的云 HR 应用到 AD 属性映射。 若要遵守用户隐私义务，你可以设置 Windows 计划任务来清除事件日志，并确保不会将任何数据保留超过48小时。

Azure AD 预配服务不会生成报告、执行分析，或提供超过30天的见解。 因此，Azure AD 预配服务不存储、处理或保留 30 天以前的任何数据。 

### <a name="troubleshoot"></a>故障排除

请参阅以下链接，以解决在预配期间可能会打开的任何问题：

- [为 Azure AD 库应用程序配置用户预配时出现问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [将属性从本地 Active Directory 同步到 Azure AD 以便预配到应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [用户预配到 Azure AD 库应用程序需要数小时或更长时间](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [配置 Azure Active Directory 库应用程序的用户预配时遇到保存管理员凭据问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)
- [未将任何用户预配到 Azure AD 库应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [正在将错误的用户组预配到 Azure AD 库应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [设置 Windows 事件查看器来排查代理方面的问题](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [设置 Azure 门户审核日志来排查服务方面的问题](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [了解 AD 用户帐户创建操作的日志](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [了解经理更新操作的日志](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [解决常见错误](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>后续步骤

- [为属性映射编写表达式](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Azure AD 同步 API 概述](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [跳过删除超出范围的用户帐户](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Azure AD Connect 预配代理：版本发布历史记录](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
