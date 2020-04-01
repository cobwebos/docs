---
title: 企业安全性
titleSuffix: Azure Machine Learning
description: 安全使用 Azure 机器学习：身份验证、授权、网络安全性、数据加密和监视。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 359fd7fc787db5710deca75dd562215d25ed9148
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437487"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure 机器学习的企业安全性

本文介绍 Azure 机器学习可用的安全性功能。

使用某个云服务时，最佳做法是仅限需要该服务的用户访问它。 首先需要了解服务使用的身份验证和授权模型。 此外，你可能想要限制网络访问，或者安全地将本地网络中的资源加入云中。 静态数据以及在服务之间移动的数据的加密也至关重要。 最后，需要能够监视服务并生成所有活动的审核日志。

> [!NOTE]
> 本文中的信息适用于 Azure 机器学习 Python SDK 版本 1.0.83.1 或更高版本。

## <a name="authentication"></a>身份验证

如果 Azure Active Directory (Azure AD) 已配置为使用多重身份验证，则支持多重身份验证。 下面是身份验证过程：

1. 客户端登录到 Azure AD 并获取 Azure 资源管理器令牌。  完全支持用户和服务主体。
1. 客户端将令牌提供给 Azure 资源管理器和所有 Azure 机器学习服务。
1. 机器学习服务将机器学习服务令牌提供给用户计算目标（例如机器学习计算）。 运行完成后，用户计算目标使用此令牌回调机器学习服务。 范围限制为工作区。

