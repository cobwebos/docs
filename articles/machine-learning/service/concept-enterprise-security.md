---
title: 企业安全性
titleSuffix: Azure Machine Learning service
description: 安全地使用 Azure 机器学习服务： 身份验证、 授权、 网络安全、 数据加密和监视。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: 9762b8cadde86a2e64f8fa74a4e794bdf1109ec4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151192"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Azure 机器学习服务的企业安全性

在本文中，您将了解到有关可用的安全功能与 Azure 机器学习服务。

使用云服务时，它是最佳做法来限制仅对需要它的用户访问。 这将启动通过了解服务使用的身份验证和授权模型。 可能还想要限制的网络访问或安全地将资源加入与云中的本地网络中。 数据加密非常重要，同时处于静态时和数据的移动服务之间。 最后，您需要能够监视服务和生成的所有活动的审核日志。

## <a name="authentication"></a>Authentication
如果 Azure Active Directory (Azure AD) 配置为相同，则支持多因素身份验证。
* 客户端登录到 Azure AD，并获取 Azure 资源管理器令牌。  完全支持用户和服务主体。
* 客户端提供的令牌向 Azure 资源管理器和所有 Azure 机器学习服务
* Azure 机器学习服务提供了用户计算的 Azure 机器学习令牌。 例如，机器学习计算。 此令牌由用户的 Azure 机器学习计算回调到 Azure 机器学习服务 （范围限制到工作区） 在运行后已完成。

