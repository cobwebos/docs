---
title: 规划云 HR 应用程序以 Azure Active Directory 用户预配
description: 本文介绍将 cloud HR 系统（如 Workday 和 SuccessFactors）与 Azure Active Directory 集成的部署过程。 将 Azure AD 与云 HR 系统集成，会生成一个完整的标识生命周期管理系统。
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha, celested
ms.openlocfilehash: 86b858b628dc2ed9eac730d4c3f090f4d7d6c7e2
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593295"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>规划云 HR 应用程序以 Azure Active Directory 用户预配

从历史上看，IT 人员依赖手动方法来创建、更新和删除员工。 它们已使用上传 CSV 文件或自定义脚本来同步员工数据。 这些设置过程容易出错、不安全且难以管理。

若要管理员工、供应商或临时工作人员的标识生命周期， [Azure Active Directory （Azure AD）用户预配服务](../app-provisioning/user-provisioning.md)提供与基于云的人力资源（HR）应用程序的集成。 应用程序的示例包括 Workday 或 SuccessFactors。

Azure AD 使用此集成来启用以下 cloud HR 应用程序（应用）工作流：

- **将用户预配到 Active Directory：** 在一个或多个 Active Directory 域中，将所选用户集从云 HR 应用设置为一个或多个。
- **将仅限云的用户预配到 Azure AD：** 在未使用 Active Directory 的情况下，请直接从云 HR 应用程序中将用户设置为 Azure AD。
- **写回云 HR 应用程序：** 将电子邮件地址和用户名属性从 Azure AD 写入 cloud HR 应用。

