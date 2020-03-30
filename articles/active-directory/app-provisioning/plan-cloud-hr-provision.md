---
title: 将云 HR 应用程序规划到 Azure 活动目录用户预配
description: 本文介绍了将云 HR 系统（如工作日和成功因素）与 Azure 活动目录集成的部署过程。 将 Azure AD 与云 HR 系统集成，可建立完整的身份生命周期管理系统。
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522426"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>将云 HR 应用程序规划到 Azure 活动目录用户预配

从历史上看，IT 员工一直依靠手动方法来创建、更新和删除员工。 他们使用了上传 CSV 文件或自定义脚本等方法来同步员工数据。 这些预配过程容易出错、不安全且难以管理。

为了管理员工、供应商或临时工作人员的身份生命周期[，Azure Active Directory （Azure AD） 用户预配服务](../app-provisioning/user-provisioning.md)提供与基于云的人力资源 （HR） 应用程序的集成。 应用程序的示例包括工作日或成功因素。

Azure AD 使用此集成启用以下云 HR 应用程序（应用）工作流：

- **将用户预配到活动目录：** 将所选用户集从云 HR 应用预配到一个或多个活动目录域中。
- **将仅云用户预配到 Azure AD：** 在不使用 Active Directory 的情况下，将用户直接从云 HR 应用预配到 Azure AD。
- **写回云 HR 应用：** 将 Azure AD 的电子邮件地址和用户名属性写回云 HR 应用。

