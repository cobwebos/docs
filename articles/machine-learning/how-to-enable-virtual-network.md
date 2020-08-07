---
title: 网络隔离和隐私
titleSuffix: Azure Machine Learning
description: 结合使用独立的 Azure 虚拟网络与 Azure 机器学习来保护试验/训练作业以及推理/评分作业。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 7684933a7671f1f3d573cb8195551a56ebc27947
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925815"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>使用专用虚拟网络进行训练和推理期间的网络隔离
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在 Azure 虚拟网络 (vnet) 中通过隔离 Azure 机器学习训练和推理作业来保护你的机器学习生命周期。 Azure 机器学习依赖于用于计算资源的其他 Azure 服务（也称为[计算目标](concept-compute-target.md)）来定型和部署模型。 可以在虚拟网络中创建这些目标。 例如，可以使用 Azure 机器学习计算来训练模型，然后将模型部署到 Azure Kubernetes 服务 (AKS)。 

__虚拟网络__充当安全边界，可将 Azure 资源与公共 Internet 相隔离。 你也可以将 Azure 虚拟网络加入本地网络。 加入网络后，可以安全训练模型，以及访问用于推理的已部署模型。

## <a name="prerequisites"></a>先决条件

+ 一个 Azure 机器学习[工作区](how-to-manage-workspace.md)。

+ 对 [Azure 虚拟网络服务](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)和 [IP 网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)具备一般性的实践知识。

+ 用于计算资源的预先存在的虚拟网络和子网。

## <a name="private-endpoints"></a>专用终结点

还可以[启用 Azure 专用链接](how-to-configure-private-link.md)，以使用专用终结点连接到工作区。 专用终结点是虚拟网络中的一组专用 IP 地址。 [了解如何设置此专用终结点](how-to-configure-private-link.md)。



> [!TIP]
> 可以结合使用虚拟网络和专用链接，以保护你的工作区和其他 Azure 资源之间的通信。 不过，有些组合需要 Enterprise Edition 工作区。 若要了解哪些方案需要 Enterprise Edition，请参阅下表：
>
> | 方案 | Enterprise</br>edition | 基本</br>edition |
> | ----- |:-----:|:-----:| 
> | 无虚拟网络或专用链接 | ✔ | ✔ |
> | 无专用链接的工作区。 虚拟网络中的其他资源（Azure 容器注册表除外） | ✔ | ✔ |
> | 无专用链接的工作区。 有专用链接的其他资源 | ✔ | |
> | 有专用链接的工作区。 虚拟网络中的其他资源（Azure 容器注册表除外） | ✔ | ✔ |
> | 有专用链接的工作区和其他任何资源 | ✔ | |
> | 有专用链接的工作区。 无专用链接或虚拟网络的其他资源 | ✔ | ✔ |
> | 虚拟网络中的 Azure 容器注册表 | ✔ | |
> | 工作区的客户托管密钥 | ✔ | |
> 

> [!WARNING]
> 
> 在已启用专用链接的工作区中，不支持 Azure 机器学习计算实例预览。

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>机器学习工作室

如果你的数据存储在虚拟网络中，则必须使用工作区[托管标识](../active-directory/managed-identities-azure-resources/overview.md)授予工作室对你的数据的访问权限。

