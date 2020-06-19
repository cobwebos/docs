---
title: 企业安全性
titleSuffix: Azure Machine Learning
description: 安全地使用 Azure 机器学习：身份验证、授权、网络安全、数据加密和监视。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/19/2020
ms.openlocfilehash: 36012801a2d36b75a0683db6f029a4560150ac2b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683060"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure 机器学习的企业安全性

本文将介绍适用于 Azure 机器学习的安全功能。

使用云服务时，最佳做法是将访问权限仅仅限制给需要该服务的用户。 首先，了解服务使用的身份验证和授权模型。 你可能还需要限制网络访问权限，或通过云安全地联接本地网络中的资源。 数据加密也非常重要，静态数据以及服务之间移动的数据均是如此。 最后，需要能够监视服务并生成所有活动的审核日志。

> [!NOTE]
> 本文中的信息适用于 Azure 机器学习 Python SDK 版本 1.0.83.1 或更高版本。

## <a name="authentication"></a>身份验证

如果 Azure Active Directory (Azure AD) 配置为使用多重身份验证，则支持多重身份验证。 下面是身份验证过程：

1. 客户端登录到 Azure AD 并获取 Azure 资源管理器令牌。  完全支持用户和服务主体。
1. 客户端将令牌提供给 Azure 资源管理器和所有 Azure 机器学习。
1. 机器学习服务向用户计算目标（例如机器学习计算）提供机器学习服务令牌。 运行完成后，用户计算目标使用此令牌回调到机器学习服务。 范围限制为工作区。