![显示在 Azure 机器学习服务中的身份验证工作原理的屏幕截图](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>Web 服务部署的身份验证密钥

为部署启用身份验证时，会自动创建身份验证密钥。

* 部署到 Azure Kubernetes 服务时，会默认启用身份验证。
* 部署到 Azure 容器实例时，会默认禁用身份验证。

若要控制身份验证，请在创建或更新部署时使用 `auth_enabled` 参数。

如果已启用身份验证，可以使用 `get_keys` 方法来检索主要和辅助身份验证密钥：

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果你需要重新生成密钥，使用 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>授权

你可以创建多个工作区，并且每个工作区可由多个用户共享。 共享工作区时，通过向用户分配以下角色来控制对该工作区的访问：
* 所有者
* 参与者
* 读取器
    
下表列出了一些主要的 Azure 机器学习服务操作以及可以执行它们的角色：

| Azure 机器学习服务操作 | 所有者 | 参与者 | 读取器 |
| ---- |:----:|:----:|:----:|
| 创建工作区 | ✓ | ✓ | |
| 共享工作区 | ✓ | |  |
| 创建计算 | ✓ | ✓ | |
| 附加计算 | ✓ | ✓ | |
| 附加数据存储 | ✓ | ✓ | |
| 运行试验 | ✓ | ✓ | |
| 查看运行数指标 | ✓ | ✓ | ✓ |
| 注册模型 | ✓ | ✓ | |
| 创建映像 | ✓ | ✓ | |
| 部署 Web 服务 | ✓ | ✓ | |
| 视图模型/图像 | ✓ | ✓ | ✓ |
| 调用 web 服务 | ✓ | ✓ | ✓ |

如果内置角色不能满足需要，还可以创建自定义角色。 请注意，仅自定义角色，我们支持的工作区和机器学习计算上的操作。 自定义的角色可能具有读取、 写入或删除工作区和该工作区中的计算资源上的权限。 您可以使该角色可用在特定工作区级别、 特定资源组级别或特定的订阅级别。 有关详细信息，请参阅[管理用户和 Azure 机器学习工作区中的角色](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>计算和数据保护
所有者和参与者可以使用的所有计算目标和数据存储附加到工作区。  
每个工作区还具有关联系统分配的托管标识 （使用相同的名称与工作区） 与在工作区中使用的附加资源的以下权限：

有关管理的标识的详细信息，请参阅[托管为 Azure 资源的标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Resource | 权限 |
| ----- | ----- |
| 工作区 | 参与者 | 
| 存储帐户 | 存储 Blob 数据参与者 | 
| Key Vault | 访问所有密钥，机密，证书 | 
| Azure 容器注册表 | 参与者 | 
| 包含的工作区的资源组 | 参与者 | 
| 包含密钥保管库 （如果不同于包含工作区） 的资源组 | 参与者 | 

建议管理员不会撤消该托管身份与上面所述的资源的访问权限。 可以使用重新同步密钥操作恢复访问权限。

Azure 机器学习服务创建参与者级别访问你的订阅中的每个工作区区域与其他应用程序 （使用 aml-名称开始）。 有关示例。 如果你有一个工作区在美国东部和同一订阅中另一个在欧洲北部中的工作区，您将看到 2 个此类应用程序。 这被必需的以便服务可以帮助管理 Azure 机器学习计算资源。


## <a name="network-security"></a>网络安全

Azure 机器学习服务依赖于其他 Azure 服务提供计算资源。 计算资源（计算目标）用于训练和部署模型。 可在虚拟网络中创建这些计算目标。 例如，可以使用 Microsoft Data Science Virtual Machine 来训练模型，然后将该模型部署到 Azure Kubernetes 服务 (AKS)。  

有关详细信息，请参阅[如何在虚拟网络中运行试验和推理](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>数据加密

### <a name="encryption-at-rest"></a>静态加密
#### <a name="azure-blob-storage"></a>Azure Blob 存储
Azure 机器学习服务绑定到的 Azure 机器学习服务工作区，并驻留在用户的订阅的 Azure Blob 存储帐户中存储快照、 输出和日志。 静态使用 Microsoft-Managed 密钥进行加密存储在 Azure Blob 存储中的所有数据。

有关如何将你自己的密钥存储在 Azure Blob 存储中的数据的详细信息，请参阅[使用 Azure Key Vault 中客户托管密钥的存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)。

定型数据通常也存储在 Azure Blob 存储，以便训练计算对其进行访问。 此存储不是由 Azure 机器学习而装载来计算为远程文件系统。

#### <a name="cosmos-db"></a>Cosmos DB
Azure 机器学习服务中管理 Azure 机器学习服务的 Microsoft 订阅存储指标和居住在 Cosmos DB 的元数据。 使用 Microsoft 托管密钥的其余部分进行加密存储在 Cosmos DB 中的所有数据。

#### <a name="azure-container-registry-acr"></a>Azure 容器注册表 (ACR)
注册表 (ACR) 中的所有容器映像进行静态都加密。 Azure 会自动对映像存储在存储之前进行加密和解密它快速在 Azure 机器学习服务中拉取映像。

#### <a name="machine-learning-compute"></a>机器学习计算
OS 磁盘的每个计算节点存储在 Azure 存储进行加密在 Azure 机器学习服务存储帐户中使用 Microsoft 托管密钥。 此计算是暂时的并没有运行时，向下通常缩放群集排队。 基础虚拟机是取消预配和删除操作系统磁盘。 OS 磁盘不支持 azure 磁盘加密。
每个虚拟机还具有本地临时磁盘的操作系统操作。 此磁盘还可以根据需要使用定型数据暂存到中。 此磁盘不会加密。 在 Azure 中的静态加密工作原理的详细信息，请参阅[Azure 数据加密在 Rest](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)。 

### <a name="encryption-in-transit"></a>传输中加密
这两个不同的 Azure 机器学习微服务和外部通信的调用的评分终结点之间的内部通信使用 SSL 支持。 Azure 存储的所有访问也都是通过安全通道。 有关详细信息，请参阅[使用 SSL 保护 Azure 机器学习 web 服务](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault
Azure 机器学习服务使用与工作区关联的 Key Vault 实例来存储不同类型的凭据：
* 关联的存储帐户连接字符串
* 到 Azure 容器存储库实例密码
* 连接字符串应用于数据存储。 

SSH 密码和密钥计算目标，例如 HDI HDInsight 和 VM 存储在单独的密钥保管库与 Microsoft 订阅相关联。 Azure 机器学习服务确实存储任何密码或密钥提供的用户改为其生成，授权，并将其自己的 SSH 密钥存储以连接到 VM/HDInsight 中运行试验。 每个工作区都有关联系统分配的托管标识 （使用相同的名称与工作区） 的密钥保管库中有权访问所有密钥、 机密和证书。

 
## <a name="monitoring"></a>监视

用户可以看到要查看在工作区执行各种操作并获取类似的操作名称、 事件发起者、 时间戳等的基本信息的工作区下的活动日志。

下面的屏幕截图显示了一个工作区的活动日志：

![在工作区的屏幕截图显示活动日志](./media/enterprise-readiness/workspace-activity-log.png)


评分请求详细信息存储在 AppInsights，在用户的订阅时创建工作区中创建的。 这包括如 HTTPMethod、 UserAgent、 ComputeType、 RequestUrl、 StatusCode、 RequestId、 持续时间等的字段。


## <a name="data-flow-diagram"></a>数据流关系图

### <a name="create-workspace"></a>创建工作区
下图显示了创建工作区中工作流。
用户登录到 Azure AD 中从任何支持的 Azure 机器学习服务客户端 （CLI 中，Python SDK 中，Azure 门户），并请求相应的 Azure 资源管理器令牌。  然后，用户调用 Azure 资源管理器用于创建工作区。  Azure 资源管理器联系人 Azure 机器学习服务资源提供程序来预配工作区。  在创建工作区的客户的订阅中创建其他资源：
* 密钥保管库 （用于存储机密）
* Azure 存储帐户 （包括 Blob 和文件共享）
* Azure 容器注册表 （用于存储 docker 映像用于推断/评分和试验）
* Application Insights （用于存储遥测数据）

根据需要还可以通过客户设置附加到工作区 （Azure Kubernetes 服务、 VM 等） 其他计算资源。 

![显示的屏幕截图创建工作区的工作流](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>保存源代码 （训练脚本）
下图显示了代码快照工作流。
与 Azure 机器学习服务工作区是目录 （试验），其中包含的源代码 （训练脚本）。  它们存储在客户的本地计算机上和在云中 （在 Azure Blob 存储客户的订阅下）。 这些代码快照或所使用的执行历史审核的检查。

![显示的屏幕截图创建工作区的工作流](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>培训
下图显示了训练工作流。
* 上述操作中存储代码快照 azure 机器学习服务调用使用快照 ID
* Azure 机器学习服务创建运行 ID （可选） 和 Azure 机器学习服务令牌，用于更高版本的计算目标等机器学习计算/VM 与 Azure 机器学习服务
* 你可以选择任一托管的计算 （例如。 机器学习计算） 或非托管的计算 （例如。 VM) 以运行训练作业。 下面这两种方案解释了数据流：
* (VM/HDInsight-Microsoft 订阅中的密钥保管库中使用 SSH 凭据访问)Azure 机器学习服务在运行管理代码计算目标上的：
    1.  准备环境 (请注意：Docker 是也 VM/本地选项。 有关机器学习计算下若要了解如何运行试验 docker 容器的工作原理，请参阅步骤）
    2.  下载代码
    3.  设置环境变量/配置
    4.  运行用户脚本 （上述代码快照）
* （机器学习计算 – 使用工作区管理标识访问）请注意，由于机器学习计算是托管的计算，它是由 Microsoft 管理，因此 Microsoft 订阅下运行。
    1.  如果需要远程 Docker 构造将启动
    2.  将管理代码写入到 Azure 文件共享用户
    3.  初始启动容器，即命令管理上述步骤中的代码


#### <a name="querying-runs--metrics"></a>查询运行和度量值
此步骤中所示的培训计算写入的流*运行指标*回 Azure 机器学习服务从 Cosmos DB 中获取存储位置。 客户端可以调用 Azure 机器学习服务会反过来请求从 Cosmos DB 指标并将其返回返回给客户端。

![显示的屏幕截图创建工作区的工作流](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>创建 web 服务
下图显示了推理工作流。 推理，或模型评分，阶段已部署的模型不使用的是用于预测，最常在生产数据。
请参阅下面的详细信息：
* 用户注册使用 Azure ML SDK 等的客户端的模型
* 用户创建使用模型、 评分文件和其他模型的依赖项的图像
* 创建 Docker 映像并将其存储在 ACR
* Web 服务部署到计算目标 (ACI/AKS) 使用上面创建的映像
* 评分请求详细信息存储在 AppInsights，这是用户的订阅中
* 遥测也会推送到 Microsoft/Azure 订阅

![显示的屏幕截图创建工作区的工作流](./media/enterprise-readiness/inferencing.png)

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