> [!IMPORTANT]
> 尽管大多数 studio 都适用于虚拟网络中存储的数据，但集成笔记本不__会__。 集成笔记本不支持使用虚拟网络中的存储。 相反，可以使用来自计算实例的 Jupyter 笔记本。 有关详细信息，请参阅[访问计算实例笔记本中的数据](#access-data-in-a-compute-instance-notebook)部分。

如果无法向工作室授予访问权限，你将收到“`Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.`”错误，以下操作会被禁用：

* 预览工作室中的数据。
* 在设计器中将数据可视化。
* 提交 AutoML 试验。
* 启动标记项目。

工作室支持从虚拟网络中的以下数据存储类型读取数据：

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL 数据库

### <a name="add-resources-to-the-virtual-network"></a>向虚拟网络中添加资源 

将你的工作区和存储帐户添加到同一虚拟网络，以便它们可以相互访问。

1. 若要将工作区连接到虚拟网络，请[启用 Azure 专用链接](how-to-configure-private-link.md)。 此功能目前以预览版形式在“美国东部”、“美国西部 2”、“美国中南部”区域提供。

1. 若要将存储帐户连接到虚拟网络，请[配置防火墙和虚拟网络设置](#use-a-storage-account-for-your-workspace)。

### <a name="configure-a-datastore-to-use-managed-identity"></a>将数据存储配置为使用托管标识

将工作区和存储服务帐户添加到虚拟网络后，你需要将数据存储配置为使用托管标识来访问数据。 这些步骤使用 Azure 基于资源的访问控制 (RBAC) 将工作区托管标识作为“读取者”添加到存储服务。 “读取者”访问权限允许工作区检索防火墙设置，并确保数据不会离开虚拟网络。

1. 在工作室中，选择“数据存储”。

1. 若要创建新的数据存储，请选择“+ 新建数据存储”。 若要更新现有的数据存储，请选择相应的数据存储并选择“更新凭据”。

1. 在数据存储设置中，对于“允许 Azure 机器学习服务使用工作区托管标识来访问存储”，选择“是”。 

> [!NOTE]
> 这些更改可能需要长达 10 分钟才能生效。

### <a name="azure-blob-storage-blob-data-reader"></a>Azure Blob 存储 Blob 数据读取者

对于 Azure Blob 存储，还会将工作区托管标识添加为 [Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)，以便它能够从 Blob 存储读取数据。


### <a name="azure-machine-learning-designer-default-datastore"></a>Azure 机器学习设计器默认数据存储

默认情况下，设计器使用附加到工作区的存储帐户来存储输出。 不过，您可以指定它将输出存储到您有权访问的任何数据存储。 如果你的环境使用虚拟网络，则可以使用这些控件确保你的数据保持安全且可访问。

为管道设置新的默认存储：

1. 在 "管道草稿" 中，选择管道标题附近的 "**设置" 齿轮图标**。
1. 选择 "**选择默认数据存储**"。
1. 指定新的数据存储。

还可以基于每个模块覆盖默认数据存储。 这使您可以控制每个模块的存储位置。

1. 选择要指定其输出的模块。
1. 展开 "**输出设置**" 部分。
1. 选择 "**替代默认输出设置**"。
1. 选择 "**设置输出设置**"。
1. 指定新的数据存储。

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 访问控制

你可以使用 RBAC 和 POSIX 样式的访问控制列表 (ACL) 来控制虚拟网络内的数据访问。

若要使用 RBAC，请将工作区托管标识添加到 [Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色。 有关详细信息，请参阅 azure [RBAC)  (azure 基于角色的访问控制](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control)。

若要使用 ACL，可以向工作区托管标识分配访问权限，就像向任何其他安全主体分配访问权限一样。 有关详细信息，请参阅[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。


### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 访问控制

Azure Data Lake Storage Gen1 仅支持 POSIX 样式的访问控制列表。 可以像其他任何安全原则一样，将工作区托管标识访问权限分配给资源。 有关详细信息，请参阅[Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。


### <a name="azure-sql-database-contained-user"></a>Azure SQL 数据库包含用户

若要使用托管标识访问存储在 Azure SQL 数据库中的数据，必须创建一个映射到托管标识的 SQL 包含用户。 若要详细了解如何从外部提供程序创建用户，请参阅[创建映射到 Azure AD 标识的包含用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。

创建 SQL 包含用户后，使用 [GRANT T-SQL 命令](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)向该用户授予权限。

### <a name="connect-to-the-studio"></a>连接到工作室

如果要从虚拟网络内的资源（例如，计算实例或虚拟机）访问工作室，则必须允许从虚拟网络到工作室的出站流量。 

例如，如果使用网络安全组 (NSG) 来限制出站流量，请将一条规则添加到__服务标记__目标 __AzureFrontDoor.Frontend__。

## <a name="use-a-storage-account-for-your-workspace"></a>对工作区使用存储帐户

> [!IMPORTANT]
> 可将 Azure 机器学习的默认存储帐户或者将非默认存储帐户放在虚拟网络中。 
>
> 创建工作区时，会自动预配默认存储帐户。
>
> 对于非默认存储帐户，可以使用 [`Workspace.create()` 函数](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)中的 `storage_account` 参数按 Azure 资源 ID 指定自定义的存储帐户。

若要将 Azure 存储服务用于虚拟网络中的工作区，请执行以下步骤：

1. 在虚拟网络的后面创建一个计算资源（例如机器学习计算实例或群集），或者将某个计算资源（例如 HDInsight 群集、虚拟机或 Azure Kubernetes 服务群集）附加到工作区。 该计算资源可用于试验或模型部署。

   有关详细信息，请参阅本文的[使用机器学习计算](#amlcompute)、[使用虚拟机或 HDInsight 群集](#vmorhdi)和[使用 Azure Kubernetes 服务](#aksvnet)部分。

1. 在 Azure 门户中，转到你要在工作区中使用的存储服务。

   [![已附加到 Azure 机器学习工作区的存储](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. 在存储服务帐户页上，选择“防火墙和虚拟网络”。

   ![Azure 门户中“Azure 存储”页上的“防火墙和虚拟网络”区域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 在“防火墙和虚拟网络”页上执行以下操作：
    - 选择“所选网络”。
    - 在“虚拟网络”下，选择“添加现有的虚拟网络”链接。  此操作将添加计算资源所在的虚拟网络（参阅步骤 1）。

        > [!IMPORTANT]
        > 存储帐户必须与用于训练或推理的计算实例或群集位于同一虚拟网络和子网中。

    - 选中“允许受信任的 Microsoft 服务访问此存储帐户”复选框。

    > [!IMPORTANT]
    > 使用 Azure 机器学习 SDK 时，开发环境必须能够连接到 Azure 存储帐户。 当存储帐户位于虚拟网络中时，防火墙必须允许从开发环境的 IP 地址进行访问。
    >
    > 若要启用对存储帐户的访问，请从开发客户端上的 Web 浏览器访问存储帐户的“防火墙和虚拟网络”。 然后选中“添加客户端 IP 地址”复选框，将客户端的 IP 地址添加到“地址范围”。  也可以使用“地址范围”字段手动输入开发环境的 IP 地址。 添加客户端的 IP 地址后，该客户端可以使用 SDK 访问存储帐户。

   [![Azure 门户中的“防火墙和虚拟网络”窗格](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="use-datastores-and-datasets"></a>使用数据存储和数据集

本部分介绍了 SDK 体验的数据存储和数据集用法。 有关工作室体验的详细信息，请参阅[机器学习工作室](#machine-learning-studio)部分。

默认情况下，当你尝试使用 SDK 访问数据时，Azure 机器学习会执行数据有效性和凭据检查。 如果数据位于虚拟网络后面，则 Azure 机器学习无法访问数据，因此无法检查数据。 若要避免这种情况，你必须创建跳过验证的数据存储和数据集。

### <a name="use-a-datastore"></a>使用数据存储

 Azure Data Lake Store Gen1 和 Azure Data Lake Store Gen2 默认跳过验证，因此无需进一步操作。 但是，对于以下服务，你可以使用类似的语法来跳过数据存储验证：

- Azure Blob 存储
- Azure 文件共享
- PostgreSQL
- Azure SQL 数据库

下面的代码示例创建一个新的 Azure Blob 数据存储并设置 `skip_validation=True`。

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-a-dataset"></a>使用数据集

对于下列数据集类型，用于跳过数据集验证的语法是类似的：
- 带分隔符的文件
- JSON 
- Parquet
- SQL
- 文件

下面的代码创建一个新的 JSON 数据集并设置 `validate=False`。

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>计算群集和实例 

若要在虚拟网络中使用[托管 Azure 机器学习计算目标](concept-compute-target.md#azure-machine-learning-compute-managed)或 [Azure 机器学习计算实例](concept-compute-instance.md)，必须满足以下网络要求：

> [!div class="checklist"]
> * 虚拟网络必须与 Azure 机器学习工作区位于同一订阅和区域中。
> * 为计算实例或群集指定的子网必须具有足够的未分配 IP 地址，以容纳目标 VM 数目。 如果该子网没有足够的未分配 IP 地址，则只会为计算群集分配一部分资源。
> * 检查对虚拟网络的订阅或资源组实施的安全策略或锁定是否限制了管理虚拟网络所需的权限。 如果你打算通过限制流量来保护虚拟网络，请为计算服务保持打开某些端口。 有关详细信息，请参阅[所需的端口](#mlcports)部分。
> * 若要将多个计算实例或群集放入一个虚拟网络，可能需要请求提高一个或多个资源的配额。
> * 如果工作区的一个或多个 Azure 存储帐户也在虚拟网络中受保护，它们必须与 Azure 机器学习计算实例或群集位于同一虚拟网络中。 
> * 为了让计算实例 Jupyter 功能可以正常运行，请确保没有禁用 Web 套接字通信。

> [!TIP]
> 机器学习计算实例或群集自动在包含虚拟网络的资源组中分配更多网络资源。 对于每个计算实例或群集，此服务分配以下资源：
> 
> * 一个网络安全组
> * 一个公共 IP 地址
> * 一个负载均衡器
> 
> 对于群集，每当群集纵向缩减为 0 个节点时，这些资源都会被删除（并重新创建）；但对于实例，这些资源会一直保留到实例完全删除（停止并不会删除资源）。 
> 这些资源受订阅的[资源配额](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)限制。


### <a name="required-ports"></a><a id="mlcports"></a> 所需端口

如果你计划通过限制进出公共 Internet 的网络流量来保护虚拟网络，则必须允许来自 Azure Batch 服务的入站通信。

Batch 服务在附加到 VM 的网络接口 (NIC) 级别添加网络安全组 (NSG)。 这些 NSG 自动配置允许以下流量的入站和出站规则：

- 端口 29876 和 29877 上的来自 __BatchNodeManagement__ 服务标记的入站 TCP 流量。

    ![使用 BatchNodeManagement 服务标记的入站规则](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- （可选）端口 22 上允许远程访问的入站 TCP 流量。 仅当要在公共 IP 上使用 SSH 进行连接时，才使用此端口。

- 任何端口上通往虚拟网络的出站流量。

- 任何端口上通往 Internet 的出站流量。

- 对于端口 44224 上的来自 __AzureMachineLearning__ 服务标记的计算实例入站 TCP 流量。

> [!IMPORTANT]
> 在 Batch 配置的 NSG 中修改或添加入站或出站规则时，请务必小心。 如果 NSG 阻止与计算节点通信，则计算服务会将计算节点的状态设置为不可用。
>
> 不需要在子网级别指定 NSG，因为 Azure Batch 会配置自身的 NSG。 但是，如果包含 Azure 机器学习计算的子网具有关联的 NSG 或防火墙，则还必须允许前面列出的流量。

下图显示了 Azure 门户中的 NSG 规则配置：

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="用于机器学习计算的入站 NSG 规则" border="true":::



![用于机器学习计算的出站 NSG 规则](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> 限制来自虚拟网络的出站连接

如果你不想要使用默认的出站规则，同时想要限制虚拟网络的出站访问，请执行以下步骤：

- 使用 NSG 规则来拒绝出站 Internet 连接。

- 对于__计算实例__或__计算群集__，请将出站流量限制为以下各项：
   - Azure 存储 - 使用__服务标记__ __Storage.RegionName__。 其中 `{RegionName}` 是 Azure 区域的名称。
   - Azure 容器注册表 - 使用__服务标记__ __AzureContainerRegistry.RegionName__。 其中 `{RegionName}` 是 Azure 区域的名称。
   - Azure 机器学习，通过使用服务标记 AzureMachineLearning
   - Azure 资源管理器，通过使用服务标记 AzureResourceManager
   - Azure Active Directory - 使用__服务标记__ __AzureActiveDirectory__

下图展示了 Azure 门户中的 NSG 规则配置：

[![机器学习计算的出站 NSG 规则](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> 如果你计划使用 Microsoft 提供的默认 Docker 映像并启用用户管理的依赖项，则还必须使用以下服务标记：
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> 当你的训练脚本中有类似于以下代码片段的代码时，需要此配置：
>
> __RunConfig 训练__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Estimator 训练__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>强制隧道

如果要在 Azure 机器学习计算中使用[强制隧道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)，则必须允许从包含计算资源的子网与公共 internet 通信。 此通信用于任务计划和访问 Azure 存储。

可以通过两种方式来实现此目的：

* 使用[虚拟网络 NAT](../virtual-network/nat-overview.md)。 NAT 网关为虚拟网络中的一个或多个子网提供出站 internet 连接。 有关信息，请参阅[设计具有 NAT 网关资源的虚拟网络](../virtual-network/nat-gateway-resource.md)。

* 将[用户定义的路由 (udr) ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)添加到包含计算资源的子网。 为资源所在区域中的 Azure Batch 服务使用的每个 IP 地址建立一个 UDR。 借助这些 UDR，Batch 服务可以与计算节点进行通信，以便进行任务计划编制。 还要添加资源所在的 Azure 机器学习服务 IP 地址，因为这是访问计算实例所必需的。 若要获取 Batch 服务和 Azure 机器学习服务的 IP 地址列表，请使用以下方法之一：

    * 下载 [Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)，并在文件中搜索 `BatchNodeManagement.<region>` 和 `AzureMachineLearning.<region>`（其中 `<region>` 是你的 Azure 区域）。

    * 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 来下载信息。 下面的示例下载 IP 地址信息，并筛选掉美国东部 2 区域的信息：

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```
    
    添加 UDR 时，请为每个相关的 Batch IP 地址前缀定义路由，并将“下一跃点类型”设置为“Internet”。  下图显示了 Azure 门户中此 UDR 的示例：

    ![地址前缀的 UDR 示例](./media/how-to-enable-virtual-network/user-defined-route.png)

    除了定义的任何 Udr 外，还必须通过本地网络设备允许到 Azure 存储的出站流量。 具体而言，此流量的 Url 采用以下格式： `<account>.table.core.windows.net` 、 `<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 。 

    有关详细信息，请参阅[在虚拟网络中创建 Azure Batch 池](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)。


### <a name="create-a-compute-cluster-in-a-virtual-network"></a>在虚拟网络中创建计算群集

若要创建机器学习计算群集，请按照以下步骤操作：

1. 登录 [Azure 机器学习工作室](https://ml.azure.com/)，然后选择你的订阅和工作区。

1. 选择左侧的“计算”。

1. 在中心内选择“训练群集”，然后选择“+”。

1. 在“新建训练群集”对话框中，展开“高级设置”部分。

1. 若要将此计算资源配置为使用虚拟网络，请在“配置虚拟网络”部分中执行以下操作：

    1. 在“资源组”下拉列表中，选择包含虚拟网络的资源组。
    1. 在“虚拟网络”下拉列表中，选择包含子网的虚拟网络。
    1. 在“子网”下拉列表中，选择要使用的子网。

   ![机器学习计算的虚拟网络设置](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

也可以使用 Azure 机器学习 SDK 创建机器学习计算群集。 以下代码在名为 `mynetwork` 的虚拟网络的 `default` 子网中创建新的机器学习计算群集：

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

创建过程完成后，请在试验中使用该群集训练模型。 有关详细信息，请参阅[选择并使用用于训练的计算目标](how-to-set-up-training-targets.md)。

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>访问计算实例笔记本中的数据

如果要在 Azure 计算实例上使用笔记本，则必须确保笔记本在与数据相同的虚拟网络和子网后的计算资源上运行。 

在创建过程中，你必须在“高级设置” > “配置虚拟网络”下将你的计算实例配置为位于同一虚拟网络中。 。 无法将现有计算实例添加到虚拟网络中。

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

若要将虚拟网络中的 Azure Kubernetes 服务 (AKS) 添加到工作区，请按照以下步骤操作：

> [!IMPORTANT]
> 在开始以下过程之前，请先完成[在 Azure Kubernetes 服务 (AKS) 中配置高级网络](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites)操作指南中的先决条件，并计划群集的 IP 地址。
>
> AKS 实例和 Azure 虚拟网络必须位于同一区域。 如果你在虚拟网络中保护工作区使用的一个或多个 Azure 存储帐户，它们必须与 AKS 实例位于同一虚拟网络中。

1. 登录 [Azure 机器学习工作室](https://ml.azure.com/)，然后选择你的订阅和工作区。

1. 选择左侧的“计算”。

1. 在中心内选择“推理群集”，然后选择“+”。

1. 在“新建推理群集”对话框中，选择“网络配置”下的“高级”。

1. 若要将此计算资源配置为使用虚拟网络，请执行以下操作：

    1. 在“资源组”下拉列表中，选择包含虚拟网络的资源组。
    1. 在“虚拟网络”下拉列表中，选择包含子网的虚拟网络。
    1. 在“子网”下拉列表中选择子网。
    1. 在“Kubernetes 服务地址范围”中，输入 Kubernetes 服务地址范围。 此地址范围使用无类域间路由 (CIDR) 表示法表示的 IP 范围来定义群集可用的 IP 地址。 此范围不得与任何子网 IP 范围重叠（例如 10.0.0.0/16）。
    1. 在“Kubernetes DNS 服务 IP 地址”框中，输入 Kubernetes DNS 服务 IP 地址。 此 IP 地址将分配给 Kubernetes DNS 服务。 此 IP 地址必须在 Kubernetes 服务地址范围内（例如 10.0.0.10）。
    1. 在“Docker 网桥地址”框中，输入 Docker 网桥地址。 此 IP 地址将分配给 Docker 网桥。 此 IP 地址不得在任何子网 IP 范围或 Kubernetes 服务地址范围内（例如 172.17.0.1/16）。

   ![Azure 机器学习：机器学习计算虚拟网络设置](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 确保用于控制虚拟网络的 NSG 组包含一条已为评分终结点启用的入站安全规则，以便可以从虚拟网络外部调用此终结点。
   > [!IMPORTANT]
   > 保留 NSG 的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。

   [![入站安全规则](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

也可以使用 Azure 机器学习 SDK 在虚拟网络中添加 Azure Kubernetes 服务。 如果虚拟网络中已有一个 AKS 群集，请根据[如何部署到 AKS](how-to-deploy-and-where.md) 中所述，将此群集附加到工作区。 以下代码在名为 `mynetwork` 的虚拟网络的 `default` 子网中创建新的 AKS 实例：

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

创建过程完成后，可在虚拟网络后面的 AKS 群集上运行推理或模型评分。 有关详细信息，请参阅[如何部署 AKS](how-to-deploy-and-where.md)。

### <a name="use-private-ips-with-azure-kubernetes-service"></a>将专用 IP 与 Azure Kubernetes 服务配合使用

默认情况下，将为 AKS 部署分配公共 IP 地址。 在虚拟网络中使用 AKS 时，可以改用专用 IP 地址。 只能从虚拟网络或已加入的网络内部访问专用 IP 地址。

可以通过将 AKS 配置为使用_内部负载均衡器_来启用专用 IP 地址。 

#### <a name="network-contributor-role"></a>网络参与者角色

> [!IMPORTANT]
> 如果通过提供先前创建的虚拟网络来创建或附加 AKS 群集，则必须向 AKS 群集的服务主体 (SP) 或托管标识授予对包含虚拟网络的资源组的 "_网络参与者_" 角色。 必须在尝试将内部负载均衡器更改为专用 IP 之前完成此操作。
>
> 若要将标识作为网络参与者添加，请执行以下步骤：

1. 若要查找 AKS 的服务主体或托管标识 ID，请使用以下 Azure CLI 命令。 将 `<aks-cluster-name>` 替换为群集的名称。 将替换 `<resource-group-name>` 为_包含 AKS 群集_的资源组的名称：

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    如果此命令返回的值 `msi` ，则使用以下命令来标识托管标识的主体 ID：

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. 若要查找包含虚拟网络的资源组的 ID，请使用以下命令。 将替换 `<resource-group-name>` 为_包含虚拟网络_的资源组的名称：

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. 若要将服务主体或托管标识作为网络参与者添加，请使用以下命令。 替换为 `<SP-or-managed-identity>` 服务主体或托管标识返回的 ID。 `<resource-group-id>`将替换为包含虚拟网络的资源组返回的 ID：

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
若要详细了解如何结合使用内部负载均衡器与 AKS，请参阅[结合使用内部负载均衡器与 Azure Kubernetes 服务](/azure/aks/internal-lb)。

#### <a name="enable-private-ip"></a>启用专用 IP

> [!IMPORTANT]
> 创建 Azure Kubernetes 服务群集时，无法启用专用 IP。 只能在更新现有群集时进行启用。

以下代码片段演示了如何__创建新的 AKS 群集__，然后将其更新为使用专用 IP/内部负载均衡器：

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

该命令引用的 `body.json` 文件的内容类似于以下 JSON 文档：

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

将__现有群集附加__到工作区时，必须等到附加操作完成后，才能配置负载均衡器。

有关附加群集的信息，请参阅[附加现有的 AKS 群集](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)。

附加现有群集后，你可以更新群集以使用专用 IP。

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="use-azure-container-instances-aci"></a>使用 Azure 容器实例 (ACI)

Azure 容器实例在部署模型时动态创建。 你必须为部署使用的子网启用子网委派，Azure 机器学习才能在虚拟网络中创建 ACI。

> [!WARNING]
> 在虚拟网络中使用 Azure 容器实例时，虚拟网络必须与 Azure 机器学习工作区位于同一资源组中。
>
> 在虚拟网络中使用 Azure 容器实例时，工作区的 Azure 容器注册表 (ACR) 也不能在虚拟网络中。

若要将虚拟网络中的 ACI 用于工作区，请按照以下步骤操作：

1. 若要在虚拟网络上启用子网委派，请参阅[添加或删除子网委派](../virtual-network/manage-subnet-delegation.md)一文中的信息。 可以在创建虚拟网络时启用委派，也可以将它添加到现有网络。

    > [!IMPORTANT]
    > 启用委派时，使用 `Microsoft.ContainerInstance/containerGroups` 作为“将子网委派给服务”值。

2. 使用 [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-) 部署模型（使用 `vnet_name` 和 `subnet_name` 参数）。 将这些参数设置为启用了委派的虚拟网络名称和子网。

## <a name="azure-firewall"></a>Azure 防火墙

若要了解如何结合使用 Azure 机器学习和 Azure 防火墙，请参阅[在 Azure 防火墙后面使用 Azure 机器学习工作区](how-to-access-azureml-behind-firewall.md)。

## <a name="azure-container-registry"></a>Azure 容器注册表

> [!IMPORTANT]
> 可以将 Azure 容器注册表 (ACR) 置于虚拟网络中，但必须先满足以下先决条件：
>
> * Azure 机器学习工作区必须是 Enterprise Edition。 若要了解如何升级，请参阅[升级到 Enterprise Edition](how-to-manage-workspace.md#upgrade)。
> * Azure 机器学习工作区区域应为 " [privated 链接已启用" 区域](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)。 
> * Azure 容器注册表必须是高级版。 若要详细了解如何升级，请参阅[更改 SKU](/azure/container-registry/container-registry-skus#changing-skus)。
> * Azure 容器注册表必须与用于训练或推理的存储帐户和计算目标位于同一虚拟网络和子网中。
> * Azure 机器学习工作区必须包含 [Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)。
>
>     如果 ACR 位于虚拟网络后面，Azure 机器学习无法使用它来直接生成 Docker 映像。 而是使用计算群集来生成映像。

1. 若要查找工作区的 Azure 容器注册表的名称，请使用以下方法之一：

    __Azure 门户__

    在工作区的“概述”部分中，“注册表”值链接到 Azure 容器注册表。

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="工作区的 Azure 容器注册表" border="true":::

    __Azure CLI__

    如果[安装了用于 Azure CLI 的机器学习扩展](reference-azure-machine-learning-cli.md)，可以运行 `az ml workspace show` 命令来显示工作区信息。

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    此命令会返回类似于 `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` 的值。 此字符串的最后一部分是工作区的 Azure 容器注册表的名称。

1. 若要限制对虚拟网络的访问，请按照[为注册表配置网络访问](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)中的步骤操作。 添加虚拟网络时，为 Azure 机器学习资源选择虚拟网络和子网。

1. 使用 Azure 机器学习 Python SDK 将计算群集配置为生成 Docker 映像。 下面的代码片段展示了如何执行此操作：

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > 存储帐户、计算群集和 Azure 容器注册表必须都位于虚拟网络的同一子网中。
    
    有关详细信息，请参阅 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) 方法参考。

1. 必须应用以下 Azure 资源管理器模板。 借助此模板，工作区可以与 ACR 进行通信。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>密钥保管库实例 

Azure 机器学习使用与工作区关联的密钥保管库实例来存储以下凭据：
* 关联的存储帐户连接字符串
* Azure 容器存储库实例的密码
* 数据存储的连接字符串

若要在虚拟网络的后面将 Azure 机器学习试验功能与 Azure Key Vault 配合使用，请执行以下步骤：

1. 转到与工作区关联的密钥保管库。

   [![与 Azure 机器学习工作区关联的 Key Vault](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. 在“Key Vault”页上的左侧窗格中，选择“防火墙和虚拟网络”。

   ![“Key Vault”窗格中的“防火墙和虚拟网络”部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. 在“防火墙和虚拟网络”页上执行以下操作：
    - 在“允许的访问来源”下，选择“所选网络”。
    - 在“虚拟网络”下，选择“添加现有的虚拟网络”，以添加试验计算资源所在的虚拟网络。 
    - 在“允许受信任的 Microsoft 服务跳过此防火墙”下选择“是”。 

   [![“Key Vault”窗格中的“防火墙和虚拟网络”部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

若要将虚拟网络中的 Azure Databricks 用于工作区，必须满足以下要求：

> [!div class="checklist"]
> * 该虚拟网络必须与 Azure 机器学习工作区位于同一订阅和区域。
> * 如果工作区的 Azure 存储帐户也在虚拟网络中受保护，则它们必须与 Azure Databricks 群集位于同一虚拟网络中。
> * 除了 Azure Databricks 使用的 __databricks-private__ 和 __databricks-public__ 子网以外，还需要为虚拟网络创建 __default__ 子网。

若要详细了解如何结合使用 Azure Databricks 和虚拟网络，请参阅[在 Azure 虚拟网络中部署 Azure Databricks](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>虚拟机或 HDInsight 群集

> [!IMPORTANT]
> Azure 机器学习只支持运行 Ubuntu 的虚拟机。

若要将虚拟网络中的虚拟机或 Azure HDInsight 群集与工作区配合使用，请执行以下步骤：

1. 使用 Azure 门户或 Azure CLI 创建 VM 或 HDInsight 群集，然后将群集置于 Azure 虚拟网络中。 有关详细信息，请参阅以下文章：
    * [为 Linux VM 创建和管理 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虚拟网络扩展 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 若要允许 Azure 机器学习与 VM 或群集上的 SSH 端口通信，请为网络安全组配置一个源条目。 SSH 端口通常是端口 22。 若要允许来自此源的流量，请执行以下操作：

    * 在“源”下拉列表中，选择“服务标记”。

    * 在“源服务标记”下拉列表中，选择“AzureMachineLearning”。 

    * 在“源端口范围”下拉列表中，选择 __*__ 。

    * 在“目标”下拉列表中，选择“任何”。 

    * 在“目标端口范围”下拉列表中，选择“22”。 

    * 在“协议”下，选择“任何”。 

    * 在“操作”下，选择“允许”。 

   ![用于在虚拟网络中的 VM 或 HDInsight 群集上执行试验的入站规则](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    保留网络安全组的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。

    如果你不想要使用默认出站规则，同时想要限制虚拟网络的出站访问，请参阅[限制来自虚拟网络的出站连接](#limiting-outbound-from-vnet)部分。

1. 将 VM 或 HDInsight 群集附加到 Azure 机器学习工作区。 有关详细信息，请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md)。


## <a name="next-steps"></a>后续步骤

* [设置训练环境](how-to-set-up-training-targets.md)
* [设置专用终结点](how-to-configure-private-link.md)
* [模型部署位置](how-to-deploy-and-where.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