[![Azure 机器学习中的身份验证](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

有关详细信息，请参阅[为 Azure 机器学习资源和工作流设置身份验证](how-to-setup-authentication.md)。 本文提供了有关身份验证的信息和示例，包括使用服务主体和自动化工作流。

### <a name="authentication-for-web-service-deployment"></a>Web 服务部署的身份验证

对于 Web 服务，Azure 机器学习支持两种形式的身份验证：密钥和令牌。 每个 Web 服务一次只能启用一种形式的身份验证。

|身份验证方法|说明|Azure 容器实例|AKS|
|---|---|---|---|
|密钥|密钥是静态的，无需刷新。 可以手动重新生成密钥。|默认情况下禁用| 默认情况下启用|
|标记|令牌在指定的时间段后过期，需要刷新。| 不可用| 默认情况下禁用 |

有关代码示例，请参阅 [Web 服务身份验证部分](how-to-setup-authentication.md#web-service-authentication)。

## <a name="authorization"></a>授权

你可以创建多个工作区，并且每个工作区可由多个用户共享。 共享工作区时，可通过向用户分配以下角色来控制对该工作区的访问：

* “所有者”
* 参与者
* 读取器

下表列出了一些主要的 Azure 机器学习操作和可执行这些操作的角色：

| Azure 机器学习操作 | “所有者” | 参与者 | 读取器 |
| ---- |:----:|:----:|:----:|
| 创建工作区 | ✓ | ✓ | |
| 共享工作区 | ✓ | |  |
| 将工作区升级到 Enterprise Edition | ✓ | |
| 创建计算目标 | ✓ | ✓ | |
| 附加计算目标 | ✓ | ✓ | |
| 附加数据存储 | ✓ | ✓ | |
| 运行试验 | ✓ | ✓ | |
| 查看运行/指标 | ✓ | ✓ | ✓ |
| 注册模型 | ✓ | ✓ | |
| 创建映像 | ✓ | ✓ | |
| 部署 Web 服务 | ✓ | ✓ | |
| 查看模型/映像 | ✓ | ✓ | ✓ |
| 调用 Web 服务 | ✓ | ✓ | ✓ |

如果内置角色不能满足需求，则可以创建自定义角色。 只有工作区和机器学习计算上的操作才支持自定义角色。 自定义角色可以对工作区及其中的计算资源具有读取、写入或删除权限。 可以在特定的工作区级别、特定的资源组级别或特定的订阅级别提供该角色。 有关详细信息，请参阅[管理 Azure 机器学习工作区中的用户和角色](how-to-assign-roles.md)。

> [!WARNING]
> Azure Active Directory 企业对企业协作支持 Azure 机器学习，但目前 Azure Active Directory 的企业对消费者协作不支持。

### <a name="securing-compute-targets-and-data"></a>保护计算目标和数据的安全

所有者和参与者可以使用附加到工作区的所有计算目标和数据存储。  

每个工作区还具有一个关联的系统分配的托管标识，其名称与工作区名称相同。 托管标识对工作区中使用的附加资源具有以下权限。

有关托管标识的详细信息，请参阅 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

| 资源 | 权限 |
| ----- | ----- |
| 工作区 | 参与者 |
| 存储帐户 | 存储 Blob 数据参与者 |
| 密钥保管库 | 对所有密钥、机密和证书的访问权限 |
| Azure 容器注册表 | 参与者 |
| 包含工作区的资源组 | 参与者 |
| 包含密钥保管库的资源组（如果与包含工作区的资源组不同） | 参与者 |

不建议管理员撤销托管标识对上表中所提到的资源的访问权限。 可以使用重新同步密钥操作还原访问权限。

Azure 机器学习将在订阅中为每个工作区区域创建一个额外的应用程序（名称以 `aml-` 或 `Microsoft-AzureML-Support-App-` 开头），该应用程序具有参与者级别的访问权限。 例如，在同一订阅中，如果在美国东部和欧洲北部各有一个工作区，则会看到两个这样的应用程序。 通过这些应用程序，Azure 机器学习可帮助管理计算资源。

## <a name="network-security"></a>网络安全

Azure 机器学习依赖于其他 Azure 服务提供计算资源。 计算资源（计算目标）用于训练和部署模型。 可以在虚拟网络中创建这些计算目标。 例如，可以使用 Azure Data Science Virtual Machine 来训练模型，然后将模型部署到 AKS。  

有关详细信息，请参阅[如何在独立的虚拟网络中安全地运行试验和推理](how-to-enable-virtual-network.md)。

还可以为工作区启用 Azure 专用链接。 通过专用链接，你可以限制从 Azure 虚拟网络到工作区的通信。 有关详细信息，请参阅[如何配置专用链接](how-to-configure-private-link.md)。

## <a name="data-encryption"></a>数据加密

### <a name="encryption-at-rest"></a>静态加密

> [!IMPORTANT]
> 如果工作区包含敏感数据，我们建议在创建工作区时设置 [hbi_workspace 标志](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)。 

`hbi_workspace` 标志控制 Microsoft 为诊断而收集的数据量，并在 Microsoft 托管环境中启用其他加密。 此外，该标志还可启用以下功能：

* 开始加密 Amlcompute 群集中的本地暂存磁盘，前提是尚未在该订阅中创建任何以前的群集。 否则，需要提供支持票证来启用对计算群集的暂存磁盘的加密 
* 在不同运行之间清理本地暂存磁盘
* 利用密钥保管库，将存储帐户、容器注册表和 SSH 帐户的凭据从执行层安全地传递到计算群集
* 启用 IP 筛选，以确保基础批处理池不会由除 AzureMachineLearningService 以外的任何外部服务调用


有关 Azure 中静态加密工作原理的详细信息，请参阅 [Azure 数据静态加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)。

#### <a name="azure-blob-storage"></a>Azure Blob 存储

Azure 机器学习在绑定到 Azure 机器学习工作区和订阅的 Azure Blob 存储帐户中存储快照、输出和日志。 Azure Blob 存储中存储的所有数据都使用 Microsoft 托管密钥进行静态加密。

有关如何将自己的密钥用于存储在 Azure Blob 存储中的数据的信息，请参阅[在 Azure Key Vault 中使用客户管理的密钥进行 Azure 存储加密](../storage/common/storage-encryption-keys-portal.md)。

训练数据通常也存储在 Azure Blob 存储中，因此可用于训练计算目标。 此存储并不受 Azure 机器学习管理，而是作为远程文件系统装载到计算目标上。

如果需要轮换或撤销密钥，则可以随时执行此操作。 轮换密钥时，存储帐户将开始使用新密钥（最新版本）来加密静态数据。 撤消（禁用）密钥时，存储帐户会处理失败的请求。 轮换或撤消操作通常需要一小时才能生效。

有关重新生成访问密钥的信息，请参阅[重新生成存储访问密钥](how-to-change-storage-access-key.md)。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure 机器学习在 Azure Cosmos DB 实例中存储指标和元数据。 此实例与 Azure 机器学习管理的 Microsoft 订阅相关联。 Azure Cosmos DB 中存储的所有数据都使用 Microsoft 托管密钥进行静态加密。

若要使用自己的（客户管理的）密钥来加密 Azure Cosmos DB 实例，可以创建一个专用的 Cosmos DB 实例用于工作区。 如果要将数据（例如运行历史记录信息）存储在 Microsoft 订阅中托管的多租户 Cosmos DB 实例之外，则建议采用此方法。 

若要使用客户管理的密钥来预配订阅中的 Cosmos DB 实例，请执行以下操作：

* 在订阅中注册 Azure 机器学习和 Azure Cosmos DB 资源提供程序（如果尚未注册）。

* 授予机器学习应用（在标识和访问管理中）对订阅的参与者权限。

    ![在门户中的标识和访问管理中向“Azure 机器学习应用”授权](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* 创建 Azure 机器学习工作区时，请使用以下参数。 这两个参数都是必需的，并且在 SDK、CLI、REST API 和资源管理器模板中受支持。

    * `resource_cmk_uri`：此参数是密钥保管库中客户管理的密钥的完整资源 URI，其中包括[密钥的版本信息](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)。 

    * `cmk_keyvault`：此参数是订阅中密钥保管库的资源 ID。 此密钥保管库位于要用于 Azure 机器学习工作区的同一区域和订阅中。 
    
        > [!NOTE]
        > 此密钥保管库实例可能不同于在预配工作区时 Azure 机器学习创建的密钥保管库。 如果要对工作区使用相同的密钥保管库实例，请在使用 [key_vault 参数](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)预配工作区时传递相同的密钥保管库。 

此 Cosmos DB 实例是在订阅的 Microsoft 托管资源组中创建的。 托管资源组的命名格式为 `<AML Workspace Resource Group Name><GUID>`。

> [!IMPORTANT]
> * 如果需要删除此 Cosmos DB 实例，则必须删除使用该实例的 Azure 机器学习工作区。 
> * 此 Cosmos DB 帐户的默认[请求单位数](../cosmos-db/request-units.md)设置为“8000” 。 不支持更改此值。 

如果需要轮换或撤销密钥，则可以随时执行此操作。 轮换密钥时，Cosmos DB 将开始使用新密钥（最新版本）来加密静态数据。 撤消（禁用）密钥时，Cosmos DB 会处理失败的请求。 轮换或撤消操作通常需要一小时才能生效。

有关 Cosmos DB 的客户管理的密钥的详细信息，请参阅[为 Azure Cosmos DB 帐户配置客户管理的密钥](../cosmos-db/how-to-setup-cmk.md)。

#### <a name="azure-container-registry"></a>Azure 容器注册表

注册表（Azure 容器注册表）中的所有容器映像均已进行静态加密。 Azure 会在存储映像之前自动对其加密，并在 Azure 机器学习拉取映像时自动对其解密。

若要使用自己的（客户管理的）密钥来加密 Azure 容器注册表，需要在预配工作区时创建自己的 ACR 并进行附加，或加密在预配工作区时创建的默认实例。

有关使用现有 Azure 容器注册表创建工作区的示例，请参阅以下文章：

* [使用 Azure CLI 为 Azure 机器学习创建工作区](how-to-manage-workspace-cli.md)。
* [使用 Azure 资源管理器模板为 Azure 机器学习创建工作区](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure 容器实例

可以使用客户管理的密钥来加密已部署的 Azure 容器实例 (ACI) 资源。 用于 ACI 的客户管理的密钥可存储在用于工作区的 Azure Key Vault 中。 有关生成密钥的信息，请参阅[使用客户管理的密钥加密数据](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)。

若要在将模型部署到 Azure 容器实例时使用密钥，请使用 `AciWebservice.deploy_configuration()` 创建新的部署配置。 使用以下参数提供密钥信息：

* `cmk_vault_base_url`：包含密钥的密钥保管库的 URL。
* `cmk_key_name`：键的名称。
* `cmk_key_version`：密钥版本。

有关如何创建和使用部署配置的详细信息，请参阅以下文章：

* [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) 参考
* [部署方式和位置](how-to-deploy-and-where.md)
* [将模型部署到 Azure 容器实例](how-to-deploy-azure-container-instance.md)

有关如何将客户管理的密钥用于 ACI 的详细信息，请参阅[使用客户管理的密钥加密数据](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)。

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

可以随时使用客户管理的密钥加密已部署的 Azure Kubernetes 服务资源。 有关详细信息，请参阅[对 Azure Kubernetes 服务使用自带密钥](../aks/azure-disk-customer-managed-keys.md)。 

借助此过程，你可以加密 Kubernetes 群集中已部署的虚拟机的数据和操作系统 (OS) 磁盘。

> [!IMPORTANT]
> 此过程仅适用于 AKS K8s 版本1.17 或更高版本。 Azure 机器学习于 2020 年 1 月 13 日增加了对 AKS 1.17 的支持。

#### <a name="machine-learning-compute"></a>机器学习计算

存储在 Azure 存储中的每个计算节点的 OS 磁盘都使用 Azure 机器学习存储帐户中的 Microsoft 托管密钥进行加密。 此计算目标是临时的，不存在排队的运行时，通常便会纵向缩减群集。 基础虚拟机已取消预配，并已删除 OS 磁盘。 OS 磁盘不支持 Azure 磁盘加密。

每个虚拟机还有一个用于 OS 操作的本地临时磁盘。 如果需要，可以使用该磁盘来暂存训练数据。 默认情况下，对于 `hbi_workspace` 参数设置为 `TRUE` 的工作区，该磁盘会进行加密。 此环境仅在运行期间短暂存在，加密支持仅限于系统管理的密钥。

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks 可在 Azure 机器学习管道中使用。 默认情况下，Azure Databricks 使用的 Databricks 文件系统 (DBFS) 使用 Microsoft 托管密钥进行加密。 若要将 Azure Databricks 配置为使用客户管理的密钥，请参阅[在默认（根）DBFS 上配置客户管理的密钥](/azure/databricks/security/customer-managed-keys-dbfs)。

### <a name="encryption-in-transit"></a>传输中加密

Azure 机器学习使用 TLS 来保护各种 Azure 机器学习微服务之间的内部通信。 所有 Azure 存储访问也都通过安全通道进行。

Azure 机器学习使用 TLS 来保护对评分终结点的外部调用。 有关详细信息，请参阅[使用 TLS 通过 Azure 机器学习来保护 Web 服务](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure 机器学习使用与工作区关联的 Azure Key Vault 实例来存储各种类型的凭据：

* 关联的存储帐户连接字符串
* Azure 容器存储库实例的密码
* 数据存储的连接字符串

计算目标（如 Azure HDInsight 和 VM）的 SSH 密码和密钥存储在与 Microsoft 订阅关联的单独的密钥保管库中。 Azure 机器学习不存储用户提供的任何密码或密钥， 而是生成、授权并存储自己的 SSH 密钥，以连接到 VM 和 HDInsight 来运行试验。

每个工作区都具有一个关联的系统分配的托管标识，其名称与工作区名称相同。 此托管标识可以访问密钥保管库中的所有密钥、机密和证书。

## <a name="data-collection-and-handling"></a>数据收集和处理

### <a name="microsoft-collected-data"></a>Microsoft 收集的数据

Microsoft 可能会收集非用户标识信息，如资源名称（例如数据集名称或机器学习试验名称）或用于诊断的作业环境变量。 所有此类数据都使用 Microsoft 托管密钥存储在 Microsoft 拥有的订阅中托管的存储中，并遵循 [Microsoft 的标准隐私策略和数据处理标准](https://privacy.microsoft.com/privacystatement)。

Microsoft 还建议不要在环境变量中存储敏感信息（如帐户密钥机密）。 我们会记录、加密和存储环境变量。 同样，为 [run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 命名时，请避免包含用户名或机密项目名称等敏感信息。 此信息可能会出现在可供 Microsoft 支持部门工程师访问的遥测日志中。

预配工作区时，可以通过将 `hbi_workspace` 参数设置为 `TRUE` 来选择退出收集诊断数据。 使用 AzureML Python SDK、CLI、REST API 或 Azure 资源管理器模板时，均支持此功能。

### <a name="microsoft-generated-data"></a>Microsoft 生成的数据

使用自动化机器学习等服务时，Microsoft 可能会生成一个经过预处理的临时数据，用于训练多个模型。 此数据存储在工作区的数据存储中，可用于适当地强制执行访问控制和加密操作。

此外，可能还需要加密[从已部署终结点记录到 Azure Application Insights 实例中的诊断信息](how-to-enable-app-insights.md)。

## <a name="monitoring"></a>监视

### <a name="metrics"></a>指标

可以使用 Azure Monitor 指标来查看和监视 Azure 机器学习工作区的指标。 在 [Azure 门户](https://portal.azure.com)中，选择工作区，然后选择“指标”：

[![显示工作区指标示例的屏幕截图](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

这些指标包括有关运行、部署和注册的信息。

有关详细信息，请参阅 [Azure Monitor 中的指标](/azure/azure-monitor/platform/data-platform-metrics)。

### <a name="activity-log"></a>活动日志

可以查看工作区的活动日志，了解工作区中执行的各种操作。 日志包含操作名称、事件发起程序和时间戳等基本信息。

此屏幕截图显示工作区的活动日志：

[![显示工作区活动日志的屏幕截图](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

评分请求详细信息存储在 Application Insights 中。 创建工作区时，订阅中会创建 Application Insights。 记录的信息包括以下字段：

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure 机器学习工作区中的一些操作不会将信息记录到活动日志中。 例如，训练运行的启动和模型的注册信息就不会记录。
>
> 其中一些操作显示在工作区的“活动”区域中，但这些通知并未指明启动该活动的人员。

## <a name="data-flow-diagrams"></a>数据流关系图

### <a name="create-workspace"></a>创建工作区

下图显示了创建工作区工作流。

* 从某个受支持的 Azure 机器学习客户端（Azure CLI、Python SDK、Azure 门户）登录到 Azure AD，并请求相应的 Azure 资源管理器令牌。
* 调用 Azure 资源管理器来创建工作区。 
* Azure 资源管理器联系 Azure 机器学习资源提供程序来预配工作区。

工作区创建过程中，会在用户的订阅中创建其他资源：

* Key Vault（用于存储机密）
* Azure 存储帐户（包括 blob 和文件共享）
* Azure 容器注册表（用于存储 Docker 映像，以便推理/评分和试验）
* Application Insights（用于存储遥测）

用户还可以根据需要预配附加到工作区（如 Azure Kubernetes 服务或 VM）的其他计算目标。

[![创建工作区工作流](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>保存源代码（训练脚本）

下图显示了代码快照工作流。

与 Azure 机器学习工作区相关联的是包含源代码（训练脚本）的目录（试验）。 这些脚本存储在本地计算机和云中（在订阅的 Azure Blob 存储中）。 代码快照用于执行或检查历史审核。

[![代码快照工作流](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>培训

下图显示了训练工作流。

* 使用上一部分中保存的代码快照的快照 ID 调用 Azure 机器学习。
* Azure 机器学习会创建一个运行 ID （可选）和一个机器学习服务令牌，计算目标（例如机器学习计算/VM）稍后会使用该令牌来与机器学习服务通信。
* 可以选择托管计算目标（例如机器学习计算）或非托管计算目标（例如 VM）来运行训练作业。 以下是针对两种场景的数据流：
   * VM/HDInsight，通过 Microsoft 订阅的密钥保管库中的 SSH 凭据进行访问。 Azure 机器学习在执行以下操作的计算目标上运行管理代码：

   1. 准备环境。 （对于 VM 和本地计算机，可以选择 Docker。 请参阅以下机器学习计算步骤，了解如何在 Docker 容器上运行试验。）
   1. 下载代码。
   1. 设置环境变量和配置。
   1. 运行用户脚本（上一部分中提到的代码快照）。

   * 机器学习计算，通过工作区托管标识进行访问。
由于机器学习计算是托管计算目标（即由 Microsoft 管理），因此会在 Microsoft 订阅下运行。

   1. 如果需要，可启动远程 Docker 构造。
   1. 管理代码会写入用户的 Azure 文件共享。
   1. 容器会通过初始命令启动。 即上一步所述的管理代码。

#### <a name="querying-runs-and-metrics"></a>查询运行和指标

在下面的流程图中，在训练计算目标将运行指标从 Cosmos DB 数据库中的存储写回 Azure 机器学习时就出现这一步。 客户端可以调用 Azure 机器学习。 反过来，机器学习从 Cosmos DB 数据库中拉取指标，并将其返回给客户端。

[![训练工作流](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>创建 Web 服务

下图显示了推理工作流。 推理或模型评分是将部署的模型用于预测的阶段，最常见的是对生产数据进行预测。

以下是详细信息：

* 用户使用 Azure 机器学习 SDK 等客户端注册模型。
* 用户使用模型、分数文件和其他模型依赖项创建映像。
* 在 Azure 容器注册表中创建和存储 Docker 映像。
* 使用上一步中创建的映像将 Web 服务部署到计算目标（容器实例/AKS）中。
* 将评分请求详细信息存储在用户订阅的 Application Insights 中。
* 此外，将遥测推送到 Microsoft/Azure 订阅。

[![推理工作流](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>后续步骤

* [使用 TLS 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [将 Azure 机器学习与 Azure 防火墙配合使用](how-to-access-azureml-behind-firewall.md)
* [通过 Azure 虚拟网络使用 Azure 机器学习](how-to-enable-virtual-network.md)
* [有关构建建议系统的最佳实践](https://github.com/Microsoft/Recommenders)
* [在 Azure 上生成实时建议 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
