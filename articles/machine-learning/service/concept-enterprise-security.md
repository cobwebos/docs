---
title: 企业安全性
titleSuffix: Azure Machine Learning service
description: '安全地使用 Azure 机器学习服务: 身份验证、授权、网络安全、数据加密和监视。'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: 81e8601ac83d43bde0767e38eb387f489d76125b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165243"
---
# <a name="enterprise-security-for-the-azure-machine-learning-service"></a>Azure 机器学习服务的企业安全性

在本文中, 你将了解 Azure 机器学习服务可用的安全功能。

使用云服务时, 最佳做法是仅将访问权限限制为需要它的用户。 首先了解服务使用的身份验证和授权模型。 你可能还希望限制网络访问, 或通过云安全地加入本地网络中的资源。 数据加密也是非常重要的, 无论是静态的还是在服务之间移动数据。 最后, 您需要能够监视服务并生成所有活动的审核日志。

## <a name="authentication"></a>身份验证

如果 Azure Active Directory (Azure AD) 配置为使用多重身份验证, 则支持多重身份验证。 身份验证过程如下:

1. 客户端登录到 Azure AD 并获取 Azure 资源管理器令牌。  完全支持用户和服务主体。
1. 客户端将令牌提供给 Azure 资源管理器和所有 Azure 机器学习服务。
1. 机器学习服务向用户计算目标 (例如机器学习计算) 提供机器学习服务标记。 在运行完成后, 用户计算目标使用此标记回拨到机器学习服务。 范围限制为工作区。