> [!NOTE]
> 此部署计划演示如何使用 Azure AD 用户预配部署云 HR 应用工作流。 有关如何将自动用户预配部署到软件即服务 （SaaS） 应用的信息，请参阅[计划自动用户预配部署](https://aka.ms/deploymentplans/provisioning)。

## <a name="enabled-hr-scenarios"></a>启用 HR 方案

Azure AD 用户预配服务支持以下基于 HR 的身份生命周期管理方案的自动化：

- **新员工招聘：** 将新员工添加到云 HR 应用时，将自动在 Active Directory 和 Azure AD 中创建用户帐户，并可以选择将电子邮件地址和用户名属性写回云 HR 应用。
- **员工属性和配置文件更新：** 在云 HR 应用中更新员工记录（如姓名、职务或经理）时，其用户帐户将自动在 Active Directory 和 Azure AD 中更新。
- **员工解雇：** 当员工在云 HR 应用中终止时，其用户帐户将自动在活动目录和 Azure AD 中禁用。
- **员工重新雇用：** 在云 HR 应用中重新雇用员工时，可以自动重新激活其旧帐户或重新预配到活动目录和 Azure AD。

## <a name="who-is-this-integration-best-suited-for"></a>这种集成最适合谁？

与 Azure AD 用户预配的云 HR 应用集成非常适合以下组织：

- 想要一个用于云 HR 用户预配的基于云的预构建解决方案。
- 需要从云 HR 应用直接用户预配到活动目录或 Azure AD。
- 要求使用从云 HR 应用获取的数据预配用户。
- 要求仅根据云 HR 应用中检测到的更改信息将用户连接到一个或多个活动目录林、域和 O。
- 使用 Office 365 进行电子邮件。

## <a name="learn"></a>了解

用户预配为持续的身份治理奠定了基础。 它增强了依赖于权威标识数据的业务流程的质量。

### <a name="terms"></a>术语

本文使用以下术语：

- **源系统**：Azure AD 提供的用户的存储库。 例如云 HR 应用，如工作日或成功因素。
- **目标系统**：Azure AD 规定的用户存储库。 示例包括活动目录、Azure AD、Office 365 或其他 SaaS 应用。
- **加入者-移动器-离开者流程**：使用云 HR 应用作为记录系统，用于新员工、转移和终止的术语。 当服务成功向目标系统提供必要的属性时，该过程将完成。

### <a name="key-benefits"></a>主要优点

这种 HR 驱动的 IT 配置功能提供了以下重大业务优势：

- **提高生产率：** 您现在可以自动分配用户帐户和 Office 365 许可证，并提供对关键组的访问。 自动化作业使新员工能够立即访问他们的工作工具并提高工作效率。
- **管理风险：** 您可以通过根据员工状态或组成员身份（数据从云 HR 应用流入）自动进行更改来提高安全性。 自动更改可确保用户身份和对关键应用的访问在用户过渡或离开组织时自动更新。
- **解决合规性和治理问题：** Azure AD 支持本机审核日志，用于由源系统和目标系统的应用执行的用户预配请求。 通过审核，您可以跟踪谁可以从单个屏幕访问应用。
- **管理成本：** 自动预配通过避免与手动预配相关的效率低下和人为错误来降低成本。 它通过使用旧版和过时的平台，减少了对长期构建的自定义开发的用户预配解决方案的需求。

### <a name="licensing"></a>授权

要将云 HR 应用配置为 Azure AD 用户预配集成，需要有效的[Azure AD 高级许可证](https://azure.microsoft.com/pricing/details/active-directory/)和云 HR 应用（如工作日或成功因素）的许可证。

对于将从云 HR 应用获取并预配到活动目录或 Azure AD 的每个用户，还需要有效的 Azure AD 高级 P1 或更高订阅许可证。 云 HR 应用中拥有的任何不当数量的许可证都可能导致用户预配过程中的错误。

### <a name="prerequisites"></a>先决条件

- Azure AD 全局管理员访问以配置 Azure AD 连接预配代理。
- 云 HR 应用的测试和生产实例。
- 云 HR 应用中的管理员权限，用于创建系统集成用户并更改以测试员工数据以进行测试。
- 对于用户预配到活动目录，运行 Windows Server 2012 或更高版本具有 .NET 4.7.1+ 运行时的服务器需要承载[Azure AD Connect 预配代理](https://go.microsoft.com/fwlink/?linkid=847801)。
- [Azure AD 连接](../hybrid/whatis-azure-ad-connect.md)用于在活动目录和 Azure AD 之间同步用户。

### <a name="training-resources"></a>培训资源

| **资源** | **链接和说明** |
|:-|:-|
| 视频 | [活动 Azure 目录中的用户预配是什么？](https://youtu.be/_ZjARPpI6NI) |
| | [如何在活动 Azure 目录中部署用户预配](https://youtu.be/pKzyts6kfrw) |
| 教程 | [有关如何将 SaaS 应用与 Azure AD 集成的教程列表](../saas-apps/tutorial-list.md) |
| | [教程：为 Workday 配置自动用户预配](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| FAQ | [自动用户预配](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [从工作日预配到 Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>解决方案体系结构

以下示例描述了常见混合环境的端到端用户预配解决方案体系结构，包括：

- **从云 HR 应用到活动目录的权威 HR 数据流。** 在此流中，HR 事件（连接器-移动器-离开者进程）在云 HR 应用租户中启动。 Azure AD 预配服务和 Azure AD 连接预配代理将云 HR 应用租户的用户数据预配到活动目录。 根据事件，它可能导致在 Active Directory 中创建、更新、启用和禁用操作。
- **与 Azure AD 同步，并将电子邮件和用户名从本地活动目录写回云 HR 应用。** 在活动目录中更新帐户后，它将通过 Azure AD 连接与 Azure AD 同步。 电子邮件地址和用户名属性可以写回云 HR 应用租户。

![工作流关系图](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>工作流描述

下图中指明了以下关键步骤：  

1. **HR 团队**在云 HR 应用租户中执行事务。
2. **Azure AD 预配服务**从云 HR 应用租户运行计划周期，并标识需要处理的更改才能与 Active Directory 同步。
3. **Azure AD 预配服务**调用 Azure AD Connect 预配代理，其请求有效负载包含活动目录帐户创建、更新、启用和禁用操作。
4. **Azure AD 连接预配代理**使用服务帐户来管理活动目录帐户数据。
5. **Azure AD 连接**运行增量[同步](../hybrid/how-to-connect-sync-whatis.md)以在活动目录中提取更新。
6. **活动目录**更新与 Azure AD 同步。
7. **Azure AD 预配服务**将电子邮件属性和用户名从 Azure AD 写入云 HR 应用租户。

## <a name="plan-the-deployment-project"></a>规划部署项目

在确定环境中此部署的策略时，请考虑组织需求。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，它们通常是由于对影响、结果和责任的期望不匹配而达到这一要求。 为了避免这些陷阱[，请确保您与合适的利益相关者接洽](https://aka.ms/deploymentplans)。 还要确保项目中的利益相关者角色得到充分理解。 记录利益相关者及其项目投入和责任。

包括人力资源组织的代表，他可以就现有 HR 业务流程和员工身份以及作业数据处理要求提供意见。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功至关重要。 主动与用户沟通，了解其体验何时和如何变化。 让他们知道，如果他们遇到问题，如何获得支持。

### <a name="plan-a-pilot"></a>规划试点

将 HR 业务流程和身份工作流从云 HR 应用集成到目标系统需要大量数据验证、数据转换、数据清理和端到端测试，然后才能将解决方案部署到生产中。

在将初始配置扩展到生产中的所有用户之前，在[试验环境中](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)运行初始配置。

## <a name="select-cloud-hr-provisioning-connector-apps"></a>选择云 HR 预配连接器应用

为了方便 Azure AD 预配云 HR 应用和活动目录之间的工作流，可以从 Azure AD 应用库中添加多个预配连接器应用：

- **云 HR 应用到活动目录用户预配**：此预配连接器应用有助于将用户帐户从云 HR 应用预配到单个活动目录域。 如果你有多个域，可以针对需要预配到的每个 Active Directory 域，从 Azure AD 应用库添加此应用的一个实例。
- **云 HR 应用到 Azure AD 用户预配**：虽然 Azure AD 连接是应该用于将 Active Directory 用户同步到 Azure AD 的工具，但此预配连接器应用可用于促进将仅云用户从云 HR 应用预配到单个 Azure AD 租户。
- **云 HR 应用写回**：此预配连接器应用便于将用户的电子邮件地址从 Azure AD 写回云 HR 应用。

例如，下图列出了 Azure AD 应用库中可用的工日连接器应用。

![Azure 活动目录门户应用库](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>决策流程图

使用以下决策流程图确定哪些云 HR 预配应用与您的方案相关。

![决策流程图](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>设计 Azure AD 连接预配代理部署拓扑

云 HR 应用和活动目录之间的预配集成需要四个组件：

- 云 HR 应用租户
- 预配连接器应用
- Azure AD 连接预配代理
- Active Directory 域

Azure AD Connect 预配代理部署拓扑取决于计划集成的云 HR 应用租户和活动目录子域的数量。 如果您有多个活动目录域，则取决于活动目录域是连续的还是[不相联的](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)。

根据您的决策，选择其中一种部署方案：

- 单个云 HR 应用租户 ->目标受信任林中的单个或多个活动目录子域
- 单云 HR 应用租户 - >在不相交的活动目录林中定位多个子域

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>单个云 HR 应用租户 ->目标受信任林中的单个或多个活动目录子域

我们建议使用以下生产配置：

|要求|建议|
|:-|:-|
|要部署的 Azure AD 连接预配代理数|两个（用于高可用性和故障转移）
|要配置的预配连接器应用数|每个子域一个应用|
|Azure AD 连接预配代理的服务器主机|Windows 2012 R2+ 具有定位活动目录域控制器的视线</br>可与 Azure AD 连接服务共存|

![流向本地代理](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>单云 HR 应用租户 - >在不相交的活动目录林中定位多个子域

此方案涉及将用户从云 HR 应用预配到不相交的活动目录林中的域。

我们建议使用以下生产配置：

|要求|建议|
|:-|:-|
|要在本地部署的 Azure AD 连接预配代理数|每个不交合一的源目录林两个|
|要配置的预配连接器应用数|每个子域一个应用|
|Azure AD 连接预配代理的服务器主机|Windows 2012 R2+ 具有定位活动目录域控制器的视线</br>可与 Azure AD 连接服务共存|

![单云 HR 应用租户不交对的活动目录林](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD 连接预配代理要求

云 HR 应用到 Active Directory 用户预配解决方案要求您在运行 Windows 2012 R2 或更高版本的服务器上部署一个或多个 Azure AD Connect 预配代理。 服务器必须至少具有 4 GB RAM 和 .NET 4.7.1+ 运行时。 确保主机服务器具有对目标活动目录域的网络访问权限。

要准备本地环境，Azure AD Connect 预配代理配置向导将代理注册到 Azure AD 租户，[打开端口](../manage-apps/application-proxy-add-on-premises-application.md#open-ports)，[允许访问 URL，](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)并支持[出站 HTTPS 代理配置](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)。

预配代理使用服务帐户与活动目录域通信。 在安装代理之前，请在 Active Directory 用户和计算机中创建满足以下要求的服务帐户：

- 不会过期的密码
- 委派的读取、创建、删除和管理用户帐户的控件权限

您可以选择应处理预配请求的域控制器。 如果您有多个地理分布的域控制器，请与首选域控制器在同一站点中安装预配代理。 这种定位提高了端到端解决方案的可靠性和性能。

为获得高可用性，可以部署多个 Azure AD Connect 预配代理。 注册代理以处理同一组本地活动目录域。

## <a name="plan-scoping-filters-and-attribute-mapping"></a>规划范围筛选器和属性映射

当您启用从云 HR 应用到活动目录或 Azure AD 的预配时，Azure 门户将通过属性映射控制属性值。

### <a name="define-scoping-filters"></a>定义范围筛选器

使用[范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)定义基于属性的规则，这些规则确定应从云 HR 应用预配到活动目录或 Azure AD 的用户。

启动联接器进程时，收集以下要求：

- 云 HR 应用是否用于引入员工和临时员工？
- 您是否计划将云 HR 应用用于 Azure AD 用户预配来管理员工和临时工作人员？
- 您是否计划仅为云 HR 应用用户的子集向 Azure AD 用户预配云 HR 应用？ 例如，员工可能只有员工。

根据您的要求，在配置属性映射时，可以设置 **"源对象范围"** 字段，以选择云 HR 应用中哪些用户集应位于预配到活动目录的范围内。 有关详细信息，请参阅云 HR 应用教程，了解常用的范围筛选器。

### <a name="determine-matching-attributes"></a>确定匹配属性

通过预配，您可以匹配源系统和目标系统之间的现有帐户。 将云 HR 应用与 Azure AD 预配服务集成时，可以[配置属性映射](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings)以确定应从云 HR 应用流向活动目录或 Azure AD 的用户数据。

启动联接器进程时，收集以下要求：

- 此云 HR 应用中用于标识每个用户的唯一 ID 是什么？
- 从身份生命周期的角度来看，如何处理重新雇用？ 重新雇用员工保留其旧员工 ID 吗？
- 您是否处理未来雇佣关系并提前为其创建活动目录帐户？
- 从身份生命周期的角度来看，您如何处理员工向临时员工转换，或者以其他方式？
- 转换后的用户是否保留其旧的活动目录帐户，或者他们是否获得新的活动目录帐户？

根据您的要求，Azure AD 支持通过为属性映射提供常量值或[写入表达式，](../app-provisioning/functions-for-customizing-application-data.md)支持直接的属性到属性映射。 这种灵活性使您能够最终控制目标应用属性中填充的内容。 您可以使用 Microsoft[图形 API](../app-provisioning/export-import-provisioning-configuration.md)和图形资源管理器将用户预配属性映射和架构导出到 JSON 文件并将其导入 Azure AD。

默认情况下，云 HR 应用中表示唯一员工 ID 的属性用作映射到 Active Directory*中的唯一属性的*匹配属性。 例如，在工作日应用方案中，**工作日****工作 ID**属性映射到活动目录**员工 ID**属性。

您可以设置多个匹配属性并分配匹配优先级。 它们按匹配优先级进行评估。 一旦找到匹配，就不会进一步评估其他匹配属性。

还可以[自定义默认属性映射](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)，例如更改或删除现有属性映射。 您还可以根据业务需求创建新的属性映射。 有关详细信息，请参阅云 HR 应用教程（如[工作日](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)），了解要映射的自定义属性的列表。

### <a name="determine-user-account-status"></a>确定用户帐户状态

默认情况下，预配连接器应用将 HR 用户配置文件状态映射到 Active Directory 或 Azure AD 中的用户帐户状态，以确定是启用还是禁用用户帐户。

启动"加入者-离开者"进程时，请收集以下要求。

| 进程 | 要求 |
| - | - |
| **联接器** | 从身份生命周期的角度来看，如何处理重新雇用？ 重新雇用员工保留其旧员工 ID 吗？ |
| | 您是否处理未来雇佣关系并提前为其创建活动目录帐户？ 这些帐户是在启用或禁用状态下创建的？ |
| | 从身份生命周期的角度来看，您如何处理员工向临时员工转换，或者以其他方式？ |
| | 转换后的用户是否保留其旧的活动目录帐户，或者他们是否获得新的活动目录帐户？ |
| **毕业生** | 在 Active Directory 中，员工和临时工作人员的解雇处理方式是否不同？ |
| | 处理用户终止时考虑生效日期是什么？ |
| | 员工和临时工作人员转换如何影响现有的活动目录帐户？ |
| | 如何在活动目录中处理"撤销"操作？ 如果在"已加入者"流程的一部分在 Active Directory 中创建将来的日期雇用，则需要处理撤销操作。 |

根据您的要求，可以使用[Azure AD 表达式](../app-provisioning/functions-for-customizing-application-data.md)自定义映射逻辑，以便根据数据点的组合启用或禁用 Active Directory 帐户。

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>将云 HR 应用映射到活动目录用户属性

每个云 HR 应用都附带默认云 HR 应用到活动目录映射。

启动"连接者-移动器-离开者"进程时，请收集以下要求。

| 进程 | 要求 |
| - | - |
| **联接器** | 活动目录帐户创建过程是手动、自动化还是部分自动化？ |
| | 您是否计划将自定义属性从云 HR 应用传播到活动目录？ |
| **搬运工** | 每当云 HR 应用中发生移动器操作时，您希望处理哪些属性？ |
| | 在用户更新时，您是否执行任何特定的属性验证？ 如果是，请提供详细信息。 |
| **毕业生** | 在 Active Directory 中，员工和临时工作人员的解雇处理方式是否不同？ |
| | 处理用户终止时考虑生效日期是什么？ |
| | 员工和临时工作人员转换如何影响现有的活动目录帐户？ |

根据您的要求，您可以修改映射以满足您的集成目标。 有关详细信息，请参阅特定的云 HR 应用教程（如[工作日](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)），了解要映射的自定义属性的列表。

### <a name="generate-a-unique-attribute-value"></a>生成唯一的属性值

启动联接器进程时，在设置具有唯一约束的 CN、samAccountName 和 UPN 等属性时，可能需要生成唯一的属性值。

Azure AD 函数["选择唯一值](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)"计算每个规则，然后检查生成的值，以检查目标系统中的唯一性。 例如，请参阅[为用户主体名称 （UPN） 属性生成唯一值](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)。

> [!NOTE]
> 当前仅支持"工作日到活动目录"用户预配此功能。 它不能与其他预配应用一起使用。

### <a name="configure-active-directory-ou-container-assignment"></a>配置活动目录 OU 容器分配

根据业务部门、位置和部门将 Active Directory 用户帐户放入容器中是一项常见要求。 启动移动器进程时，如果监督组织发生更改，则可能需要在 Active Directory 中将用户从一个 OU 移动到另一个 OU。

使用[Switch（）](../app-provisioning/functions-for-customizing-application-data.md#switch)函数配置 OU 分配的业务逻辑，并将其映射到活动目录属性**父分母名称**。

例如，如果要根据 HR 属性 **"市政"** 在 OU 中创建用户，则可以使用以下表达式：

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

使用此表达式，如果市政值为达拉斯、奥斯汀、西雅图或伦敦，则用户帐户将在相应的 OU 中创建。 如果没有匹配项，则在默认 OU 中创建该帐户。

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>计划新用户帐户的密码传递

启动 Joiners 进程时，需要设置并传递新用户帐户的临时密码。 借助云 HR 到 Azure AD 用户预配，您可以在第一天为用户推出 Azure AD[自助服务密码重置](../authentication/quickstart-sspr.md)（SSPR） 功能。

SSPR 是 IT 管理员使用户能够重置密码或解锁帐户的简单方法。 您可以将**移动号码**属性从云 HR 应用预配到活动目录，并将其与 Azure AD 同步。 **移动号码**属性位于 Azure AD 中后，可以为用户帐户启用 SSPR。 然后在第一天，新用户可以使用已注册和验证的手机号码进行身份验证。

## <a name="plan-for-initial-cycle"></a>计划初始周期

首次运行 Azure AD 预配服务时，它会针对云 HR 应用执行[初始周期](../app-provisioning/how-provisioning-works.md#initial-cycle)，以创建云 HR 应用中所有用户对象的快照。 初始周期所占用的时间直接取决于源系统中有多少用户。 对于具有 100，000 多个用户的云 HR 应用租户而言，初始周期可能需要很长时间。

**对于大型云 HR 应用租户（>30，000 个用户），** 在逐步阶段运行初始周期。 仅在验证在 Active Directory 中为不同的用户预配方案设置了正确的属性后，才能启动增量更新。 按照此处的顺序操作。

1. 通过设置[范围筛选器](#plan-scoping-filters-and-attribute-mapping)，仅为有限的用户组运行初始周期。
2. 验证活动目录帐户预配和为首次运行选定的用户设置的属性值。 如果结果符合您的预期，请展开范围筛选器以逐步包括更多用户，并验证第二次运行的结果。

对测试用户的初始周期的结果感到满意后，启动[增量更新](../app-provisioning/how-provisioning-works.md#incremental-cycles)。

## <a name="plan-testing-and-security"></a>计划测试和安全

在部署的每个阶段，从初始试验到启用用户预配，确保测试结果与预期结果一样，并审核预配周期。

### <a name="plan-testing"></a>计划测试

将云 HR 应用配置为 Azure AD 用户预配后，请运行测试用例以验证此解决方案是否符合组织的要求。

|方案|预期结果|
|:-|:-|
|新员工在云 HR 应用中被雇用。| - 用户帐户预配在活动目录中。</br>- 用户可以登录到活动目录域应用并执行所需的操作。</br>- 如果配置了 Azure AD 连接同步，则还会在 Azure AD 中创建用户帐户。
|用户在云 HR 应用中终止。|- 在活动目录中禁用用户帐户。</br>- 用户无法登录到受活动目录保护的任何企业应用。
|用户监督组织在云 HR 应用程序中更新。|根据属性映射，用户帐户在活动目录中从一个 OU 移动到另一个 OU。|
|HR 在云 HR 应用中更新用户的经理。|活动目录中的经理字段将更新以反映新经理的名称。|
|人力资源部将员工重新雇用到新职位。|行为取决于云 HR 应用如何配置为生成员工 ID：</br>- 如果重新雇用旧员工 ID，连接器将启用用户的现有 Active Directory 帐户。</br>- 如果重新雇用获取新的员工 ID，连接器将为用户提供新的活动目录帐户。|
|人力资源部将员工转换为合同工作人员，反之亦然。|为新角色创建新的 Active Directory 帐户，并在转换生效日期禁用旧帐户。|

使用前面的结果确定如何根据已建立的时间表将自动用户预配实现转换为生产。

> [!TIP]
> 使用使用生产数据刷新测试环境时，使用数据缩减和数据清理等技术来删除或屏蔽敏感的个人数据，以符合隐私和安全标准。 

### <a name="plan-security"></a>规划安全性

在部署新服务时，通常需要进行安全审查。 如果需要或尚未进行安全审核，请参阅许多 Azure AD 白皮书，这些[白皮书](https://www.microsoft.com/download/details.aspx?id=36391)提供作为服务的身份概述。

### <a name="plan-rollback"></a>计划回滚

云 HR 用户预配实现可能无法在生产环境中根据需要工作。 如果是这样，以下回滚步骤可以帮助您恢复到以前的已知良好状态。

1. 查看[预配摘要报告](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal)和[预配日志](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)，以确定对受影响的用户或组执行了哪些不正确的操作。 有关预配摘要报告和日志的详细信息，请参阅[管理云 HR 应用用户预配](#manage-your-configuration)。
2. 受影响的用户或组的最后已知良好状态可以通过预配审核日志或查看目标系统（Azure AD 或活动目录）来确定。
3. 使用应用所有者使用最后一个已知良好的状态值更新直接在应用中受影响的用户或组。

## <a name="deploy-the-cloud-hr-app"></a>部署云 HR 应用

选择符合解决方案要求的云 HR 应用。

**工作日**：要将工作日的工作配置文件导入活动目录和 Azure AD，请参阅[教程：为自动用户预配配置工作日](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)。 或者，您可以将电子邮件地址和用户名写回工作日。

## <a name="manage-your-configuration"></a>管理配置

Azure AD 可通过审核日志和报告提供有关组织用户预配使用情况和操作运行状况的其他见解。

### <a name="gain-insights-from-reports-and-logs"></a>从报告和日志中获取见解

成功[的初始周期](../app-provisioning/how-provisioning-works.md#initial-cycle)后，Azure AD 预配服务将继续无限期地以特定于每个应用的教程中定义的间隔无限期地运行增量更新，直到发生以下事件之一：

- 该服务已手动停止。 通过使用[Azure 门户](https://portal.azure.com/)或相应的 Microsoft[图形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)命令触发新的初始周期。
- 由于属性映射或范围筛选器的变化，将触发新的初始周期。
- 由于错误率高，预配过程进入隔离状态。 它在隔离中停留超过四周，此时它会自动禁用。

要查看这些事件和预配服务执行的所有其他活动，[了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)。

#### <a name="azure-monitor-logs"></a>Azure Monitor 日志

预配服务执行的所有活动都记录在 Azure AD 审核日志中。 您可以将 Azure AD 审核日志路由到 Azure 监视器日志以进行进一步分析。 使用 Azure 监视器日志（也称为日志分析工作区），可以查询数据以查找事件、分析趋势并跨各种数据源执行相关性。 观看此[视频](https://youtu.be/MP5IaCTwkQg)，了解在实际用户方案中使用 Azure AD 日志的 Azure 监视器日志的好处。

安装[Azure AD 活动日志的日志分析视图](../reports-monitoring/howto-install-use-log-analytics-views.md)，以访问有关环境中预配事件的[预构建报告](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views)。

有关详细信息，请参阅如何使用 Azure[监视器日志分析 Azure AD 活动日志](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)。

### <a name="manage-personal-data"></a>管理个人数据

Windows 服务器上安装的 Azure AD Connect 预配代理在 Windows 事件日志中创建日志，这些日志可能包含个人数据，具体取决于云 HR 应用到 Active Directory 属性映射。 为了遵守用户隐私义务，请设置 Windows 计划任务以清除事件日志，并确保数据不会保留超过 48 小时。

Azure AD 预配服务不会生成报告、执行分析或提供超过 30 天的见解，因为该服务不会存储、处理或保留任何数据超过 30 天。

### <a name="troubleshoot"></a>疑难解答

要排除预配期间可能出现的任何问题，请参阅以下文章：

- [为 Azure AD 库应用程序配置用户预配时遇到的问题](application-provisioning-config-problem.md)
- [将属性从本地活动目录同步到 Azure AD 以预配到应用程序](user-provisioning-sync-attributes-for-mapping.md)
- [用户预配到 Azure AD 库应用程序需要数小时或更长时间](application-provisioning-when-will-provisioning-finish.md)
- [在将用户预配配置配置为 Azure 活动目录库应用程序时保存管理员凭据时出现问题](application-provisioning-config-problem-storage-limit.md)
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
- [Azure AD 连接预配代理：版本发布历史记录](provisioning-agent-release-version-history.md)
