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
ms.date: 01/09/2020
ms.openlocfilehash: 32b3135f805cc6c68d8cd9d6fa2b6f957cd140ad
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444139"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure 机器学习的企业安全性

在本文中，你将了解 Azure 机器学习可用的安全功能。

使用云服务时，最佳做法是仅将访问权限限制为需要它的用户。 首先了解服务使用的身份验证和授权模型。 你可能还希望限制网络访问，或通过云安全地加入本地网络中的资源。 数据加密也是非常重要的，无论是静态的还是在服务之间移动数据。 最后，您需要能够监视服务并生成所有活动的审核日志。

> [!NOTE]
> 本文中的信息适用于 Azure 机器学习 Python SDK 1.0.83.1 或更高版本。

## <a name="authentication"></a>Authentication

如果 Azure Active Directory （Azure AD）配置为使用多重身份验证，则支持多重身份验证。 身份验证过程如下：

1. 客户端登录到 Azure AD 并获取 Azure 资源管理器令牌。  完全支持用户和服务主体。
1. 客户端将令牌提供给 Azure 资源管理器和所有 Azure 机器学习。
1. 机器学习服务向用户计算目标（例如机器学习计算）提供机器学习服务标记。 在运行完成后，用户计算目标使用此标记回拨到机器学习服务。 范围限制为工作区。