[![Azure 机器学习服务中的身份验证](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Web 服务部署的身份验证

对于 web 服务, Azure 机器学习支持两种形式的身份验证: 密钥和令牌。 每个 web 服务一次只能启用一种形式的身份验证。

|身份验证方法|Azure 容器实例|AKS|
|---|---|---|
|Key|默认情况下禁用| 默认情况下启用|
|令牌| 不可用| 默认情况下禁用 |

#### <a name="authentication-with-keys"></a>密钥身份验证

为部署启用密钥身份验证时, 会自动创建身份验证密钥。

* 部署到 Azure Kubernetes Service (AKS) 时, 默认情况下会启用身份验证。
* 在部署到 Azure 容器实例时, 默认情况下禁用身份验证。

若要启用密钥身份验证, `auth_enabled`请在创建或更新部署时使用参数。

如果启用密钥身份验证, 则可以使用`get_keys`方法检索主要和辅助身份验证密钥:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如需重新生成密钥，请使用 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)。

#### <a name="authentication-with-tokens"></a>带令牌的身份验证

为 web 服务启用令牌身份验证时, 用户必须向 web 服务提供 Azure 机器学习 JSON Web 令牌才能访问它。

* 部署到 Azure Kubernetes 服务时, 令牌身份验证默认情况下处于禁用状态。
* 当你部署到 Azure 容器实例时, 令牌身份验证不受支持。

若要控制令牌身份验证, `token_auth_enabled`请在创建或更新部署时使用参数。

如果启用了令牌身份验证, 则可以使用`get_token`方法检索 JSON Web 令牌 (JWT), 并使用该令牌的过期时间:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 需要在令牌`refresh_by`时间之后请求新令牌。
>
> 强烈建议在 Azure Kubernetes Service 群集所在的同一区域中创建 Azure 机器学习工作区。 
>
> 若要使用令牌进行身份验证, web 服务将调用创建 Azure 机器学习工作区的区域。 如果工作区的区域不可用, 则即使群集与工作区位于不同的区域, 也无法获取 web 服务的令牌。 结果是, 在工作区的区域再次可用之前, Azure AD 身份验证不可用。 
>
> 此外, 群集区域与工作区区域之间的距离越大, 提取令牌所需的时间就越长。

## <a name="authorization"></a>Authorization

你可以创建多个工作区，并且每个工作区可由多个用户共享。 共享工作区时, 可以通过将这些角色分配给用户, 来控制对工作区的访问权限:

* 所有者
* 参与者
* 读者

下表列出了一些主要 Azure 机器学习服务操作和可执行这些操作的角色:

| Azure 机器学习服务操作 | 所有者 | 参与者 | 读者 |
| ---- |:----:|:----:|:----:|
| 创建工作区 | ✓ | ✓ | |
| 共享工作区 | ✓ | |  |
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

如果内置角色不能满足您的需要, 则可以创建自定义角色。 自定义角色仅支持工作区上的操作和机器学习计算。 自定义角色可以对工作区和该工作区中的计算资源具有读取、写入或删除权限。 可以使角色在特定的工作区级别、特定的资源组级别或特定的订阅级别可用。 有关详细信息, 请参阅[在 Azure 机器学习工作区中管理用户和角色](how-to-assign-roles.md)。

### <a name="securing-compute-targets-and-data"></a>保护计算目标和数据

所有者和参与者可以使用附加到工作区的所有计算目标和数据存储。  

每个工作区还具有一个关联的系统分配的托管标识, 该标识与工作区具有相同的名称。 托管标识对工作区中使用的附加资源具有以下权限。

有关托管标识的详细信息, 请参阅[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

| Resource | 权限 |
| ----- | ----- |
| 工作区 | 参与者 |
| 存储帐户 | 存储 Blob 数据参与者 |
| 密钥保管库 | 访问所有密钥、机密和证书 |
| Azure 容器注册表 | 参与者 |
| 包含工作区的资源组 | 参与者 |
| 包含密钥保管库的资源组 (如果不同于包含工作区的密钥保管库) | 参与者 |

建议管理员不要向上表中提到的资源撤销对托管标识的访问权限。 你可以使用 "重新同步密钥" 操作还原访问权限。

Azure 机器学习服务在每个工作区区域的订阅中创建`aml-`一个附加应用程序 (名称以开头) 和参与者级别的访问权限。 例如, 如果你在美国东部有一个工作区, 在同一订阅中的北欧中有另一个工作区, 你会看到其中两个应用程序。 通过这些应用程序, Azure 机器学习服务可以帮助你管理计算资源。

## <a name="network-security"></a>网络安全

Azure 机器学习服务依赖于其他 Azure 服务提供计算资源。 计算资源（计算目标）用于训练和部署模型。 可以在虚拟网络中创建这些计算目标。 例如, 你可以使用 Azure Data Science Virtual Machine 来训练模型, 然后将模型部署到 AKS。  

有关详细信息, 请参阅[如何在虚拟网络中运行试验和推理](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>数据加密

### <a name="encryption-at-rest"></a>静态加密

#### <a name="azure-blob-storage"></a>Azure Blob 存储

Azure 机器学习服务将快照、输出和日志存储在 Azure Blob 存储帐户中, 该帐户与 Azure 机器学习服务工作区和你的订阅相关联。 Azure Blob 存储中存储的所有数据都静态地与 Microsoft 托管密钥一起加密。

有关如何对存储在 Azure Blob 存储中的数据使用自己的密钥的信息, 请参阅[Azure Key Vault 中使用客户托管密钥的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)。

训练数据通常也存储在 Azure Blob 存储中, 因此可通过训练计算目标来进行访问。 此存储不受 Azure 机器学习管理, 但会装载到以远程文件系统的形式计算目标。

有关重新生成用于工作区的 Azure 存储帐户的访问密钥的信息, 请参阅[重新生成存储访问密钥](how-to-change-storage-access-key.md)。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure 机器学习服务将指标和元数据存储在与由 Azure 机器学习服务管理的 Microsoft 订阅关联的 Azure Cosmos DB 实例中。 存储在 Azure Cosmos DB 中的所有数据都是通过 Microsoft 管理的密钥加密的。

#### <a name="azure-container-registry"></a>Azure 容器注册表

注册表中的所有容器映像 (Azure 容器注册表) 都静态加密。 Azure 会在存储映像之前自动对其进行加密, 并在 Azure 机器学习服务拉入映像时, 自动将其解密。

#### <a name="machine-learning-compute"></a>机器学习计算

存储在 Azure 存储中的每个计算节点的 OS 磁盘都在 Azure 机器学习服务存储帐户中通过 Microsoft 托管的密钥进行加密。 此计算目标是暂时的, 当没有排队的运行时, 通常会缩小群集。 底层虚拟机已取消预配, 操作系统磁盘被删除。 OS 磁盘不支持 Azure 磁盘加密。

每个虚拟机还具有一个本地临时磁盘用于操作系统操作。 如果需要, 可以使用磁盘来暂存定型数据。 磁盘未加密。
若要深入了解 Azure 中的静态加密如何工作, 请参阅[静态 Azure 数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)。

### <a name="encryption-in-transit"></a>传输中加密

你可以使用 SSL 来保护 Azure 机器学习微服务之间的内部通信, 并保护对计分终结点的外部调用。 所有 Azure 存储访问也通过安全通道进行。

有关详细信息, 请参阅[通过 Azure 机器学习使用 SSL 来保护 web 服务](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure 机器学习服务使用与工作区关联的 Azure Key Vault 实例来存储各种类型的凭据:

* 关联的存储帐户连接字符串
* 密码到 Azure 容器存储库实例
* 数据存储的连接字符串

用于计算目标 (如 Azure HDInsight 和 Vm) 的 SSH 密码和密钥存储在与 Microsoft 订阅关联的单独密钥保管库中。 Azure 机器学习服务不会存储用户提供的任何密码或密钥。 相反, 它会生成、授权并存储自己的 SSH 密钥, 以连接到 Vm 和 HDInsight 来运行试验。

每个工作区都具有关联的系统分配的托管标识, 该标识与工作区同名。 此托管标识可以访问密钥保管库中的所有密钥、机密和证书。

## <a name="monitoring"></a>监视

### <a name="metrics"></a>指标

你可以使用 Azure Monitor 度量值来查看和监视 Azure 机器学习服务工作区的指标。 在[Azure 门户](https://portal.azure.com)中, 选择工作区, 然后选择 "**度量值**":

[![显示工作区的示例指标的屏幕截图](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

指标包括有关运行、部署和注册的信息。

有关详细信息，请参阅 [Azure Monitor 中的指标](/azure/azure-monitor/platform/data-platform-metrics)。

### <a name="activity-log"></a>活动日志

您可以查看工作区的活动日志, 以查看在工作区中执行的各种操作。 日志包含基本信息, 如操作名称、事件发起程序和时间戳。

此屏幕截图显示工作区的活动日志:

[![显示工作区的活动日志的屏幕截图](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

评分请求详细信息存储在 Application Insights 中。 创建工作区时, 会在订阅中创建 Application Insights。 记录的信息包括 HTTPMethod、UserAgent、ComputeType、RequestUrl、StatusCode、RequestId 和 Duration 等字段。

> [!IMPORTANT]
> Azure 机器学习工作区中的一些操作不会将信息记录到活动日志。 例如, 不会记录定型运行和模型注册的开始时间。
>
> 其中的一些操作显示在工作区的 "**活动**" 区域中, 但这些通知并不表示启动该活动的人员。

## <a name="data-flow-diagrams"></a>数据流关系图

### <a name="create-workspace"></a>创建工作区

下图显示了 "创建工作区" 工作流。

* 用户登录到某个受支持的 Azure 机器学习服务客户端 (Azure CLI、Python SDK、Azure 门户), 并请求适当的 Azure 资源管理器令牌 Azure AD。
* 用户调用 Azure 资源管理器来创建工作区。 
* Azure 资源管理器联系 Azure 机器学习服务资源提供程序来设置工作区。

创建工作区时, 会在用户的订阅中创建其他资源:

* Key Vault (用于存储机密)
* Azure 存储帐户 (包括 blob 和文件共享)
* Azure 容器注册表 (用于存储用于推理/计分和试验的 Docker 映像)
* Application Insights (用于存储遥测)

用户还可以根据需要预配附加到工作区 (如 Azure Kubernetes 服务或 Vm) 的其他计算目标。

[![创建工作区工作流](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>保存源代码 (训练脚本)

下图显示了代码快照工作流。

与 Azure 机器学习服务工作区关联的是包含源代码的目录 (试验) (训练脚本)。 这些脚本存储在本地计算机和云中 (在订阅的 Azure Blob 存储中)。 代码快照用于执行或检查历史审核。

[![代码快照工作流](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>培训

下图显示了定型工作流。

* 将调用 Azure 机器学习服务, 其中包含上一部分中保存的代码快照的快照 ID。
* Azure 机器学习服务创建一个运行 ID (可选) 和一个机器学习服务令牌, 计算目标 (如机器学习计算/Vm) 稍后使用该令牌与机器学习服务通信。
* 你可以选择托管计算目标 (如机器学习计算) 或非托管计算目标 (例如 Vm) 来运行你的培训作业。 以下是两种情况下的数据流:
   * Vm/HDInsight, 由 Microsoft 订阅中的密钥保管库中的 SSH 凭据访问。 Azure 机器学习服务在以下计算目标上运行管理代码:

   1. 准备环境。 (Docker 是 Vm 和本地计算机的选项。 请参阅以下步骤, 机器学习计算来了解如何在 Docker 容器上运行试验。)
   1. 下载代码。
   1. 设置环境变量和配置。
   1. 运行用户脚本 (上一节中提到的代码快照)。

   * 机器学习计算, 通过工作区管理的标识访问。
由于机器学习计算是托管计算目标 (即它由 Microsoft 管理), 因此它在你的 Microsoft 订阅下运行。

   1. 如果需要, 将启动远程 Docker 构造。
   1. 管理代码将写入用户的 Azure 文件共享。
   1. 使用初始命令启动容器。 也就是说, 按上一步所述的管理代码。

#### <a name="querying-runs-and-metrics"></a>查询运行和度量值

在下流关系图中, 当定型计算目标将运行指标从 Cosmos DB 数据库中的存储写入 Azure 机器学习服务时, 将发生此步骤。 客户端可以调用 Azure 机器学习服务。 机器学习将从 Cosmos DB 数据库中转而取度量值, 并将其返回给客户端。

[![训练工作流](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>创建 web 服务

下图显示了推理工作流。 推理或模型计分是部署模型用于预测的阶段, 最常见的是生产数据。

以下是详细信息：

* 用户使用类似于 Azure 机器学习 SDK 的客户端注册模型。
* 用户使用模型、分数文件和其他模型依赖项创建一个图像。
* 创建 Docker 映像并将其存储在 Azure 容器注册表中。
* Web 服务使用上一步中创建的映像部署到计算目标 (容器实例/AKS)。
* 评分请求详细信息存储在用户订阅中的 Application Insights 中。
* 遥测还会推送到 Microsoft/Azure 订阅。

[![推理工作流](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>后续步骤

* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 web 服务的机器学习模型](how-to-consume-web-service.md)
* [如何运行批量预测](how-to-run-batch-predictions.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [Azure 机器学习服务 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [在 Azure 虚拟网络中使用 Azure 机器学习服务](how-to-enable-virtual-network.md)
* [有关构建建议系统的最佳实践](https://github.com/Microsoft/Recommenders)
* [在 Azure 上生成实时建议 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