[![Azure 机器学习中的身份验证](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

有关详细信息，请参阅为[Azure 机器学习资源和工作流设置身份验证](how-to-setup-authentication.md)。 本文提供有关身份验证的信息和示例，包括使用服务主体和自动化工作流。

### <a name="authentication-for-web-service-deployment"></a>Web 服务部署的身份验证

对于 Web 服务，Azure 机器学习支持两种形式的身份验证：密钥和令牌。 每个 Web 服务每次只能启用一种形式的身份验证。

|身份验证方法|说明|Azure 容器实例|AKS|
|---|---|---|---|
|键|密钥是静态的，无需刷新。 可以手动重新生成密钥。|默认已禁用| 默认已启用|
|标记|令牌会在指定的时限后过期，需要刷新。| 不可用| 默认已禁用 |

有关代码示例，请参阅[Web 服务身份验证部分](how-to-setup-authentication.md#web-service-authentication)。

## <a name="authorization"></a>授权

你可以创建多个工作区，并且每个工作区可由多个用户共享。 共享工作区时，可通过向用户分配以下角色来控制对该工作区的访问：

* “所有者”
* 参与者
* 读取器

下表列出了一些主要 Azure 机器学习操作，以及可执行这些操作的角色：

| Azure 机器学习操作 | “所有者” | 参与者 | 读取器 |
| ---- |:----:|:----:|:----:|
| 创建工作区 | ✓ | ✓ | |
| 共享工作区 | ✓ | |  |
| 将工作区升级到企业版 | ✓ | |
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

如果内置角色不符合你的需求，可以创建自定义角色。 只有针对工作区上和机器学习计算的操作支持自定义角色。 自定义角色对工作区及其中的计算资源拥有读取、写入或删除权限。 可以使角色在特定工作区级别、特定资源组级别或特定订阅级别可用。 有关详细信息，请参阅[管理 Azure 机器学习工作区中的用户和角色](how-to-assign-roles.md)。

> [!WARNING]
> Azure 活动目录业务对企业协作当前不支持 Azure 机器学习。

### <a name="securing-compute-targets-and-data"></a>保护计算目标和数据

所有者和参与者可以使用已附加到工作区的所有计算目标和数据存储。  

每个工作区还有一个关联的系统分配的托管标识，该标识与工作区同名。 托管标识对工作区中使用的附加资源拥有以下权限。

有关托管标识的详细信息，请参阅 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

| 资源 | 权限 |
| ----- | ----- |
| 工作区 | 参与者 |
| 存储帐户 | 存储 Blob 数据参与者 |
| 密钥保管库 | 访问所有密钥、机密和证书 |
| Azure 容器注册表 | 参与者 |
| 包含工作区的资源组 | 参与者 |
| 包含 Key Vault 的资源组（如果不同于包含工作区的资源组） | 参与者 |

不建议管理员撤销托管标识对上表中所述资源的访问权限。 可以使用重新同步密钥操作来恢复访问权限。

对于每个工作区区域，Azure 机器学习将在订阅中创建一个拥有参与者级别访问权限的附加应用程序（名称以 `aml-` 或 `Microsoft-AzureML-Support-App-` 开头）。 例如，如果在美国东部有一个工作区，在北欧有一个工作区在同一订阅中，您将看到其中两个应用程序。 Azure 机器学习可以通过这些应用程序来帮助你管理计算资源。

## <a name="network-security"></a>网络安全

Azure 机器学习依赖于其他 Azure 服务提供计算资源。 计算资源（计算目标）用于训练和部署模型。 可以在虚拟网络中创建这些计算目标。 例如，可以使用 Azure Data Science Virtual Machine 来训练模型，然后将该模型部署到 AKS。  

有关详细信息，请参阅[如何在虚拟网络中运行试验和推理](how-to-enable-virtual-network.md)。

您还可以为工作区启用 Azure 专用链接。 专用链接允许您限制从 Azure 虚拟网络到工作区的通信。 有关详细信息，请参阅[如何配置专用链路](how-to-configure-private-link.md)。

> [!TIP]
> 您可以将虚拟网络和专用链接组合在一起，以保护工作区与其他 Azure 资源之间的通信。 但是，某些组合需要企业版工作区。 使用下表了解哪些方案需要企业版：
>
> | 方案 | Enterprise</br>edition | Basic</br>edition |
> | ----- |:-----:|:-----:| 
> | 无虚拟网络或专用链路 | ✔ | ✔ |
> | 没有专用链接的工作区。 虚拟网络中的其他资源（Azure 容器注册表除外） | ✔ | ✔ |
> | 没有专用链接的工作区。 具有专用链接的其他资源 | ✔ | |
> | 带专用链接的工作区。 虚拟网络中的其他资源（Azure 容器注册表除外） | ✔ | ✔ |
> | 工作区和具有专用链接的任何其他资源 | ✔ | |
> | 带专用链接的工作区。 没有专用链路或虚拟网络的其他资源 | ✔ | ✔ |
> | 虚拟网络中的 Azure 容器注册表 | ✔ | |
> | 工作区的客户托管密钥 | ✔ | |
> 

> [!WARNING]
> 启用专用链接的工作区不支持 Azure 机器学习计算实例预览。
> 
> Azure 机器学习不支持使用启用专用链接的 Azure 库伯奈斯服务。 相反，您可以在虚拟网络中使用 Azure 库伯奈斯服务。 有关详细信息，请参阅[Azure 虚拟网络中的安全 Azure ML 实验和推理作业](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>数据加密

### <a name="encryption-at-rest"></a>静态加密

> [!IMPORTANT]
> 如果您的工作区包含敏感数据，我们建议您在创建工作区时设置[hbi_workspace标志](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)。 这将控制 Microsoft 为诊断目的收集的数据量，并在 Microsoft 托管环境中启用其他加密。

有关 Azure 中静态加密的工作原理的详细信息，请参阅 [Azure 静态数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)。

#### <a name="azure-blob-storage"></a>Azure Blob 存储

Azure 机器学习在绑定到 Azure 机器学习工作区和订阅的 Azure Blob 存储帐户中存储快照、输出与日志。 Azure Blob 存储中存储的所有数据已通过 Microsoft 管理的密钥静态加密。

有关如何对 Azure Blob 存储中存储的数据使用自己密钥的信息，请参阅[使用 Azure Key Vault 中客户管理的密钥进行 Azure 存储加密](../storage/common/storage-encryption-keys-portal.md)。

训练数据通常也存储在 Azure Blob 存储中，因此可供训练计算目标访问。 此存储不由 Azure 机器学习管理，而是作为远程文件系统装载到计算目标。

如果需要__旋转或撤销__密钥，您可以随时轮换或撤消密钥。 旋转密钥时，存储帐户将开始使用新密钥（最新版本）来加密静态数据。 当吊销（禁用）密钥时，存储帐户会处理失败的请求。 旋转或撤销通常需要一个小时才能生效。

有关重新生成访问密钥的信息，请参阅[重新生成存储访问密钥](how-to-change-storage-access-key.md)。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure 机器学习在 Azure Cosmos DB 实例中存储指标和元数据。 此实例与 Azure 机器学习管理的 Microsoft 订阅相关联。 Azure Cosmos DB 中存储的所有数据已通过 Microsoft 管理的密钥静态加密。

要使用自己的（客户管理）密钥来加密 Azure Cosmos DB 实例，可以创建专用 Cosmos DB 实例，以便与工作区一起使用。 如果要将数据存储在 Microsoft 订阅中托管的多租户 Cosmos DB 实例之外的数据（如运行历史记录信息），我们建议使用此方法。 

要使用客户管理的密钥在订阅中启用预配 Cosmos DB 实例，请执行以下操作：

* 为 Cosmos DB 启用客户管理的关键功能。 目前，必须请求访问权限才能使用此功能。 为此，请联系[cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)。

* 在订阅中注册 Azure 机器学习和 Azure Cosmos 数据库资源提供程序（如果尚未完成）。

* 授权机器学习应用（在标识和访问管理中）对订阅具有参与者权限。

    ![在门户中的标识和访问管理中授权"Azure 机器学习应用"](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* 创建 Azure 机器学习工作区时，请使用以下参数。 这两个参数都是必需的，在 SDK、CLI、REST API 和资源管理器模板中都有支持。

    * `resource_cmk_uri`：此参数是密钥保管库中客户托管密钥的完整资源 URI，包括[密钥的版本信息](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)。 

    * `cmk_keyvault`：此参数是订阅中密钥保管库的资源 ID。 此密钥保管库需要位于将用于 Azure 机器学习工作区的同一区域和订阅中。 
    
        > [!NOTE]
        > 此密钥保管库实例可能不同于 Azure 机器学习在预配工作区时创建的密钥保管库。 如果要对工作区使用相同的密钥保管库实例，请使用[key_vault 参数](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)在预配工作区时传递相同的密钥保管库。 

此 Cosmos DB 实例是在订阅中的 Microsoft 托管资源组中创建的。 

> [!IMPORTANT]
> * 如果需要删除此 Cosmos DB 实例，则必须删除使用它的 Azure 机器学习工作区。 
> * 此 Cosmos DB 帐户的默认[__请求单位__](../cosmos-db/request-units.md)设置为__8000__。 不支持更改此值。 

如果需要__旋转或撤销__密钥，您可以随时轮换或撤消密钥。 旋转密钥时，Cosmos DB 将开始使用新密钥（最新版本）来加密静态数据。 当吊销（禁用）密钥时，Cosmos DB 会处理失败的请求。 旋转或撤销通常需要一个小时才能生效。

有关使用 Cosmos DB 的客户管理密钥的详细信息，请参阅[为 Azure Cosmos DB 帐户配置客户管理的密钥](../cosmos-db/how-to-setup-cmk.md)。

#### <a name="azure-container-registry"></a>Azure 容器注册表

注册表（Azure 容器注册表）中的所有容器映像均已进行静态加密。 Azure 在存储映像之前会自动加密映像，并在 Azure 机器学习提取映像时对其进行解密。

要使用自己的（客户管理）密钥来加密 Azure 容器注册表，您需要创建自己的 ACR 并在预配工作区或加密在工作区预配时创建的默认实例时附加它。

有关使用现有 Azure 容器注册表创建工作区的示例，请参阅以下文章：

* [使用 Azure CLI 为 Azure 机器学习创建工作区](how-to-manage-workspace-cli.md)。
* [使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure 容器实例

您可以使用客户管理的密钥加密已部署的 Azure 容器实例 （ACI） 资源。 用于 ACI 的客户管理密钥可以存储在工作区的 Azure 密钥保管库中。 有关生成密钥的信息，请参阅[使用客户管理的密钥加密数据](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)。

要将模型部署到 Azure 容器实例时使用密钥，请使用`AciWebservice.deploy_configuration()`创建新的部署配置。 使用以下参数提供关键信息：

* `cmk_vault_base_url`：包含密钥的密钥保管库的 URL。
* `cmk_key_name`：键的名称。
* `cmk_key_version`：密钥的版本。

有关创建和使用部署配置的详细信息，请参阅以下文章：

* [AciWebservice.deploy_configuration（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)引用
* [在何处以及如何部署](how-to-deploy-and-where.md)
* [将模型部署到 Azure 容器实例](how-to-deploy-azure-container-instance.md)

有关将客户管理的密钥用于 ACI 的详细信息，请参阅[使用客户管理的密钥加密数据](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)。

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

您可以随时使用客户管理的密钥加密已部署的 Azure Kubernetes 服务资源。 有关详细信息，请参阅使用[Azure 库伯奈斯服务自带密钥](../aks/azure-disk-customer-managed-keys.md)。 

此过程允许您加密 Kubernetes 群集中已部署虚拟机的数据和 OS 磁盘。

> [!IMPORTANT]
> 此过程仅适用于 AKS K8s 版本 1.17 或更高版本。 Azure 机器学习在 2020 年 1 月 13 日增加了对 AKS 1.17 的支持。

#### <a name="machine-learning-compute"></a>机器学习计算

Azure 存储中存储的每个计算节点的 OS 磁盘，已通过 Azure 机器学习存储帐户中由 Microsoft 管理的密钥进行加密。 此计算目标是暂时的；没有排队的运行时，群集通常会缩减。 底层虚拟机将解除预配，OS 磁盘将被删除。 OS 磁盘不支持 Azure 磁盘加密。

每个虚拟机还包含一个本地临时磁盘用于 OS 操作。 如果需要，可以使用该磁盘来暂存训练数据。 默认情况下，将参数设置为`hbi_workspace``TRUE`的工作区的磁盘进行加密。 此环境仅在运行期间是短暂的，加密支持仅限于系统管理的密钥。

#### <a name="azure-databricks"></a>Azure Databricks

Azure 数据块可用于 Azure 机器学习管道。 默认情况下，Azure 数据砖块使用的数据块文件系统 （DBFS） 使用 Microsoft 管理的密钥进行加密。 要将 Azure 数据块配置为使用客户管理的密钥，请参阅[在默认（根）DBFS 上配置客户管理的密钥](/azure/databricks/security/customer-managed-keys-dbfs)。

### <a name="encryption-in-transit"></a>传输中加密

可以使用 TLS 来保护 Azure 机器学习微服务之间的内部通信，并保护对评分终结点的外部调用。 所有 Azure 存储访问也是通过安全通道发生的。

有关详细信息，请参阅使用[TLS 通过 Azure 机器学习来保护 Web 服务](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure 机器学习使用与工作区关联的 Azure Key Vault 实例来存储各种凭据：

* 关联的存储帐户连接字符串
* Azure 容器存储库实例的密码
* 数据存储的连接字符串

Azure HDInsight 等计算目标和 VM 的 SSH 密码与密钥存储在与 Microsoft 订阅关联的独立 Key Vault 中。 Azure 机器学习不会存储用户提供的任何密码或密钥， 而是生成、授权并存储自身的 SSH 密钥，用于连接到 VM 和 HDInsight 以运行试验。

每个工作区有一个关联的系统分配的托管标识，该标识与工作区同名。 此托管标识有权访问 Key Vault 中的所有密钥、机密和证书。

## <a name="data-collection-and-handling"></a>数据收集和处理

### <a name="microsoft-collected-data"></a>微软收集数据

Microsoft 可能会收集非用户标识信息，如资源名称（例如数据集名称或机器学习实验名称）或用于诊断目的的作业环境变量。 所有这些数据都使用微软管理的密钥存储在微软拥有的订阅中托管的存储中，并遵循[微软的标准隐私政策和数据处理标准](https://privacy.microsoft.com/privacystatement)。

Microsoft 还建议不要将敏感信息（如帐户密钥机密）存储在环境变量中。 环境变量由我们记录、加密和存储。 同样，在命名[runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)时，请避免包含敏感信息，如用户名或机密项目名称。 此信息可能显示在 Microsoft 支持工程师可访问的遥测日志中。

您可以在预配工作区`hbi_workspace``TRUE`时将参数设置为 从正在收集的诊断数据中选择退出。 使用 AzureML Python SDK、CLI、REST API 或 Azure 资源管理器模板时，此功能受支持。

### <a name="microsoft-generated-data"></a>微软生成的数据

使用自动机器学习等服务时，Microsoft 可能会生成临时的预处理数据，用于训练多个模型。 此数据存储在工作区中的数据存储中，这允许您适当地实施访问控制和加密。

您可能还希望将[从已部署终结点记录的诊断信息](how-to-enable-app-insights.md)加密到 Azure 应用程序见解实例中。

## <a name="monitoring"></a>监视

### <a name="metrics"></a>指标

可以使用 Azure Monitor 指标来查看和监视 Azure 机器学习工作区的指标。 在 [Azure 门户](https://portal.azure.com)中选择你的工作区，然后选择“指标”：****

[![显示工作区示例指标的屏幕截图](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

指标包含有关运行、部署和注册的信息。

有关详细信息，请参阅 [Azure Monitor 中的指标](/azure/azure-monitor/platform/data-platform-metrics)。

### <a name="activity-log"></a>活动日志

可以查看工作区的活动日志，以了解在工作区中执行的各项操作。 日志包含操作名称、事件发起端和时间戳等基本信息。

以下屏幕截图显示了工作区的活动日志：

[![显示工作区活动日志的屏幕截图](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

评分请求详细信息存储在 Application Insights 中。 创建工作区时，会在订阅中创建 Application Insights。 记录的信息包括字段，例如：

* HTTP方法
* UserAgent
* ComputeType
* 请求 Url
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure 机器学习工作区中的某些操作不会将信息记录到活动日志。 例如，不会记录训练运行的启动和模型的注册。
>
> 其中的某些操作显示在工作区的“活动”区域，但这些通知不会指出活动是谁发起的。****

## <a name="data-flow-diagrams"></a>数据流示意图

### <a name="create-workspace"></a>创建工作区

下图显示了创建工作区工作流。

* 从受支持的 Azure 机器学习客户端之一（Azure CLI、Python SDK、Azure 门户）登录到 Azure AD，并请求相应的 Azure 资源管理器令牌。
* 调用 Azure 资源管理器以创建工作区。 
* Azure 资源管理器联系 Azure 机器学习资源提供程序来预配工作区。

创建工作区期间，会在用户的订阅中创建其他资源：

* Key Vault（用于存储机密）
* Azure 存储帐户（包括 Blob 和文件共享）
* Azure 容器注册表（存储用于推理/评分和试验的 Docker 映像）
* Application Insights（用于存储遥测数据）

用户还可根据需要预配附加到工作区的其他计算目标（例如 Azure Kubernetes 服务或 VM）。

[![创建工作区工作流](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>保存源代码（训练脚本）

下图显示了代码快照工作流。

与 Azure 机器学习工作区关联的是包含源代码（训练脚本）的目录（试验）。 这些脚本存储在本地计算机和云中（位于订阅的 Azure Blob 存储中）。 代码快照用于执行或检查历史审核。

[![代码快照工作流](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>培训

下图显示了训练工作流。

* 使用在上一部分保存的代码快照的快照 ID 调用 Azure 机器学习。
* Azure 机器学习创建一个运行 ID（可选）和一个机器学习服务令牌，计算目标（例如机器学习计算/VM）随后使用该令牌来与机器学习服务通信。
* 您可以选择托管计算目标（如机器学习计算）或非托管计算目标（如 VM）来运行培训作业。 下面是这两种方案的数据流：
   * VM/HDInsight，通过 Microsoft 订阅中的 Key Vault 内的 SSH 凭据访问。 Azure 机器学习在计算目标上运行管理代码，以执行以下操作：

   1. 准备环境。 （Docker 是 VM 和本地计算机的一个选项。 请参阅适用于机器学习计算的以下步骤来了解如何在 Docker 容器上运行试验。）
   1. 下载代码。
   1. 设置环境变量和配置。
   1. 运行用户脚本（上一部分提到的代码快照）。

   * 机器学习计算，通过工作区托管标识访问。
由于机器学习计算是托管的计算目标（即，它由 Microsoft 管理），因此它会在 Microsoft 订阅下运行。

   1. 根据需要启动远程 Docker 构造。
   1. 管理代码将写入用户的 Azure 文件共享。
   1. 使用初始命令启动容器。 即，使用上一步骤中所述的管理代码。

#### <a name="querying-runs-and-metrics"></a>查询运行和指标

在以下流示意图中，当训练计算目标将运行指标从 Cosmos DB 数据库中的存储写回到 Azure 机器学习时，将执行此步骤。 客户端可以调用 Azure 机器学习。 而机器学习又会从 Cosmos DB 数据库提取指标，然后将指标返回给客户端。

[![培训工作流](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>创建 Web 服务

下图显示了推理工作流。 推理或模型评分是将部署的模型用于预测（通常针对生产数据）的阶段。

以下是详细信息：

* 用户使用 Azure 机器学习 SDK 等客户端注册模型。
* 用户使用模型、评分文件和其他模型依赖项创建映像。
* Docker 映像在创建后存储在 Azure 容器注册表中。
* 使用在上一步骤中创建的映像将 Web 服务部署到计算目标（容器实例/AKS）。
* 评分请求详细信息存储在应用程序见解中，该见解位于用户的订阅中。
* 遥测数据还会推送到 Microsoft/Azure 订阅。

[![推理工作流](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>后续步骤

* [使用 TLS 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [如何运行批量预测](how-to-use-parallel-run-step.md)
* [使用应用程序见解监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [通过 Azure 虚拟网络使用 Azure 机器学习](how-to-enable-virtual-network.md)
* [有关构建建议系统的最佳实践](https://github.com/Microsoft/Recommenders)
* [在 Azure 上生成实时建议 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
