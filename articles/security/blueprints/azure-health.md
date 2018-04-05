---
title: Azure 健康分析蓝图
description: 部署 HIPAA/HITRUST 健康分析蓝图指南
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: simorjay
ms.openlocfilehash: 700378d23f869427fb50b9dee5bcf8448ac73404
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Azure 安全性和符合性蓝图 - HIPAA/HITRUST 健康数据和 AI

## <a name="overview"></a>概述

**Azure 安全性和符合性蓝图 - HIPAA/HITRUST 健康数据和 AI 提供了包含 Azure PaaS 解决方案在内的一整套部署，用于演示如何在遵循行业符合性要求的情况下，安全地引入、存储、分析健康数据并与之交互。对于其数据受到监管的客户来说，本蓝图有助于他们加速采用和利用云。**

Azure 安全性和符合性蓝图 - HIPAA/HITRUST 健康数据和 AI 蓝图提供的工具和指南有助于部署安全且符合美国健康保险流通与责任法案 (HIPAA) 和健康信息信托联盟 (HITRUST) 规定的平台即服务 (PaaS) 环境，以便在安全的已部署为端到端解决方案的多层云环境中引入、存储、分析个人与非个人医疗记录并与之交互。 其中展示了常用的参考体系结构，旨在简化 Microsoft Azure 的采用。 这个规定的体系结构演示了一个解决方案，寻求通过基于云的方法减轻部署负担和成本的组织可以利用此解决方案解决其需求。

![](images/components.png)

根据设计，此解决方案可以使用一个示例数据集并以安全的方式对其进行存储。该数据集已采用快速医疗保健互操作性资源 (FHIR) 进行格式化，而此格式是一种世界性的标准，用于通过电子方式交换医疗保健信息。 然后，客户就可以通过 Azure 机器学习来利用强大的商业智能工具和分析，审核根据示例数据做出的预测。 Azure 机器学习可以促进特定类型的试验，例如，本蓝图包括的示例数据集、脚本和工具可以用来预测病人的住院时间。 

本蓝图旨在充当一个模块化的基础，方便客户按照特定的要求进行调整，开发新的 Azure 机器学习试验来解决临床和操作用例方案的问题。 根据设计，它在部署后是安全且合规的；但是，客户有责任正确配置角色并实施修改。 注意以下事项：

-   本蓝图提供了一个基线，有助于客户在 HITRUST 和 HIPAA 环境中使用 Microsoft Azure。

-   虽然本蓝图按设计是符合 HIPAA 和 HITRUST 标准的（使用通用安全框架 -- CSF），但必须由外部审核员根据 HIPAA 和 HITRUST 认证要求进行认证之后，才能认为它是合规的。

-   客户有责任对任何使用此基础体系结构生成的解决方案进行适当的安全性和符合性审核。

## <a name="deploying-the-automation"></a>部署自动化

- 若要部署此解决方案，请遵循部署指南中提供的说明。 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