[Azure 机器学习中的 ![身份验证](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

有关详细信息，请参阅[为 Azure 机器学习资源和工作流设置身份验证](how-to-setup-authentication.md)。 本文提供了有关身份验证的信息和示例，包括使用服务主体和自动化工作流。


### <a name="authentication-for-web-service-deployment"></a>Web 服务部署的身份验证

对于 web 服务，Azure 机器学习支持两种形式的身份验证：密钥和令牌。 每个 web 服务一次只能启用一种形式的身份验证。

|身份验证方法|说明|Azure 容器实例|AKS|
|---|---|---|---|
|密钥|密钥是静态的，无需刷新。 可以手动重新生成密钥。|默认情况下禁用| 默认为启用|
|标记|令牌在指定的时间段后过期，需要刷新。| 不可用| 默认情况下禁用 |

有关代码示例，请参阅[web 服务身份验证部分](how-to-setup-authentication.md#web-service-authentication)。

## <a name="authorization"></a>授权

你可以创建多个工作区，并且每个工作区可由多个用户共享。 共享工作区时，可以通过将这些角色分配给用户，来控制对工作区的访问权限：

* “所有者”
* 参与者
* 读取器

下表列出了一些主要 Azure 机器学习操作和可执行这些操作的角色：

| Azure 机器学习操作 | “所有者” | 参与者 | 读取器 |
| ---- |:----:|:----:|:----:|
| 创建工作区 | ✓ | ✓ | |
| 共享工作区 | ✓ | |  |
| 将工作区升级到 Enterprise edition | ✓ | |
| 创建计算目标 | ✓ | ✓ | |
| 附加计算目标 | ✓ | ✓ | |
| 附加数据存储区 | ✓ | ✓ | |
| 运行试验 | ✓ | ✓ | |
| 查看运行/度量值 | ✓ | ✓ | ✓ |
| 注册模型 | ✓ | ✓ | |
| 创建映像 | ✓ | ✓ | |
| 部署 Web 服务 | ✓ | ✓ | |
| 查看模型/图像 | ✓ | ✓ | ✓ |
| 调用 web 服务 | ✓ | ✓ | ✓ |

如果内置角色不能满足您的需要，则可以创建自定义角色。 自定义角色仅支持工作区上的操作和机器学习计算。 自定义角色可以对工作区和该工作区中的计算资源具有读取、写入或删除权限。 可以使角色在特定的工作区级别、特定的资源组级别或特定的订阅级别可用。 有关详细信息，请参阅[在 Azure 机器学习工作区中管理用户和角色](how-to-assign-roles.md)。

### <a name="securing-compute-targets-and-data"></a>保护计算目标和数据

所有者和参与者可以使用附加到工作区的所有计算目标和数据存储。  

每个工作区还具有一个关联的系统分配的托管标识，该标识与工作区具有相同的名称。 托管标识对工作区中使用的附加资源具有以下权限。

有关托管标识的详细信息，请参阅[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

| 资源 | 权限 |
| ----- | ----- |
| 工作区 | 参与者 |
| 存储帐户 | 存储 Blob 数据参与者 |
| 密钥保管库 | 访问所有密钥、机密和证书 |
| Azure 容器注册表 | 参与者 |
| 包含工作区的资源组 | 参与者 |
| 包含密钥保管库的资源组（如果不同于包含工作区的密钥保管库） | 参与者 |

建议管理员不要向上表中提到的资源撤销对托管标识的访问权限。 你可以使用 "重新同步密钥" 操作还原访问权限。

Azure 机器学习将在每个工作区区域的订阅中创建一个其他应用程序（名称以 `aml-` 或 `Microsoft-AzureML-Support-App-`开头）和参与者级别的访问权限。 例如，如果在美国东部有一个工作区，并在同一订阅中北欧一个工作区，则会看到其中两个应用程序。 通过这些应用程序，Azure 机器学习可帮助你管理计算资源。

## <a name="network-security"></a>网络安全

Azure 机器学习依赖于其他 Azure 服务计算资源。 计算资源（计算目标）用于训练和部署模型。 可以在虚拟网络中创建这些计算目标。 例如，你可以使用 Azure Data Science Virtual Machine 来训练模型，然后将模型部署到 AKS。  

有关详细信息，请参阅[如何在虚拟网络中运行试验和推理](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>数据加密

### <a name="encryption-at-rest"></a>静态加密

> [!IMPORTANT]
> 如果工作区包含敏感数据，我们建议在创建工作区时设置[hbi_workspace 标志](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)。 这会控制 Microsoft 出于诊断目的收集的数据量，并在 Microsoft 托管环境中启用其他加密功能。


#### <a name="azure-blob-storage"></a>Azure Blob 存储

Azure 机器学习在与 Azure 机器学习工作区和订阅关联的 Azure Blob 存储帐户中存储快照、输出和日志。 Azure Blob 存储中存储的所有数据都静态地与 Microsoft 托管密钥一起加密。

有关如何对存储在 Azure Blob 存储中的数据使用自己的密钥的信息，请参阅[Azure Key Vault 中使用客户托管密钥的 Azure 存储加密](../storage/common/storage-encryption-keys-portal.md)。

训练数据通常也存储在 Azure Blob 存储中，因此可通过训练计算目标来进行访问。 此存储不受 Azure 机器学习管理，但会装载到以远程文件系统的形式计算目标。

有关重新生成访问密钥的信息，请参阅[重新生成存储访问密钥](how-to-change-storage-access-key.md)。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure 机器学习将指标和元数据存储在 Azure Cosmos DB 实例中。 此实例与 Azure 机器学习管理的 Microsoft 订阅相关联。 存储在 Azure Cosmos DB 中的所有数据都是通过 Microsoft 管理的密钥加密的。

若要使用自己的（客户管理的）密钥来加密 Azure Cosmos DB 实例，您可以创建一个专用的 Cosmos DB 实例用于您的工作区。 如果要将数据（例如运行历史记录信息）存储在 Microsoft 订阅中托管的多租户 Cosmos DB 实例之外，我们建议采用此方法。 

> [!NOTE]
> 此功能目前仅适用于美国东部、美国西部2、美国中南部。

若要在订阅中使用客户管理的密钥来预配 Cosmos DB 实例，请执行以下操作：

* 为 Cosmos DB 启用客户管理的密钥功能。 此时，你必须请求访问权限才能使用此功能。 为此，请联系[cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)。

* 在订阅中注册 Azure 机器学习和 Azure Cosmos DB 资源提供程序（如果尚未这样做）。

* 使用订阅的参与者权限授权机器学习应用（在标识和访问管理中）。

    ![在门户中的标识和访问管理中授权 "Azure 机器学习应用"](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* 创建 Azure 机器学习工作区时，请使用以下参数。 这两个参数都是必需的，并且在 SDK、CLI、REST Api 和资源管理器模板中受支持。

    * `resource_cmk_uri`：此参数是 key vault 中客户托管密钥的完整资源 URI，其中包括[密钥的版本信息](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)。 

    * `cmk_keyvault`：此参数是订阅中密钥保管库的资源 ID。 此密钥保管库必须位于将用于 Azure 机器学习工作区的同一区域和订阅中。 
    
        > [!NOTE]
        > 此密钥保管库实例可能不同于在预配工作区时 Azure 机器学习创建的密钥保管库。 如果要对工作区使用同一密钥保管库实例，请在使用[key_vault 参数](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)设置工作区时传递相同的密钥保管库。 

此 Cosmos DB 实例是在订阅的 Microsoft 托管资源组中创建的。 

> [!IMPORTANT]
> * 如果需要删除此 Cosmos DB 实例，则必须删除使用它的 Azure 机器学习工作区。 
> * 此 Cosmos DB 帐户的默认[__请求单位__](../cosmos-db/request-units.md)设置为__8000__。 不支持更改此值。 

有关 Cosmos DB 的客户托管密钥的详细信息，请参阅[为 Azure Cosmos DB 帐户配置客户管理的密钥](../cosmos-db/how-to-setup-cmk.md)。

#### <a name="azure-container-registry"></a>Azure 容器注册表

注册表中的所有容器映像（Azure 容器注册表）都静态加密。 Azure 会在存储映像之前自动对其进行加密，并在 Azure 机器学习拉入映像时对其解密。

若要使用自己的（客户管理的）密钥来加密 Azure 容器注册表，需要在设置工作区时创建自己的 ACR 并附加它，或加密在工作区预配时创建的默认实例。

有关使用现有 Azure 容器注册表创建工作区的示例，请参阅以下文章：

* [使用 Azure CLI 创建 Azure 机器学习的工作区](how-to-manage-workspace-cli.md)。
* [使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure 容器实例

Azure 容器实例不支持磁盘加密。 如果需要磁盘加密，建议改[为部署到 Azure Kubernetes Service 实例](how-to-deploy-azure-kubernetes-service.md)。 在这种情况下，你可能还想要使用 Azure 机器学习支持基于角色的访问控制，以防止部署到订阅中的 Azure 容器实例。

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

你可以随时使用客户管理的密钥加密已部署的 Azure Kubernetes 服务资源。 有关详细信息，请参阅[携带 Azure Kubernetes 服务的密钥](../aks/azure-disk-customer-managed-keys.md)。 

此过程允许你加密 Kubernetes 群集中已部署的虚拟机的数据和 OS 磁盘。

> [!IMPORTANT]
> 此过程仅适用于 AKS K8s 版本1.17 或更高版本。 Azure 机器学习增加了1.17 年1月 13 2020 日对 AKS 的支持。

#### <a name="machine-learning-compute"></a>机器学习计算

存储在 Azure 存储中的每个计算节点的 OS 磁盘都在 Azure 机器学习存储帐户中通过 Microsoft 托管的密钥进行加密。 此计算目标是暂时的，当没有排队的运行时，通常会缩小群集。 底层虚拟机已取消预配，操作系统磁盘被删除。 OS 磁盘不支持 Azure 磁盘加密。

每个虚拟机还具有一个本地临时磁盘用于操作系统操作。 如果需要，可以使用磁盘来暂存定型数据。 默认情况下，对工作区加密磁盘，将 `hbi_workspace` 参数设置为 `TRUE`。 此环境只在运行期间生存期较短，加密支持仅限于系统管理的密钥。

若要深入了解 Azure 中的静态加密如何工作，请参阅[静态 Azure 数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)。

### <a name="encryption-in-transit"></a>传输中加密

你可以使用 SSL 来保护 Azure 机器学习微服务之间的内部通信，并保护对计分终结点的外部调用。 所有 Azure 存储访问也通过安全通道进行。

有关详细信息，请参阅[通过 Azure 机器学习使用 SSL 来保护 web 服务](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure 机器学习使用与工作区关联的 Azure Key Vault 实例来存储各种类型的凭据：

* 关联的存储帐户连接字符串
* 密码到 Azure 容器存储库实例
* 数据存储的连接字符串

用于计算目标（如 Azure HDInsight 和 Vm）的 SSH 密码和密钥存储在与 Microsoft 订阅关联的单独密钥保管库中。 Azure 机器学习不存储用户提供的任何密码或密钥。 相反，它会生成、授权并存储自己的 SSH 密钥，以连接到 Vm 和 HDInsight 来运行试验。

每个工作区都具有关联的系统分配的托管标识，该标识与工作区同名。 此托管标识可以访问密钥保管库中的所有密钥、机密和证书。

## <a name="data-collection-and-handling"></a>数据收集和处理

### <a name="microsoft-collected-data"></a>Microsoft 收集的数据

Microsoft 可能会收集非用户识别信息，如资源名称（例如数据集名称或机器学习试验名称）或用于诊断目的的作业环境变量。 所有此类数据都使用 microsoft 托管的密钥存储在 Microsoft 拥有的订阅中，并遵循[microsoft 的标准隐私策略和数据处理标准](https://privacy.microsoft.com/privacystatement)。

Microsoft 还建议不要在环境变量中存储敏感信息（如帐户密钥机密）。 环境变量由我们记录、加密和存储。 同样，命名[runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)时，请避免包含敏感信息，如用户名或机密项目名称。 此信息可能会出现在 Microsoft 支持部门工程师可访问的遥测日志中。

设置工作区时，你可以通过将 `hbi_workspace` 参数设置为 `TRUE` 来选择不收集诊断数据。 使用 AzureML Python SDK、CLI、REST Api 或 Azure 资源管理器模板时，支持此功能。

### <a name="microsoft-generated-data"></a>Microsoft 生成的数据

当使用自动机器学习等服务时，Microsoft 可能会生成一个临时的预先处理数据，用于训练多个模型。 此数据存储在工作区中的数据存储中，可用于适当地强制实施访问控制和加密。

你可能还需要将[从已部署终结点记录的诊断信息](how-to-enable-app-insights.md)加密到 Azure 应用程序 Insights 实例。

## <a name="monitoring"></a>监视

### <a name="metrics"></a>度量值

你可以使用 Azure Monitor 度量值来查看和监视 Azure 机器学习工作区的指标。 在[Azure 门户](https://portal.azure.com)中，选择工作区，然后选择 "**度量值**"：

[显示工作区的示例指标 ![屏幕截图](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

指标包括有关运行、部署和注册的信息。

有关详细信息，请参阅[Azure Monitor 中的度量值](/azure/azure-monitor/platform/data-platform-metrics)。

### <a name="activity-log"></a>活动日志

您可以查看工作区的活动日志，以查看在工作区中执行的各种操作。 日志包含基本信息，如操作名称、事件发起程序和时间戳。

此屏幕截图显示工作区的活动日志：

[显示工作区活动日志 ![屏幕截图](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

评分请求详细信息存储在 Application Insights 中。 创建工作区时，会在订阅中创建 Application Insights。 记录的信息包括以下字段：

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure 机器学习工作区中的一些操作不会将信息记录到活动日志。 例如，不会记录定型运行和模型注册的开始时间。
>
> 其中的一些操作显示在工作区的 "**活动**" 区域中，但这些通知并不表示启动该活动的人员。

## <a name="data-flow-diagrams"></a>数据流关系图

### <a name="create-workspace"></a>创建工作区

下图显示了 "创建工作区" 工作流。

* 你可以从某个受支持的 Azure 机器学习客户端（Azure CLI、Python SDK、Azure 门户）登录到 Azure AD，并请求适当的 Azure 资源管理器令牌。
* 调用 Azure 资源管理器来创建工作区。 
* Azure 资源管理器联系 Azure 机器学习资源提供程序来设置工作区。

创建工作区时，会在用户的订阅中创建其他资源：

* Key Vault （用于存储机密）
* Azure 存储帐户（包括 blob 和文件共享）
* Azure 容器注册表（用于存储用于推理/计分和试验的 Docker 映像）
* Application Insights （用于存储遥测）

用户还可以根据需要预配附加到工作区（如 Azure Kubernetes 服务或 Vm）的其他计算目标。

[![创建工作区工作流](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>保存源代码（训练脚本）

下图显示了代码快照工作流。

与 Azure 机器学习工作区关联的是包含源代码的目录（试验）（训练脚本）。 这些脚本存储在本地计算机和云中（在订阅的 Azure Blob 存储中）。 代码快照用于执行或检查历史审核。

[![代码快照工作流](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>培训

下图显示了定型工作流。

* 在上一部分中保存的代码快照的快照 ID 调用 Azure 机器学习。
* Azure 机器学习创建一个运行 ID （可选）和一个机器学习服务令牌，计算目标（如机器学习计算/Vm）稍后使用该令牌与机器学习服务通信。
* 你可以选择托管计算目标（如机器学习计算）或非托管计算目标（例如 Vm）来运行定型作业。 以下是两种情况下的数据流：
   * Vm/HDInsight，由 Microsoft 订阅中的密钥保管库中的 SSH 凭据访问。 Azure 机器学习在以下计算目标上运行管理代码：

   1. 准备环境。 （Docker 是 Vm 和本地计算机的选项。 请参阅以下步骤，机器学习计算来了解如何在 Docker 容器上运行试验。）
   1. 下载代码。
   1. 设置环境变量和配置。
   1. 运行用户脚本（上一节中提到的代码快照）。

   * 机器学习计算，通过工作区管理的标识访问。
由于机器学习计算是托管计算目标（即它由 Microsoft 管理），因此它在你的 Microsoft 订阅下运行。

   1. 如果需要，将启动远程 Docker 构造。
   1. 管理代码将写入用户的 Azure 文件共享。
   1. 使用初始命令启动容器。 也就是说，按上一步所述的管理代码。

#### <a name="querying-runs-and-metrics"></a>查询运行和度量值

在下流关系图中，当定型计算目标将运行度量写入 Cosmos DB 数据库中的存储 Azure 机器学习时，将发生此步骤。 客户端可以调用 Azure 机器学习。 机器学习将从 Cosmos DB 数据库中转而取度量值，并将其返回给客户端。

[![定型工作流](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>创建 web 服务

下图显示了推理工作流。 推理或模型计分是部署模型用于预测的阶段，最常见的是生产数据。

以下是详细信息：

* 用户使用类似于 Azure 机器学习 SDK 的客户端注册模型。
* 用户使用模型、分数文件和其他模型依赖项创建一个图像。
* 创建 Docker 映像并将其存储在 Azure 容器注册表中。
* Web 服务使用上一步中创建的映像部署到计算目标（容器实例/AKS）。
* 评分请求详细信息存储在用户订阅中的 Application Insights 中。
* 遥测还会推送到 Microsoft/Azure 订阅。

[![推理工作流](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>后续步骤

* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 web 服务的机器学习模型](how-to-consume-web-service.md)
* [如何运行批量预测](how-to-use-parallel-run-step.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [将 Azure 机器学习与 Azure 虚拟网络配合使用](how-to-enable-virtual-network.md)
* [有关构建建议系统的最佳实践](https://github.com/Microsoft/Recommenders)
* [在 Azure 上生成实时建议 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