> [!NOTE]
> 此部署计划显示了如何部署具有 Azure AD 用户预配的云 HR 应用工作流。 有关如何将自动用户预配部署到软件即服务（SaaS）应用的信息，请参阅[计划自动用户预配部署](https://aka.ms/deploymentplans/provisioning)。

## <a name="enabled-hr-scenarios"></a>启用的 HR 方案

Azure AD 用户预配服务实现了以下基于 HR 的标识生命周期管理方案的自动化：

- **新员工雇佣：** 将新员工添加到云 HR 应用时，会自动在 Active Directory 和 Azure AD 中创建用户帐户，并将电子邮件地址和用户名属性写回 cloud HR 应用。
- **员工属性和配置文件更新：** 如果在云 HR 应用中更新了 "姓名"、"标题" 或 "经理" 之类的员工记录，则会在 Active Directory 和 Azure AD 中自动更新其用户帐户。
- **员工离职：** 当员工在云 HR 应用中终止时，会自动在 Active Directory 和 Azure AD 中禁用其用户帐户。
- **员工 rehires：** 当员工 rehired 在云 HR 应用中时，可以自动将其旧帐户重新激活或重新预配到 Active Directory 和 Azure AD。

## <a name="who-is-this-integration-best-suited-for"></a>此集成最适合于哪一种？

Cloud HR 应用与 Azure AD 用户预配的集成非常适合于以下组织：

- 需要一个基于云的预构建解决方案，以进行云 HR 用户预配。
- 需要从 cloud HR 应用直接用户预配，以便 Active Directory 或 Azure AD。
- 要求使用从云 HR 应用获取的数据设置用户。
- 需要根据在云 HR 应用中检测到的更改信息，加入、移动和离开用户以同步到一个或多个 Active Directory 林、域和 Ou。
- 使用 Office 365 发送电子邮件。

## <a name="learn"></a>了解

用户预配为正在进行的标识管理创建基础。 它增强了依赖于权威标识数据的业务流程的质量。

### <a name="terms"></a>术语

本文使用以下术语：

- **源系统**： Azure AD 预配的用户的存储库。 例如，Workday 或 SuccessFactors 等云 HR 应用。
- **目标系统**： Azure AD 预配到的用户的存储库。 例如 Active Directory、Azure AD、Office 365 或其他 SaaS 应用程序。
- **Joiners-离开者 process**：使用云 HR 应用作为记录的一种术语，用于新员工、转移和终止。 当服务成功地将所需的属性预配到目标系统时，该过程完成。

### <a name="key-benefits"></a>主要优点

HR 驱动的 IT 预配的这一功能提供以下重要的业务优势：

- **提高工作效率：** 你现在可以自动分配用户帐户和 Office 365 许可证，并提供对密钥组的访问权限。 自动分配允许新员工立即访问其作业工具并提高工作效率。
- **管理风险：** 你可以通过基于员工状态或从云 HR 应用中流入数据的组成员身份自动执行更改，从而提高安全性。 自动更改可确保用户的标识和访问密钥应用在用户转换或离开组织时自动更新。
- **解决合规性和监管：** Azure AD 支持由源系统和目标系统的应用执行的用户预配请求的本地审核日志。 通过审核，您可以在单个屏幕上跟踪谁有权访问应用程序。
- **管理成本：** 通过避免与手动预配相关的低效和人为错误，自动预配可降低成本。 它通过使用旧版和过时的平台减少了随时间而构建的自定义开发用户预配解决方案的需求。

### <a name="licensing"></a>授权

若要将云 HR 应用配置为 Azure AD 用户预配集成，需要一个有效的[Azure AD Premium 许可证](https://azure.microsoft.com/pricing/details/active-directory/)和一个云 HR 应用的许可证，如 Workday 或 SuccessFactors。

还需要一个有效的 Azure AD Premium P1 或更高版本的订阅许可证，该许可证将源自 cloud HR 应用并设置为 Active Directory 或 Azure AD。 云 HR 应用中拥有的任何不正确的许可证数可能会导致用户预配时出现错误。

### <a name="prerequisites"></a>先决条件

- Azure AD 全局管理员访问权限来配置 Azure AD Connect 预配代理。
- Cloud HR 应用的测试和生产实例。
- Cloud HR 应用中的管理员权限，用于创建系统集成用户并进行更改，以供测试之用。
- 要 Active Directory 的用户预配，需要运行 Windows Server 2012 或更高版本的服务器来承载[Azure AD Connect 预配代理](https://go.microsoft.com/fwlink/?linkid=847801)。
- 用于在 Active Directory 和 Azure AD 之间同步用户的[Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) 。

### <a name="training-resources"></a>培训资源

| **资源** | **链接和说明** |
|:-|:-|
| 视频 | [什么是活动 Azure 目录中的用户预配？](https://youtu.be/_ZjARPpI6NI) |
| | [如何在 Active Azure Directory 中部署用户预配](https://youtu.be/pKzyts6kfrw) |
| 教程 | [有关如何将 SaaS 应用与 Azure AD 集成的教程列表](../saas-apps/tutorial-list.md) |
| | [教程：为 Workday 配置自动用户预配](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| 常见问题解答 | [自动用户预配](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [从 Workday 预配到 Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>解决方案体系结构

以下示例介绍了适用于常见混合环境的端到端用户预配解决方案体系结构，其中包括：

- **从 cloud HR 应用到 Active Directory 的权威 HR 数据流。** 在此流中，HR 事件（Joiners-离开者进程）在云 HR 应用租户中启动。 Azure AD 预配服务和 Azure AD Connect 预配代理将用户数据从云 HR 应用租户预配到 Active Directory 中。 根据事件，可能会导致在 Active Directory 中创建、更新、启用和禁用操作。
- **与 Azure AD 同步，并将本地 Active Directory 中的电子邮件和用户名写回云 HR 应用。** Active Directory 中的帐户更新后，它将通过 Azure AD Connect 与 Azure AD 同步。 电子邮件地址和用户名属性可以写回到云 HR 应用租户。

![工作流关系图](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>工作流的说明

图中指示了以下关键步骤：  

1. **Hr 团队**在 cloud HR app 租户中执行事务。
2. **Azure AD 预配服务**运行来自 cloud HR 应用租户的计划周期，并标识需要处理以便与 Active Directory 同步的更改。
3. **Azure AD 预配服务**使用包含 Active Directory 帐户创建、更新、启用和禁用操作的请求负载调用 Azure AD Connect 预配代理。
4. **Azure AD Connect 预配代理**使用服务帐户来管理 Active Directory 帐户数据。
5. **Azure AD Connect**在 Active Directory 中运行增量[同步](../hybrid/how-to-connect-sync-whatis.md)以获取更新。
6. **Active Directory**更新与 Azure AD 同步。
7. **Azure AD 预配服务**，将电子邮件属性和用户名从 Azure AD 部署到云 HR 应用租户。

## <a name="plan-the-deployment-project"></a>规划部署项目

在环境中确定此部署的策略时，请考虑组织的需求。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，他们通常会由于对影响、结果和责任的不匹配期望。 若要避免这些问题，请[确保您参与到了正确的利益干系人](https://aka.ms/deploymentplans)。 此外，请确保项目中的利益干系人角色非常熟悉。 记录利益干系人及其项目输入和责任。

包括 HR 组织的代表，可以提供现有 HR 业务流程的输入和工作人员标识以及作业数据处理要求。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功至关重要。 主动与用户交流其体验的发生时间和方式。 如果遇到问题，请让他们知道如何获得支持。

### <a name="plan-a-pilot"></a>规划试点

将 HR 业务流程和标识工作流从 cloud HR 应用集成到目标系统需要进行大量的数据验证、数据转换、数据清理和端到端测试，然后才能将解决方案部署到生产环境。

请先在[试点环境](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)中运行初始配置，然后再将其扩展到生产环境中的所有用户。

## <a name="select-cloud-hr-provisioning-connector-apps"></a>选择云 HR 预配连接器应用

为了便于 Azure AD 在 cloud HR 应用和 Active Directory 之间预配工作流，你可以从 Azure AD 应用库添加多个预配连接器应用：

- **要 Active Directory 用户预配的 CLOUD hr 应用**：此预配连接器应用可帮助将云 hr 应用的用户帐户预配到单个 Active Directory 域。 如果你有多个域，可以针对需要预配到的每个 Active Directory 域，从 Azure AD 应用库添加此应用的一个实例。
- **要 Azure AD 用户预配的云 HR 应用程序**：虽然 Azure AD Connect 是应该用于将 Active Directory 用户同步到 Azure AD 的工具，但此预配连接器应用可用于帮助将仅限云的用户从 cloud HR 应用程序预配到单个 Azure AD 租户。
- **CLOUD hr 应用回写**：此预配连接器应用便于将用户电子邮件地址的回写从 Azure AD 到云 HR 应用。

例如，下图列出了 Azure AD 应用库中可用的 Workday 连接器应用。

![Azure Active Directory 门户应用库](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>决策流程图

使用以下决策流程图确定哪些云 HR 预配应用与你的方案相关。

![决策流程图](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>设计 Azure AD Connect 预配代理部署拓扑

Cloud HR 应用和 Active Directory 之间的预配集成需要四个组件：

- Cloud HR 应用租户
- 预配连接器应用
- Azure AD Connect 预配代理
- Active Directory 域

Azure AD Connect 预配代理部署拓扑取决于你计划集成的云 HR 应用租户和 Active Directory 子域的数量。 如果有多个 Active Directory 域，它取决于 Active Directory 域是连续的还是非[连续](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)的。

根据你的决定，选择以下部署方案之一：

- 单个云 HR 应用租户-> 目标为受信任林中的单个或多个 Active Directory 子域
- 单个云 HR 应用租户-> 在非连续 Active Directory 林中具有多个子域

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>单个云 HR 应用租户-> 目标为受信任林中的单个或多个 Active Directory 子域

建议采用以下生产配置：

|要求|建议|
|:-|:-|
|要部署的 Azure AD Connect 预配代理数|2（用于高可用性和故障转移）
|要配置的预配连接器应用数|每个子域一个应用|
|用于 Azure AD Connect 设置代理的服务器主机|Windows 2012 R2 + 向 geolocated Active Directory 域控制器提供线路</br>可以与 Azure AD Connect 服务共存|

![流向本地代理](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>单个云 HR 应用租户-> 在非连续 Active Directory 林中具有多个子域

此方案涉及如何将 cloud HR 应用中的用户预配到不连续 Active Directory 林中的域。

建议采用以下生产配置：

|要求|建议|
|:-|:-|
|要在本地部署的 Azure AD Connect 预配代理数|每个非连续 Active Directory 林2个|
|要配置的预配连接器应用数|每个子域一个应用|
|用于 Azure AD Connect 设置代理的服务器主机|Windows 2012 R2 + 向 geolocated Active Directory 域控制器提供线路</br>可以与 Azure AD Connect 服务共存|

![单个云 HR 应用租户非连续 Active Directory 林](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect 预配代理要求

要 Active Directory 用户预配解决方案的 cloud HR 应用需要在运行 Windows 2012 R2 或更高版本的服务器上部署一个或多个 Azure AD Connect 设置代理。 服务器必须至少具有 4 GB RAM 和 .NET 4.7.1 + 运行时。 请确保主机服务器具有对目标 Active Directory 域的网络访问权限。

为准备本地环境，Azure AD Connect 预配代理配置向导将代理注册到 Azure AD 租户，[打开端口](../manage-apps/application-proxy-add-on-premises-application.md#open-ports)，[允许访问 url](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)，并支持[出站 HTTPS 代理配置](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)。

预配代理使用服务帐户与 Active Directory 域通信。 在安装代理之前，请在 Active Directory 满足以下要求的用户和计算机中创建服务帐户：

- 不过期的密码
- 用于读取、创建、删除和管理用户帐户的委派控制权限

您可以选择应处理预配请求的域控制器。 如果有多个地理上分散的域控制器，请将设置代理安装在与首选域控制器相同的站点中。 此位置可提高端到端解决方案的可靠性和性能。

为实现高可用性，可以部署多个 Azure AD Connect 设置代理。 注册代理以处理同一组本地 Active Directory 域。

## <a name="plan-scoping-filters-and-attribute-mapping"></a>规划范围筛选器和属性映射

当你启用从云 HR 应用进行预配以便 Active Directory 或 Azure AD 时，Azure 门户通过属性映射来控制属性值。

### <a name="define-scoping-filters"></a>定义范围筛选器

使用[范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)定义基于属性的规则，这些规则确定哪些用户应该从云 HR 应用程序中进行设置，以便 Active Directory 或 Azure AD。

启动 Joiners 进程时，请收集以下要求：

- 云 HR 应用是否用于使员工和临时工作人员都可以使用？
- 你是否打算使用 cloud HR 应用 Azure AD 用户预配来管理员工和临时工作人员？
- 你是否打算推出云 HR 应用，以便仅为云 HR 应用用户的子集 Azure AD 用户预配？ 例如，可能只是雇员。

根据你的要求，在配置属性映射时，可以将 "源对象范围" 字段设置为 "**源对象范围**" 字段，以选择云 HR 应用中的哪些用户集应在设置为 Active Directory 的范围内。 有关详细信息，请参阅适用于常用范围筛选器的云 HR 应用教程。

### <a name="determine-matching-attributes"></a>确定匹配属性

利用预配，可以匹配源系统和目标系统之间的现有帐户。 将 cloud HR 应用与 Azure AD 预配服务集成时，可以[配置属性映射](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings)，以确定应将哪些用户数据从云 HR 应用流到 Active Directory 或 Azure AD。

启动 Joiners 进程时，请收集以下要求：

- 此 cloud HR 应用中用于识别每个用户的唯一 ID 是什么？
- 从标识生命周期角度看，如何处理 rehires？ Rehires 是否保留其旧的员工 Id？
- 您是否会提前处理日后招聘的员工并提前为他们创建 Active Directory 帐户？
- 从标识生命周期角度来看，如何处理员工如何进行临时工作人员转换？
- 转换后的用户是否保留旧的 Active Directory 帐户，或者是否获取新帐户？

根据您的要求，Azure AD 通过提供常量值或[为属性映射编写表达式来](../app-provisioning/functions-for-customizing-application-data.md)支持直接的特性到属性映射。 这种灵活性使你可以全面控制在目标应用属性中填充的内容。 你可以使用[MICROSOFT GRAPH API](../app-provisioning/export-import-provisioning-configuration.md)和图形资源管理器将用户预配属性映射和架构导出到 JSON 文件，然后将其导入回 Azure AD。

默认情况下，代表唯一员工 ID 的 cloud HR 应用中的属性用作*映射到 Active Directory 中的唯一属性*的匹配属性。 例如，在 Workday 应用程序方案中， **workday** **WorkerID**属性映射到 Active Directory**雇员 id**属性。

你可以设置多个匹配属性并分配匹配的优先级。 它们按匹配优先级计算。 一旦找到匹配，就不会进一步评估其他匹配属性。

您还可以[自定义默认属性映射](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)，例如更改或删除现有属性映射。 你还可以根据业务需求创建新的属性映射。 有关详细信息，请参阅 cloud HR app 教程（如[Workday](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)），了解要映射的自定义属性列表。

### <a name="determine-user-account-status"></a>确定用户帐户状态

默认情况下，预配连接器应用会将 HR 用户配置文件状态映射到 Active Directory 或 Azure AD 中的用户帐户状态，以确定是要启用还是禁用该用户帐户。

启动 Joiners-离开者进程时，请收集以下要求。

| 进程 | 要求 |
| - | - |
| **Joiners** | 从标识生命周期角度看，如何处理 rehires？ Rehires 是否保留其旧的员工 Id？ |
| | 您是否会提前处理日后招聘的员工并提前为他们创建 Active Directory 帐户？ 这些帐户是否创建为启用或禁用状态？ |
| | 从标识生命周期角度来看，如何处理员工如何进行临时工作人员转换？ |
| | 转换后的用户是否保留旧的 Active Directory 帐户，或者是否获取新帐户？ |
| **离开者** | Active Directory 中的员工和临时工作人员的终止处理方式是否不同？ |
| | 处理用户终止会考虑哪些生效日期？ |
| | 员工和临时工作人员转换如何影响现有 Active Directory 帐户？ |
| | 如何处理 Active Directory 中的 Rescind 操作？ 如果在 Active Directory 中创建将来的招聘，则需要处理 Rescind 操作。 |

根据您的需求，您可以使用[Azure AD 表达式](../app-provisioning/functions-for-customizing-application-data.md)自定义映射逻辑，以便基于数据点的组合启用或禁用 Active Directory 帐户。

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>将 cloud HR 应用映射到 Active Directory 用户属性

每个云 HR 应用都附带了默认的云 HR 应用来 Active Directory 映射。

启动 Joiners-离开者进程时，请收集以下要求。

| 进程 | 要求 |
| - | - |
| **Joiners** | Active Directory 帐户创建过程是手动、自动还是部分自动？ |
| | 你是否打算将自定义属性从 cloud HR 应用传播到 Active Directory？ |
| **搬运工** | 当移动操作在云 HR 应用中发生时，你想要处理哪些属性？ |
| | 在用户更新时是否执行任何特定属性验证？ 如果是，请提供详细信息。 |
| **离开者** | Active Directory 中的员工和临时工作人员的终止处理方式是否不同？ |
| | 处理用户终止会考虑哪些生效日期？ |
| | 员工和临时工作人员转换如何影响现有 Active Directory 帐户？ |

根据你的要求，你可以修改映射以满足你的集成目标。 有关详细信息，请参阅特定云 HR 应用教程（如[Workday](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)），了解要映射的自定义属性列表。

### <a name="generate-a-unique-attribute-value"></a>生成唯一属性值

当你启动 Joiners 进程时，你可能需要在设置具有唯一约束的 CN、samAccountName 和 UPN 等属性时生成唯一属性值。

Azure AD 函数[SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)计算每个规则，然后检查为目标系统中的唯一性生成的值。 有关示例，请参阅为[userPrincipalName （UPN）特性生成唯一值](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)。

> [!NOTE]
> 目前只有 Workday 支持此功能才能 Active Directory 用户预配。 它不能与其他预配应用一起使用。

### <a name="configure-active-directory-ou-container-assignment"></a>配置 Active Directory OU 容器分配

这是一个常见的要求，是将用户帐户 Active Directory 到基于业务部门、位置和部门的容器中。 启动移动进程时，如果存在监督的组织更改，则可能需要在 Active Directory 中将用户从一个 OU 移动到另一个 OU。

使用[Switch （）](../app-provisioning/functions-for-customizing-application-data.md#switch)函数配置 OU 分配的业务逻辑，并将其映射到 Active Directory 属性**parentDistinguishedName**。

例如，如果要基于 HR 属性**市政府**在 OU 中创建用户，可以使用以下表达式：

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

在此表达式中，如果市政府值为达拉斯、德克萨斯州、西雅图或伦敦，则将在相应的 OU 中创建用户帐户。 如果没有匹配项，则会在默认 OU 中创建帐户。

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>规划新用户帐户的密码传递

启动 Joiners 进程时，需要设置和传递新用户帐户的临时密码。 利用 cloud HR Azure AD 用户预配，你可以在第一天向用户推出 Azure AD 的[自助服务密码重置](../authentication/quickstart-sspr.md)（SSPR）功能。

SSPR 是 IT 管理员允许用户重置其密码或解锁其帐户的简单方式。 你可以从云 HR 应用程序设置**移动电话号码**属性，以便 Active Directory 并将其与 Azure AD 同步。 Azure AD 中的 "**移动电话号码**" 属性后，可以为用户的帐户启用 "SSPR"。 然后，在第一天，新用户可以使用已注册和已验证的移动电话号码进行身份验证。

## <a name="plan-for-initial-cycle"></a>规划初始周期

首次运行 Azure AD 预配服务时，它会对 cloud HR 应用执行[初始循环](../app-provisioning/how-provisioning-works.md#initial-cycle)，以创建 cloud hr 应用中所有用户对象的快照。 初始周期所用的时间直接取决于源系统中有多少用户。 使用超过100000个用户的某些云 HR 应用租户的初始周期可能需要较长时间。

**对于大型云 HR 应用租户（>30000 用户），请**在渐进阶段中运行初始周期。 仅在验证在不同用户预配方案 Active Directory 中设置正确属性后，才启动增量更新。 遵循此处的顺序。

1. 通过设置[范围筛选器](#plan-scoping-filters-and-attribute-mapping)，仅为一组有限的用户运行初始周期。
2. 验证 Active Directory 帐户设置以及为首次运行选择的用户设置的属性值。 如果结果满足您的预期，请展开范围筛选器以逐步包含更多用户，并验证第二次运行的结果。

对测试用户初始周期的结果感到满意后，开始[增量更新](../app-provisioning/how-provisioning-works.md#incremental-cycles)。

## <a name="plan-testing-and-security"></a>规划测试和安全

在部署的每个阶段，从初始试点到启用用户预配，确保测试结果是否符合预期并审核预配周期。

### <a name="plan-testing"></a>规划测试

将 cloud HR 应用配置为 Azure AD 用户预配后，运行测试用例以验证此解决方案是否满足组织的要求。

|方案|预期结果|
|:-|:-|
|在云 HR 应用中雇用新员工。| -在 Active Directory 中设置用户帐户。</br>-用户可以登录 Active Directory 域应用并执行所需的操作。</br>-如果已配置 Azure AD Connect 同步，则还会在 Azure AD 中创建用户帐户。
|用户在云 HR 应用中终止。|-Active Directory 中禁用该用户帐户。</br>-用户无法登录到受 Active Directory 保护的任何企业应用。
|用户监督的组织已在云 HR 应用中更新。|根据属性映射，用户帐户会在 Active Directory 中从一个 OU 移动到另一个 OU。|
|HR 更新了 cloud HR 应用中用户的经理。|Active Directory 中的 "管理器" 字段将更新以反映新经理的姓名。|
|HR rehires 将员工转换为新角色。|行为取决于云 HR 应用的配置方式，以生成员工 Id：</br>-如果为 rehire 重复使用旧雇员 ID，则连接器将为用户启用现有的 Active Directory 帐户。</br>-如果 rehire 获取新的员工 ID，连接器将为用户创建新的 Active Directory 帐户。|
|HR 将员工转换为合同工作人员，反之亦然。|将为新角色创建新的 Active Directory 帐户，并在转换生效日期禁用旧帐户。|

使用以前的结果来确定如何根据已建立的时间线将自动用户预配实现过渡到生产。

> [!TIP]
> 使用生产数据刷新测试环境时，使用数据缩减和数据清理等技术来删除或掩盖敏感的个人数据，以遵守隐私和安全标准。 

### <a name="plan-security"></a>规划安全性

在部署新服务的过程中，通常需要安全审查。 如果需要安全评审或未执行安全检查，请参阅许多 Azure AD 白皮书，这些[白皮书](https://www.microsoft.com/download/details.aspx?id=36391)提供标识即服务的概述。

### <a name="plan-rollback"></a>规划回滚

云 HR 用户预配实现在生产环境中可能无法正常工作。 如果是这样，以下回滚步骤可以帮助您恢复到以前已知的良好状态。

1. 查看[预配摘要报告](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal)和[预配日志](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)，以确定对受影响的用户或组执行了哪些操作不正确。 有关预配摘要报告和日志的详细信息，请参阅[管理 CLOUD HR 应用用户预配](#manage-your-configuration)。
2. 受影响的用户或组的最后已知良好状态可以通过预配审核日志或查看目标系统（Azure AD 或 Active Directory）来确定。
3. 通过使用上一次已知的良好状态值，与应用所有者合作来更新直接受影响的用户或组。

## <a name="deploy-the-cloud-hr-app"></a>部署 cloud HR 应用

选择符合解决方案要求的云 HR 应用。

**Workday**：若要将工作线程配置文件从 Workday 导入 Active Directory 和 Azure AD，请参阅[教程：为自动用户预配配置 Workday](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)。 您也可以将电子邮件地址和用户名写回到 Workday。

## <a name="manage-your-configuration"></a>管理配置

Azure AD 可以通过审核日志和报告，进一步深入了解你的组织的用户预配使用情况和运行状况。

### <a name="gain-insights-from-reports-and-logs"></a>从报表和日志获取见解

在成功完成[初始周期](../app-provisioning/how-provisioning-works.md#initial-cycle)后，Azure AD 预配服务将继续按特定于每个应用的教程中定义的时间间隔，按特定于每个应用的时间间隔持续运行回回增量更新，直到发生以下事件之一：

- 已手动停止此服务。 使用[Azure 门户](https://portal.azure.com/)或适当的[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)命令触发新初始周期。
- 由于属性映射或范围筛选器的更改时，将触发新的初始周期。
- 由于较高的错误率，预配过程将进入隔离区。 它保持隔离时间超过四周，此时自动禁用。

若要查看这些事件以及预配服务执行的所有其他活动，请[了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)。

#### <a name="azure-monitor-logs"></a>Azure Monitor 日志

预配服务执行的所有活动都记录在 Azure AD 审核日志中。 可以将 Azure AD 审核日志路由到 Azure Monitor 日志以便进一步分析。 利用 Azure Monitor 日志（也称为 Log Analytics 工作区），您可以查询数据以查找事件、分析趋势，并跨各种数据源执行关联。 观看此[视频](https://youtu.be/MP5IaCTwkQg)，了解在实际用户方案中使用 Azure Monitor 日志 Azure AD 日志的好处。

安装[Azure AD 活动日志的 log analytics 视图](../reports-monitoring/howto-install-use-log-analytics-views.md)，以获取有关环境中预配事件的预[构建报表](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views)。

有关详细信息，请参阅如何[分析 Azure Monitor 日志中的 Azure AD 活动日志](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)。

### <a name="manage-personal-data"></a>管理个人数据

Windows server 上安装的 Azure AD Connect 预配代理在 Windows 事件日志中创建日志，该日志可能包含个人数据，具体取决于你的云 HR 应用程序 Active Directory 属性映射。 若要遵守用户隐私义务，请设置 Windows 计划任务以清除事件日志，并确保不会将任何数据保留超过48小时。

Azure AD 预配服务不会生成报表、执行分析，也不会在30天内提供见解，因为服务不会存储、处理或保留超过30天的任何数据。

### <a name="troubleshoot"></a>疑难解答

若要解决在预配期间可能会打开的任何问题，请参阅以下文章：

- [为 Azure AD 库应用程序配置用户预配时遇到的问题](application-provisioning-config-problem.md)
- [将属性从本地 Active Directory 同步到 Azure AD 以便预配到应用程序](user-provisioning-sync-attributes-for-mapping.md)
- [用户预配到 Azure AD 库应用程序需要数小时或更长时间](application-provisioning-when-will-provisioning-finish.md)
- [在为 Azure Active Directory 库应用程序配置用户预配时保存管理员凭据时出现问题](application-provisioning-config-problem-storage-limit.md)
- [没有为任何用户预配 Azure AD 库应用程序](application-provisioning-config-problem-no-users-provisioned.md)
- [预配到 Azure AD 库应用程序的用户组错误](application-provisioning-config-problem-wrong-users-provisioned.md)
- [设置 Windows 事件查看器来排查代理方面的问题](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [设置 Azure 门户审核日志来排查服务方面的问题](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [了解 AD 用户帐户创建操作的日志](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [了解管理器更新操作的日志](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [解决常见错误](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>后续步骤

- [为属性映射编写表达式](functions-for-customizing-application-data.md)
- [Azure AD 同步 API 概述](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [跳过删除超出范围的用户帐户](skip-out-of-scope-deletions.md)
- [Azure AD Connect 预配代理：版本发布历史记录](provisioning-agent-release-version-history.md)