有关本解决方案工作原理的简要概述，请观看[此视频](https://aka.ms/healthblueprintvideo)，其中解释并演示了其部署。

- 常见问题可参阅[常见问题解答](https://aka.ms/healthblueprintfaq)指南。

-   **体系结构图。** 此图显示了用于蓝图和示例用例方案的引用体系结构。

-   **部署模板**。 在此部署中，[Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)通过在安装期间指定配置参数，将体系结构的组件自动部署到 Microsoft Azure。

-   **[自动化部署脚本](https://aka.ms/healthblueprintdeploy)**。 这些脚本帮助部署解决方案。 脚本包括：


-   一个模块安装和[全局管理员](/azure/active-directory/active-directory-assign-admin-roles-azure-portal)设置脚本，用于安装所需的 PowerShell 模块，并验证是否已正确配置全局管理员角色。 
-   可以使用安装 PowerShell 脚本来部署通过 .zip 文件（其中包含预建的演示函数）提供的此解决方案。

## <a name="solution-components"></a>解决方案组件


基础体系结构由以下组件构成：

-   **[威胁模型](https://aka.ms/healththreatmodel)**：以 tm7 格式提供的综合性威胁模型适用于 [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168)，可以显示解决方案的组件、组件之间的数据流，以及信任边界。 此模型有助于客户了解在开发机器学习组件或其他修改项时系统基础结构中存在的潜在风险点。

-   **[客户实现矩阵](https://aka.ms/healthcrmblueprint)**：一种 Microsoft Excel 工作簿，其中列出了相关的 HITRUST 要求，并说明了 Microsoft 和客户的相互责任。

-   **[健康审核](https://aka.ms/healthreviewpaper)**。 此解决方案已由 Coalfire systems, Inc. 审核。有关健康符合性（HIPAA 和 HITRUST）实施方面的审核和指南提供了审核员对此解决方案的审核意见，\'以及在将蓝图转换为生产就绪型部署过程中的注意事项。

# <a name="architectural-diagram"></a>体系结构图


![](images/refarch.png)

## <a name="roles"></a>角色


此蓝图为管理用户（操作员）定义了两个角色，为负责医院管理和病人护理的用户定义了三个角色。 定义的第六个角色是针对负责评估 HIPAA 和其他法规遵循情况的审核员的。 有了 Azure 基于角色的访问控制 (RBAC)，就可以通过内置的自定义角色对解决方案的每个用户进行访问管理，既准确又有针对性。 请参阅 [Azure 门户中基于角色的访问控制入门](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)和[用于 Azure 基于角色的访问控制的内置角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)，对 RBAC、角色和权限进行详细了解。

### <a name="site-administrator"></a>站点管理员


站点管理员负责客户的 Azure 订阅。 他们控制总体部署，但无权访问病人记录。

-   默认角色分配：[所有者](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#owner)

-   自定义角色分配：不适用

-   范围：订阅

### <a name="database-analyst"></a>数据库分析师

数据库分析师管理 SQL Server 实例和数据库。
他们无权访问病人记录。

-   内置角色分配：[SQL DB 参与者](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#sql-db-contributor)、[SQL Server 参与者](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#sql-server-contributor)

-   自定义角色分配：不适用

-   范围：ResourceGroup

 ### <a name="data-scientist"></a>数据科学家


数据科学家操作 Azure 机器学习服务。 他们可以导入、导出和管理数据，并且可以运行报表。 数据科学家有权访问病人数据，但没有管理员权限。

-   内置角色分配：[存储帐户参与者](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#storage-account-contributor)

-   自定义角色分配：不适用

-   范围：ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>首席医疗信息官 (CMIO)


CMIO 有效地解决了医疗保健机构中医疗保健专业人员不懂信息/技术的问题。 其职责通常包括通过分析确定医疗机构中的资源是否分配得当。

-   内置角色分配：无

### <a name="care-line-manager"></a>医护管理员


医护管理员直接负责照顾病人。
此角色需要监测每个病人的状况，并确保护理人员能够满足病人的具体护理要求。 医护管理员负责添加和更新病人记录。

-   内置角色分配：无

-   自定义角色分配：有权运行 HealthcareDemo.ps1，负责病人入院和出院事项。

-   范围：ResourceGroup

### <a name="auditor"></a>审核员


审核员评估解决方案的符合性。 他们不能直接访问网络。

-   内置角色分配：[读者](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#reader)

-   自定义角色分配：不适用

-   范围：订阅

## <a name="example-use-case"></a>示例用例


本蓝图随附的示例用例演示了如何使用蓝图对云中的健康数据启用机器学习和分析操作。 Contosoclinic 是美国的一家小医院。 医院网络管理员希望使用 Azure 机器学习，在病人入院时更好地预测其住院时间，以便提高操作性工作负荷效率，改进所能提供的护理的质量。

### <a name="predicting-length-of-stay"></a>预测住院时间


示例用例方案使用 Azure 机器学习将病人入住时获取的详细医疗信息与根据过往病人的情况聚合的历史数据进行比较，从而预测新入院病人的住院时间。
蓝图包括大量的匿名医疗记录，用于演示解决方案的定型和预测功能。 在生产部署中，客户会使用自己的记录给解决方案定型，以便进行更准确的预测，使预测结果能够反映客户的环境、设施和病人的具体详细情况。

### <a name="users-and-roles"></a>用户和角色


**站点管理员 -- Alex**

电子邮件：Alex\_SiteAdmin

Alex 的工作是对那些能够减轻本地网络管理负担并降低管理成本的技术进行评估。 Alex 已经对 Azure 进行了一段时间的评估，但仍然在努力地根据 HiTrust 符合性要求配置所需的服务，以便将病人数据存储在云中。 Alex 选择了 Azure 健康 AI 来部署没有符合性问题的健康解决方案，满足客户在 HiTrust 方面的要求。

**数据科学家 -- Debra**

电子邮件：Debra\_DataScientist

Debra 负责创建和使用模型来分析医疗记录，以便深入了解病人护理。 Debra 使用 SQL 和 R 统计编程语言来创建其模型。

**数据库分析师 -- Danny**

电子邮件：Danny\_DBAnalyst

有关 Microsoft SQL Server（用于存储 Contosoclinic 的所有病人数据）的事项，应主要联系 Danny。 Danny 是一位有经验的 SQL Server 管理员，最近开始熟悉 Azure SQL 数据库工作。

**首席医疗信息官 -- Caroline**

Caroline 与医护管理员 Chris 以及数据科学家 Debra 一起合作，共同探讨影响病人住院时间的具体因素。
Caroline 使用住院时间 (LOS) 解决方案的预测来确定是否已在医院网络中正确分配资源。 例如，使用此解决方案中提供的仪表板。

**医护管理员 -- Chris**

电子邮件：Chris\_CareLineManager

作为直接在 Contosoclinic 负责管理病人入院和出院的人员，Chris 使用 LOS 解决方案生成的预测来确保安排足够的人员为住院病人提供照护。

**审核员 -- Han**

电子邮件：Han\_Auditor

Han 是获得认证的审核员，拥有 ISO、SOC 和 HiTrust 方面的审核经验。 雇佣 Han 是为了审核 Contosoclinc 的网络。 Han 可以审核解决方案随附的“客户责任矩阵”，确保蓝图和 LOS 解决方案可以用来存储、处理和显示敏感的个人数据。


# <a name="design-configuration"></a>设计配置


此部分详述内置到蓝图中的默认配置和安全措施，大致可用于：

- **引入**原始数据源，包括 FHIR 数据源
- **存储**敏感信息
- **分析**和预测结果
- 与结果和预测**交互**
- 解决方案的**标识**管理
- 启用了**安全性**的功能


## <a name="identity"></a>标识 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory 和基于角色的访问控制 (RBAC)


**身份验证：**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 解决方案的所有用户（包括访问 SQL 数据库的用户）都在 Azure Active Directory 中创建。



-   使用 Azure AD 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](/azure/active-directory/develop/active-directory-integrating-applications)。

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) 可以检测会影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件，并采取相应的措施予以解决。

-   [Azure 基于角色的访问控制 (RBAC)](/azure/active-directory/role-based-access-control-configure) 可在 Azure 中实现极有针对性的访问管理。 只有订阅管理员可以访问订阅，只有站点管理员可以访问 Azure Key Vault。 要求使用强密码（至少 12 个字符，包括至少一个大/小写字母、一个数字和一个特殊字符）。

-   如果在部署过程中启用了 -enableMFA 开关，则支持多重身份验证。

-   如果在部署过程中启用了 -enableADDomainPasswordPolicy 开关，则密码会在 60 天后过期。

**角色：**

-   解决方案利用[内置角色](/azure/active-directory/role-based-access-built-in-roles)来管理对资源的访问。

-   默认情况下，会向所有用户分配特定的内置角色。

### <a name="azure-key-vault"></a>Azure 密钥保管库

-   Key Vault 中存储的数据包括：

    -   Application Insights 密钥
    -   病人数据存储访问密钥
    -   病人连接字符串
    -   病人数据表名称
    -   Azure ML Web 服务终结点
    -   Azure ML 服务 API 密钥

-   根据需要配置高级访问权限策略
-   在定义 Key Vault 访问策略时，尽量将访问密钥和机密的必需权限降至最低
-   Key Vault 中的所有密钥和机密都有过期日期
-   Key Vault 中的所有密钥都受 HSM 保护 \[密钥类型 = 受 HSM 保护的 2048 位 RSA 密钥\]
-   使用基于角色的访问控制 (RBAC) 向所有用户/标识授予了最低必需权限
-   应用程序共享 Key Vault 的前提是彼此信任且需要在运行时访问相同的机密
-   启用 Key Vault 的诊断日志时，保留期设置为至少 365 天。
-   对密钥进行允许的加密操作时，仅限必需的操作

## <a name="ingest"></a>引入 

### <a name="azure-functions"></a>Azure Functions
根据设计，解决方案会使用 [Azure Functions](/azure/azure-functions/) 来处理在分析演示中使用的示例性的住院时间数据。 在函数中创建了三种功能。

**1.批量导入客户数据 phi 数据**

根据 “部署和运行演示”中的概述，将演示脚本..\\HealthcareDemo.ps1 与 **BulkPatientAdmission** 开关配合使用时，该脚本会执行以下处理管道：
1. **Azure Blob 存储** - 病人数据 .csv 文件示例上传到存储
2. **事件网格** - 事件将数据发布到 Azure Function（批量导入 - Blob 事件）
3. **Azure Function** - 进行处理后，将数据存储到 SQL 存储中，使用的是安全的函数 - event(type; blob url)
4. **SQL DB** - 使用标记来分类，对病人数据进行数据库存储，并通过 ML 过程进行定型试验。

![](images/dataflow.png)

另外，根据设计，Azure Function 可以读取和保护示例数据集中使用以下标记的指定敏感数据：
- dataProfile => “ePHI”
- owner => \<站点管理员 UPN\>
- environment => “Pilot”
- department => “Global Ecosystem" 此标记应用于其中的病人 'names' 确定为明文的示例数据集。

**2.新病人入院**

根据 “部署和运行演示”中的概述，将演示脚本..\\HealthcareDemo.ps1 与 **BulkPatientadmission** 开关配合使用时，该脚本会执行以下处理管道：![](images/securetransact.png)
**1.Azure Function** 被触发，该函数从 Azure Active Directory 请求[持有者令牌](/rest/api/)。

**2.Key Vault** 请求获取与请求的令牌关联的机密。

**3. Azure 角色验证请求，并对向 Key Vault 发出的访问请求授权。

**4.Key Vault** 返回机密。在此示例中，该机密为 SQL DB 连接字符串。

**5.Azure Function** 使用连接字符串安全地连接到 SQL 数据库，然后继续进行操作，以便存储 ePHI 数据。

为了存储数据，已根据快速医疗保健互操作性资源（FHIR，发音同 fire）的规定实施常用的 API 架构。 为此函数提供了以下 FHIR 交换元素：

-   [病人架构](https://www.hl7.org/fhir/patient.html)：涵盖病人的“个人”信息。

-   [观察架构](https://www.hl7.org/fhir/observation.html)：涵盖医疗保健中的中心元素，用于支持诊断、监视进度、确定基线和模式，甚至用于捕获人口统计特征。 

-   [就诊架构](https://www.hl7.org/fhir/encounter.html)：涵盖各种就诊类型，例如流动就诊、紧急就诊、家庭护理式就诊、住院就诊，以及虚拟就诊。

-   [条件架构](https://www.hl7.org/fhir/condition.html)：涵盖各种详细信息，涉及到条件、问题、诊断，或者其他具有一定关注度的事件、情况、问题或临床概念。  



### <a name="event-grid"></a>事件网格


此解决方案支持 Azure 事件网格，这是一项服务，用于管理所有事件从任意源到任意目标的路由，并且提供：

-   [安全性和身份验证](/azure/event-grid/security-authentication)

-   [基于角色的访问控制](/azure/event-grid/security-authentication#management-access-control)，适用于各种管理操作，例如列出事件订阅、创建新的事件订阅，以及生成密钥

-   审核

## <a name="store"></a>存储 

### <a name="sql-database-and-server"></a>SQL 数据库和 SQL Server 


-   [透明数据加密 (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 提供通过 Azure Key Vault 中存储的密钥对 Azure SQL 数据库中存储的数据进行实时加密和解密的功能。

-   [SQL 漏洞评估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是一个易于配置的工具，可以发现、跟踪和修正潜在的数据库漏洞。

-   [SQL 数据库威胁检测](/azure/sql-database/sql-database-threat-detection)已启用。

-   [SQL 数据库审核](/azure/sql-database/sql-database-auditing)已启用。

-   [SQL 数据库指标和诊断日志记录](/azure/sql-database/sql-database-metrics-diag-logging)已启用。

-   [服务器级和数据库级防火墙规则](/azure/sql-database/sql-database-firewall-configure)已收紧。

-   [Always Encrypted 列](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)用于保护病人的姓、名和中间名。
    此外，数据库列加密还使用 Azure Active Directory (AD) 对访问 Azure SQL 数据库的应用程序进行身份验证。

-   对 SQL 数据库和 SQL Server 的访问权限按“最小特权”原则配置。

-   只有要求的 IP 地址才能通过 SQL 防火墙进行访问。

### <a name="storage-accounts"></a>存储帐户


-   [动态数据只能使用 TLS/SSL 来传输](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)。

-   不允许对容器进行匿名访问。

-   配置警报规则是为了跟踪匿名活动。

-   必须使用 HTTPS 来访问存储帐户资源。

-   身份验证请求数据会进行记录，并且会受到监视。

-   Blob 存储中的数据会进行静态加密。

## <a name="analyze"></a>分析

### <a name="machine-learning"></a>机器学习


-   已为机器学习 Web 服务[启用日志记录](/azure/machine-learning/studio/web-services-logging)。
- 使用 [Machine Learning](/azure/machine-learning/preview/experimentation-service-configuration) Workbench 要求开发各种试验，以便向解决方案集提供预测功能。 [集成 Workbench](/azure/machine-learning/preview/using-git-ml-project) 有助于简化试验的管理。

## <a name="security"></a>安全性

### <a name="azure-security-center"></a>Azure 安全中心
- 在 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)可集中查看所有 Azure 资源的安全状态。 可立即确认相应的安全控制措施是否部署到位且配置正确，并且可以快速识别任何需要关注的资源。 

- [Azure 顾问](/azure/advisor/advisor-overview)是个性化的云顾问程序，可帮助遵循最佳做法来优化 Azure 部署。 它可分析资源配置和遥测使用情况，并推荐解决方案，有助于提高 Azure 资源的经济效益、性能、高可用性和安全性。

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 它检测性能异常。 其中包含强大的分析工具来帮助诊断问题，了解用户在应用中实际执行了哪些操作。 Application Insights 有助于持续提高性能与可用性。

### <a name="azure-alerts"></a>Azure 警报
- 可以通过警报这种方法来监视 Azure 服务并根据数据来配置条件。 当警报条件与监视的数据匹配时，警报还会提供通知。

### <a name="operations-management-suite-oms"></a>Operations Management Suite (OMS)
[Operations Management Suite（简称 OMS）](/azure/operations-management-suite/operations-management-suite-overview)是管理服务的集合。

-   已为安全中心启用工作区

-   启用工作区是为了进行工作负荷监视

-   启用工作负荷监视是为了：

    -   标识和访问

    -   安全和审核

    -   Azure SQL DB 分析

    -   [Azure Web 应用分析](/azure/log-analytics/log-analytics-azure-web-apps-analytics)解决方案

    -   密钥保管库分析

    -   更改跟踪

-   [Application Insights 连接器（预览版）](/azure/log-analytics/log-analytics-app-insights-connector)已启用

-   [活动日志分析](/azure/log-analytics/log-analytics-activity)已启用
