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
ms.openlocfilehash: d1ad89943f6acfec6e42199ef399643be12e2b8b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856238"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Azure 机器学习服务的企业安全性

在本文中, 你将了解 Azure 机器学习服务中可用的安全功能。

使用云服务时, 最佳做法是仅将访问权限限制为需要它的用户。 首先了解服务使用的身份验证和授权模型。 你可能还希望限制网络访问, 或将本地网络中的资源安全地加入到云中。 数据加密也是非常重要的, 无论是静态的还是在服务之间移动数据。 最后, 您需要能够监视服务并生成所有活动的审核日志。

## <a name="authentication"></a>身份验证

如果为相同的配置 Azure Active Directory (Azure AD), 则支持多重身份验证。

* 客户端登录 Azure AD, 并获取 Azure 资源管理器令牌。  完全支持用户和服务主体。
* 客户端将令牌提供给 Azure 资源管理器 & 所有 Azure 机器学习服务
* Azure 机器学习服务向用户计算提供 Azure 机器学习令牌。 例如, 机器学习计算。 在运行完成后, 用户计算使用此 Azure 机器学习令牌回调到 Azure 机器学习服务 (限制作用域)。

![显示身份验证在 Azure 机器学习服务中的工作方式的屏幕截图](./media/enterprise-readiness/authentication.png)

### <a name="authentication-for-web-service-deployment"></a>Web 服务部署的身份验证

Azure 机器学习为 web 服务、密钥和令牌支持两种形式的身份验证。 每个 webservice 一次只能启用一种形式的身份验证。

|身份验证方法|ACI|AKS|
|---|---|---|
|Key|默认情况下禁用| 默认情况下启用|
|令牌| 不可用| 默认情况下禁用 |

#### <a name="authentication-with-keys"></a>密钥身份验证

为部署启用密钥身份验证时, 会自动创建身份验证密钥。

* 部署到 Azure Kubernetes 服务时，会默认启用身份验证。
* 部署到 Azure 容器实例时，会默认禁用身份验证。

若要启用密钥身份验证, `auth_enabled`请在创建或更新部署时使用参数。

如果启用密钥身份验证, 则可以使用`get_keys`方法检索主要和辅助身份验证密钥:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果需要重新生成密钥, 请使用[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>带令牌的身份验证

为 web 服务启用令牌身份验证时, 用户必须为 web 服务提供 Azure 机器学习的 JSON Web 令牌才能访问该令牌。

* 在部署到 Azure Kubernetes 服务时, 令牌身份验证默认情况下处于禁用状态。
* 在部署到 Azure 容器实例时, 令牌身份验证不受支持。

若要控制令牌身份验证, `token_auth_enabled`请在创建或更新部署时使用参数。

如果启用令牌身份验证, 则可以使用`get_token`方法检索 JWT 令牌和令牌的过期时间:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 需要在令牌`refresh_by`时间之后请求新令牌。
>
> 强烈建议在 Azure Kubernetes Service 群集所在的同一区域中创建 Azure 机器学习工作区。 若要使用令牌进行身份验证, web 服务将调用创建 Azure 机器学习工作区的区域。 如果工作区的区域不可用, 则即使群集与工作区位于不同的区域, 也不能为 webservice 获取令牌。 这实际上会导致 Azure AD 身份验证不可用, 直到工作区的区域再次可用。 此外, 群集区域与工作区区域之间的距离越大, 提取令牌所需的时间就越长。

## <a name="authorization"></a>Authorization

你可以创建多个工作区，并且每个工作区可由多个用户共享。 共享工作区时，通过向用户分配以下角色来控制对该工作区的访问：

* 所有者
* 参与者
* 读者

下表列出了一些主要 Azure 机器学习服务操作和可执行这些操作的角色:

| Azure 机器学习服务操作 | 所有者 | 参与者 | 读者 |
| ---- |:----:|:----:|:----:|
| 创建工作区 | ✓ | ✓ | |
| 共享工作区 | ✓ | |  |
| 创建计算 | ✓ | ✓ | |
| 附加计算 | ✓ | ✓ | |
| 附加数据存储 | ✓ | ✓ | |
| 运行试验 | ✓ | ✓ | |
| 查看运行/度量值 | ✓ | ✓ | ✓ |
| 注册模型 | ✓ | ✓ | |
| 创建映像 | ✓ | ✓ | |
| 部署 Web 服务 | ✓ | ✓ | |
| 查看模型/图像 | ✓ | ✓ | ✓ |
| 调用 web 服务 | ✓ | ✓ | ✓ |

如果内置角色不足以满足你的需求, 你还可以创建自定义角色。 我们支持的自定义角色仅适用于工作区上的操作和机器学习计算。 自定义角色可能对工作区和该工作区中的计算资源具有读取、写入或删除权限。 可以使角色在特定的工作区级别、特定的资源组级别或特定的订阅级别可用。 有关详细信息, 请参阅[在 Azure 机器学习工作区中管理用户和角色](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>保护计算和数据

所有者和参与者可以使用附加到工作区的所有计算目标和数据存储。  
对于工作区中使用的附加资源, 每个工作区还具有关联的系统分配的托管标识 (具有与工作区相同的名称):

有关托管标识的详细信息, 请参阅[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Resource | 权限 |
| ----- | ----- |
| 工作区 | 参与者 |
| 存储帐户 | 存储 Blob 数据参与者 |
| Key Vault | 访问所有密钥、机密和证书 |
| Azure 容器注册表 | 参与者 |
| 包含工作区的资源组 | 参与者 |
| 包含 Key Vault 的资源组 (如果不同于包含工作区的资源组) | 参与者 |

建议管理员不要撤销对上述资源的托管标识的访问权限。 可以使用 "重新同步密钥" 操作还原访问权限。

Azure 机器学习服务会创建一个附加的应用程序 ( `aml-`名称以开头), 并在订阅中为每个工作区区域提供参与者级别访问权限。 例如， 如果在美国东部的工作区和另一个在同一订阅中北欧的工作区中, 你将看到两个此类应用程序。 这是必需的, 以便 Azure 机器学习服务可以帮助管理计算资源。

## <a name="network-security"></a>网络安全

Azure 机器学习服务依赖于其他 Azure 服务提供计算资源。 计算资源（计算目标）用于训练和部署模型。 可在虚拟网络中创建这些计算目标。 例如，可以使用 Microsoft Data Science Virtual Machine 来训练模型，然后将该模型部署到 Azure Kubernetes 服务 (AKS)。  

有关详细信息, 请参阅[如何在虚拟网络中运行试验和推理](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>数据加密

### <a name="encryption-at-rest"></a>静态加密

#### <a name="azure-blob-storage"></a>Azure Blob 存储

Azure 机器学习服务将快照、输出和日志存储在 Azure Blob 存储帐户中, 该帐户与 Azure 机器学习服务工作区关联, 并位于用户的订阅中。 Azure Blob 存储中存储的所有数据都使用 Microsoft 托管的密钥进行静态加密。

有关如何为 Azure Blob 存储中存储的数据自带密钥的详细信息, 请参阅[Azure Key Vault 中使用客户托管密钥的存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)。

训练数据通常也存储在 Azure Blob 存储中, 以便可以对其进行定型计算。 此存储不由 Azure 机器学习管理, 但装载为作为远程文件系统进行计算。

有关重新生成用于工作区的 Azure 存储帐户的访问密钥的信息, 请参阅 "[重新生成存储访问密钥](how-to-change-storage-access-key.md)" 一文。

#### <a name="cosmos-db"></a>Cosmos DB

Azure 机器学习服务可将指标和元数据存储到 Azure 机器学习服务管理的 Microsoft 订阅中的 Cosmos DB。 使用 Microsoft 托管密钥以静态加密存储在 Cosmos DB 中的所有数据。

#### <a name="azure-container-registry-acr"></a>Azure 容器注册表 (ACR)

注册表 (ACR) 中的所有容器映像都静态加密。 Azure 会在存储映像之前自动对其进行加密, 并在 Azure 机器学习服务拉入映像时即时将其解密。

#### <a name="machine-learning-compute"></a>机器学习计算

每个计算节点的 OS 磁盘存储在 Azure 存储中, 使用 Microsoft 托管密钥在 Azure 机器学习服务存储帐户中进行加密。 此计算是暂时的, 当没有排队的排队时, 通常会缩小群集。 底层虚拟机已取消预配, 操作系统磁盘已删除。 OS 磁盘不支持 Azure 磁盘加密。
每个虚拟机还具有一个本地临时磁盘用于操作系统操作。 还可以选择使用此磁盘来暂存定型数据。 此磁盘未加密。
若要深入了解 Azure 中的静态加密如何工作, 请参阅[Azure 静态数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)。

### <a name="encryption-in-transit"></a>传输中加密

使用 SSL 时, 支持各种 Azure 机器学习微服务与调用评分终结点的外部通信之间的内部通信。 所有 Azure 存储访问也通过安全通道进行。
有关详细信息, 请参阅[使用 SSL 保护 Azure 机器学习 web 服务](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure 机器学习服务使用与工作区关联 Key Vault 实例来存储各种类型的凭据:

* 关联的存储帐户连接字符串
* 密码到 Azure 容器存储库实例
* 数据存储的连接字符串。

用于计算目标 (如 HDI HDInsight 和 VM) 的 SSH 密码和密钥存储在与 Microsoft 订阅关联的单独 Key Vault 中。 Azure 机器学习服务不会存储用户提供的任何密码或密钥, 而是生成、授权并存储自己的 SSH 密钥, 以便连接到 VM/HDInsight 来运行试验。
每个工作区都有一个关联的系统分配的托管标识 (与工作区具有相同的名称), 可以访问 Key Vault 中的所有密钥、机密和证书。

## <a name="monitoring"></a>监视

### <a name="metrics"></a>指标

Azure Monitor 指标可用于查看和监视 Azure 机器学习服务工作区的指标。 在[Azure 门户](https://portal.azure.com)中, 选择工作区, 然后使用 "__指标__" 链接。

![显示工作区的示例指标的屏幕截图](./media/enterprise-readiness/workspace-metrics.png)

指标包括有关运行、部署和注册的信息。

有关详细信息，请参阅 [Azure Monitor 中的指标](/azure/azure-monitor/platform/data-platform-metrics)。

### <a name="activity-log"></a>活动日志

你可以在工作区中查看活动日志, 以查看在工作区中执行的各种操作, 并获取基本信息, 如操作名称、事件启动者、时间戳等。

以下屏幕截图显示工作区的活动日志:

![显示工作区下的活动日志的屏幕截图](./media/enterprise-readiness/workspace-activity-log.png)

评分请求详细信息存储在 AppInsights 中, 在创建工作区时在用户的订阅中创建。 其中包括 HTTPMethod、UserAgent、ComputeType、RequestUrl、StatusCode、RequestId、Duration 等等字段。

## <a name="data-flow-diagram"></a>数据流关系图

### <a name="create-workspace"></a>创建工作区

下图显示了 "创建工作区" 工作流。
用户从任何支持的 Azure 机器学习服务客户端 (CLI、Python SDK、Azure 门户) 登录 Azure AD, 并请求适当的 Azure 资源管理器令牌。 然后, 用户调用 Azure 资源管理器来创建工作区。  Azure 资源管理器联系 Azure 机器学习服务资源提供程序来设置工作区。  创建工作区时在客户的订阅中创建其他资源:

* KeyVault (用于存储机密)
* 一个 Azure 存储帐户 (包括 Blob & 文件共享)
* Azure 容器注册表 (用于存储用于推理/计分和试验的 docker 映像)
* Application Insights (用于存储遥测)

附加到工作区的其他计算 (Azure Kubernetes 服务、VM 等) 还可以根据需要由客户进行设置。

![显示创建工作区工作流的屏幕截图](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>保存源代码 (训练脚本)

下图显示了代码快照工作流。
与 Azure 机器学习服务工作区关联的是目录 (试验), 其中包含源代码 (训练脚本)。  它们存储在客户的本地计算机和云中 (在客户的订阅下的 Azure Blob 存储中)。 这些代码快照用于执行或检查历史审核。

![显示创建工作区工作流的屏幕截图](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>培训

下图显示了定型工作流。

* 将通过上面保存的代码快照的快照 ID 调用 Azure 机器学习服务
* Azure 机器学习服务创建运行 ID (可选) & Azure 机器学习服务令牌, 该令牌稍后由机器学习计算/VM 等计算目标用来反馈 Azure 机器学习服务
* 你可以选择托管计算 (例如 机器学习计算) 或非托管计算 (例如 VM) 来运行您的培训作业。 以下两种方案都说明了数据流:
* (VM/HDInsight-在 Microsoft 订阅中 Key Vault 中使用 SSH 凭据进行访问)Azure 机器学习服务在以下计算目标上运行管理代码:

   1. 准备环境。 (请注意, Docker 也是适用于 VM 和本地的选项。 请参阅以下步骤, 机器学习计算来了解如何在 docker 容器上运行试验。)
   1. 下载代码。
   1. 设置环境变量和配置。
   1. 运行用户脚本 (上面提到的代码快照)。

* (机器学习计算–使用工作区托管标识访问)请注意, 由于机器学习计算是托管计算, 因此它由 Microsoft 管理, 因此它在 Microsoft 订阅下运行。

   1. 如果需要, 将启动远程 Docker 构造。
   1. 将管理代码写入用户 Azure 文件共享。
   1. 使用初始命令 (即, 上一步骤中所述的管理代码) 启动容器。

#### <a name="querying-runs-and-metrics"></a>查询运行和度量值

此步骤显示在定型计算将*运行度量*写入 Cosmos DB 中存储的 Azure 机器学习服务的流中。 客户端可以调用 Azure 机器学习服务, 该服务将从 Cosmos DB 中打开拉取指标, 并将其返回给客户端。

![显示创建工作区工作流的屏幕截图](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>创建 web 服务

下图显示了推理工作流。 推理或模型计分是部署模型用于预测的阶段, 最常见的是生产数据。
请参阅下面的详细信息:

* 用户使用类似 Azure ML SDK 的客户端注册模型
* 用户使用模型、分数文件和其他模型依赖项创建图像
* Docker 映像将创建并存储在 ACR 中
* Webservice 使用上面创建的映像部署到计算目标 (ACI/AKS)
* 评分请求详细信息存储在 AppInsights 中, 后者位于用户的订阅中
* 遥测还会推送到 Microsoft/Azure 订阅

![显示创建工作区工作流的屏幕截图](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>后续步骤

* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [如何运行批量预测](how-to-run-batch-predictions.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [Azure 机器学习服务 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [通过 Azure 虚拟网络使用 Azure 机器学习服务](how-to-enable-virtual-network.md)
* [有关构建建议系统的最佳实践](https://github.com/Microsoft/Recommenders)
* [在 Azure 上生成实时建议 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
