---
title: 网络隔离 & 隐私
titleSuffix: Azure Machine Learning
description: 将独立的 Azure 虚拟网络与 Azure 机器学习配合使用来保护试验/培训以及推理/评分作业。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 05/11/2020
ms.custom: contperfq4
ms.openlocfilehash: 5099cc2ce2228bcdbf49d3484e488e7373883ec0
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119042"
---
# <a name="secure-your-machine-learning-lifecycles-with-private-virtual-networks"></a>通过专用虚拟网络保护机器学习生命周期
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，你将了解如何在 Azure 虚拟网络（vnet）中 Azure 机器学习隔离试验/培训作业和推理/评分作业。 你还将了解一些*高级安全设置*，即基本或实验用例所不需要的信息。

> [!WARNING]
> 如果基础存储位于虚拟网络中，则用户将无法使用 Azure 机器学习的 studio web 体验，包括：
> - 拖放设计器
> - 自动机器学习的 UI
> - 用于数据标记的 UI
> - 数据集的 UI
> - 笔记本
> 
> 如果尝试，将收到类似于以下错误的消息：`__Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.__`

## <a name="what-is-a-vnet"></a>什么是 VNET？

**虚拟网络**充当安全边界，将 Azure 资源与公共 internet 隔离开来。 你也可以将 Azure 虚拟网络加入本地网络。 加入网络后，可以安全训练模型，以及访问用于推理的已部署模型。

Azure 机器学习依赖于其他 Azure 服务（也称为[计算目标](concept-compute-target.md)）来定型和部署模型。 可以在虚拟网络中创建目标。 例如，可以使用 Azure 机器学习计算来训练模型，然后将模型部署到 Azure Kubernetes 服务（AKS）。 


## <a name="prerequisites"></a>必备条件

+ Azure 机器学习[工作区](how-to-manage-workspace.md)。

+ 对 [Azure 虚拟网络服务](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)和 [IP 网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)具备一般性的实践知识。

+ 预先存在一个要与计算资源配合使用的虚拟网络和子网。

## <a name="private-endpoints"></a>专用终结点

还可以使用专用终结点[启用 Azure 专用链接](how-to-configure-private-link.md)，以连接到工作区。 专用终结点是虚拟网络中的一组专用 IP 地址。 [了解如何设置此专用终结点。](how-to-configure-private-link.md)



> [!TIP]
> 你可以将虚拟网络和专用链接组合在一起，以保护你的工作区和其他 Azure 资源之间的通信。 但是，某些组合需要 Enterprise edition 工作区。 使用下表了解需要 Enterprise edition 的方案：
>
> | 方案 | Enterprise</br>edition | 基本</br>edition |
> | ----- |:-----:|:-----:| 
> | 无虚拟网络或专用链接 | ✔ | ✔ |
> | 无专用链接的工作区。 虚拟网络中的其他资源（Azure 容器注册表除外） | ✔ | ✔ |
> | 无专用链接的工作区。 具有专用链接的其他资源 | ✔ | |
> | 具有专用链接的工作区。 虚拟网络中的其他资源（Azure 容器注册表除外） | ✔ | ✔ |
> | 工作区和具有专用链接的任何其他资源 | ✔ | |
> | 具有专用链接的工作区。 没有专用链接或虚拟网络的其他资源 | ✔ | ✔ |
> | 虚拟网络中的 Azure 容器注册表 | ✔ | |
> | 工作区的客户托管密钥 | ✔ | |
> 

> [!WARNING]
> 在启用了 "专用" 链接的工作区中不支持 Azure 机器学习计算实例预览。
> 
> Azure 机器学习不支持使用启用了专用链接的 Azure Kubernetes 服务。 相反，你可以在虚拟网络中使用 Azure Kubernetes 服务。 有关详细信息，请参阅[在 Azure 虚拟网络中保护 AZURE ML 试验和推理作业](how-to-enable-virtual-network.md)。


<a id="amlcompute"></a>

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>计算群集 & 实例

若要在虚拟网络中使用[托管 Azure 机器学习**计算目标**](concept-compute-target.md#azure-machine-learning-compute-managed)或[Azure 机器学习计算**实例**](concept-compute-instance.md) ，必须满足以下网络要求：

> [!div class="checklist"]
> * 该虚拟网络必须与 Azure 机器学习工作区位于同一订阅和区域。
> * 为计算实例或群集指定的子网必须具有足够的未分配 IP 地址，以容纳目标 VM 数目。 如果该子网没有足够的未分配 IP 地址，则只会为计算群集分配一部分资源。
> * 检查对虚拟网络的订阅或资源组实施的安全策略或锁定是否限制了管理虚拟网络所需的权限。 如果你打算通过限制流量来保护虚拟网络，请为计算服务保持打开某些端口。 有关详细信息，请参阅[所需的端口](#mlcports)部分。
> * 若要将多个计算实例或群集放入一个虚拟网络，可能需要请求提高一个或多个资源的配额。
> * 如果工作区的 Azure 存储帐户也在虚拟网络中受保护，则它们必须与 Azure 机器学习计算实例或群集位于同一虚拟网络中。 

> [!TIP]
> 机器学习的计算实例或群集会自动分配__包含虚拟网络的资源组中__的其他网络资源。 对于每个计算实例或群集，该服务将分配以下资源：
> 
> * 一个网络安全组
> * 一个公共 IP 地址
> * 一个负载均衡器
> 
> 在群集的情况下，每次群集缩小到0个节点时，都将删除（并重新创建）这些资源，但对于实例被完全删除（停止并不删除资源），资源会保存到该实例。 
> 这些资源受订阅的[资源配额](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)限制。


### <a name="required-ports"></a><a id="mlcports"></a>必需的端口

机器学习计算目前使用 Azure Batch 服务在指定的虚拟网络中预配 VM。 子网必须允许来自 Batch 服务的入站通信。 可以使用这种通信在机器学习计算节点上计划运行，以及与 Azure 存储和其他资源进行通信。 Batch 服务在附加到 VM 的网络接口 (NIC) 级别添加网络安全组 (NSG)。 这些 NSG 自动配置允许以下流量的入站和出站规则：

- 端口 29876 和 29877 上的来自 __BatchNodeManagement__ 服务标记的入站 TCP 流量。____

    ![使用 BatchNodeManagement 服务标记的入站规则](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- （可选）端口 22 上允许远程访问的入站 TCP 流量。 仅当要在公共 IP 上使用 SSH 进行连接时，才使用此端口。

- 任何端口上通往虚拟网络的出站流量。

- 任何端口上通往 Internet 的出站流量。

- 对于端口 44224 上的来自 __AzureMachineLearning__ 服务标记的计算实例入站 TCP 流量。____

在 Batch 配置的 NSG 中修改或添加入站或出站规则时，请务必小心。 如果 NSG 阻止与计算节点通信，则计算服务会将计算节点的状态设置为不可用。

不需要在子网级别指定 NSG，因为 Azure Batch 会配置自身的 NSG。 但是，如果指定的子网具有关联的 NSG 或防火墙，请如前所述配置入站和出站安全规则。

下图显示了 Azure 门户中的 NSG 规则配置：

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="机器学习计算的入站 NSG 规则" border="true":::



![机器学习计算的出站 NSG 规则](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> 限制来自虚拟网络的出站连接

如果你不想使用默认的出站规则，并且想要限制虚拟网络的出站访问权限，请执行以下步骤：

- 使用 NSG 规则拒绝出站 Internet 连接。

- 对于__计算实例__或__计算群集__，请将出站流量限制为以下各项：
   - Azure 存储，使用__RegionName__的__服务标记__。 其中 `{RegionName}` 是 Azure 区域的名称。
   - Azure 容器注册表，使用__AzureContainerRegistry. RegionName__的__服务标记__。 其中 `{RegionName}` 是 Azure 区域的名称。
   - Azure 机器学习 - 使用 __AzureMachineLearning__ 的服务标记____
   - Azure 资源管理器，使用__AzureResourceManager__ __服务标记__
   - Azure Active Directory，使用__AzureActiveDirectory__的__服务标记__

下图显示了 Azure 门户中的 NSG 规则配置：

[![机器学习计算的出站 NSG 规则](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> 如果你计划使用 Microsoft 提供的默认 Docker 映像并启用用户托管依赖项，则还必须使用__Region_Name MicrosoftContainerRegistry__的__服务标记__（例如，MicrosoftContainerRegistry. EastUS）。
>
> 当你的代码与以下代码片段类似于定型脚本的一部分时，需要此配置：
>
> __.Runconfig 培训__
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
> __估计器培训__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>用户定义的用于强制隧道的路由

若要在机器学习计算中使用强制隧道，请将[用户定义的路由 (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) 添加到包含计算资源的子网。

* 为资源所在区域中的 Azure Batch 服务使用的每个 IP 地址建立一个 UDR。 这些 UDR 使 Batch 服务能够与计算节点通信，以计划任务。 另外，请添加资源存在的 Azure 机器学习服务的 IP 地址，因为访问计算实例时需要此地址。 若要获取 Batch 服务和 Azure 机器学习服务的 IP 地址列表，请使用以下方法之一：

    * 下载[AZURE IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)，并在文件中搜索 `BatchNodeManagement.<region>` 和 `AzureMachineLearning.<region>` ，其中 `<region>` 是你的 Azure 区域。

    * 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 下载信息。 以下示例下载 IP 地址信息，并筛选出“美国东部 2”区域的信息：

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

* 发往 Azure 存储的出站流量不能被本地网络设备阻止。 具体而言，URL 采用 `<account>.table.core.windows.net`、`<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 格式。

添加 UDR 时，请为每个相关的 Batch IP 地址前缀定义路由，并将“下一跃点类型”设置为“Internet”。________ 下图显示了 Azure 门户中此 UDR 的示例：

![地址前缀的 UDR 示例](./media/how-to-enable-virtual-network/user-defined-route.png)

有关详细信息，请参阅[在虚拟网络中创建 Azure Batch 池](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)。

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>在虚拟网络中创建计算群集

若要创建机器学习计算群集，请使用以下步骤：

1. 登录到[Azure 机器学习 studio](https://ml.azure.com/)，然后选择你的订阅和工作区。

1. 选择左侧的“计算”  。

1. 从中心选择 "__训练群集__"，然后选择 __+__ 。

1. 在 "__新建定型群集__" 对话框中，展开 "__高级设置__" 部分。

1. 若要将此计算资源配置为使用虚拟网络，请在 "__配置虚拟网络__" 部分中执行以下操作：

    1. 在“资源组”下拉列表中，选择包含虚拟网络的资源组。____
    1. 在“虚拟网络”下拉列表中，选择包含子网的虚拟网络。____
    1. 在“子网”下拉列表中，选择要使用的子网。____

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

## <a name="use-a-storage-account-for-your-workspace"></a>对工作区使用存储帐户

若要在虚拟网络中使用工作区的 Azure 存储帐户，请使用以下步骤：

1. 在虚拟网络的后面创建一个计算资源（例如机器学习计算实例或群集），或者将某个计算资源（例如 HDInsight 群集、虚拟机或 Azure Kubernetes 服务群集）附加到工作区。 该计算资源可用于试验或模型部署。

   有关详细信息，请参阅本文的[使用机器学习计算](#amlcompute)、[使用虚拟机或 HDInsight 群集](#vmorhdi)和[使用 Azure Kubernetes 服务](#aksvnet)部分。

1. 在 Azure 门户中，转到已附加到工作区的存储。

   [![附加到 Azure 机器学习工作区的存储](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. 在“Azure 存储”页上，选择“防火墙和虚拟网络”。****____

   ![Azure 门户中“Azure 存储”页上的“防火墙和虚拟网络”区域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 在 "__防火墙和虚拟网络__" 页上，执行以下操作：
    - 选择“所选网络”。____
    - 在“虚拟网络”下，选择“添加现有的虚拟网络”链接。________ 此操作将添加计算资源所在的虚拟网络（参阅步骤 1）。

        > [!IMPORTANT]
        > 存储帐户必须与用于定型或推理的计算实例或群集位于同一个虚拟网络和子网中。

    - 选中“允许受信任的 Microsoft 服务访问此存储帐户”复选框。____

    > [!IMPORTANT]
    > 使用 Azure 机器学习 SDK 时，开发环境必须能够连接到 Azure 存储帐户。 当存储帐户位于虚拟网络中时，防火墙必须允许从开发环境的 IP 地址进行访问。
    >
    > 若要启用对存储帐户的访问，请从开发客户端上的 Web 浏览器访问存储帐户的“防火墙和虚拟网络”。____** 然后选中“添加客户端 IP 地址”复选框，将客户端的 IP 地址添加到“地址范围”。________ 也可以使用“地址范围”字段手动输入开发环境的 IP 地址。____ 添加客户端的 IP 地址后，该客户端可以使用 SDK 访问存储帐户。

   [![Azure 门户中的 "防火墙和虚拟网络" 窗格](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> 可将 Azure 机器学习的默认存储帐户或者将非默认存储帐户放在虚拟网络中。____
>
> 创建工作区时，会自动预配默认存储帐户。
>
> 对于非默认存储帐户，此 `storage_account` [ `Workspace.create()` 函数](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)中的参数允许你按 Azure 资源 ID 指定自定义存储帐户。


<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

若要将虚拟网络中的 Azure Kubernetes 服务（AKS）添加到工作区，请执行以下步骤：

> [!IMPORTANT]
> 在开始执行以下过程之前，请满足[在 Azure Kubernetes 服务 (AKS) 中配置高级网络](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites)操作指南中的先决条件，并规划好群集的 IP 地址。
>
> AKS 实例和 Azure 虚拟网络必须位于同一区域。 如果在虚拟网络中保护工作区使用的 Azure 存储帐户，这些帐户必须与 AKS 实例位于同一虚拟网络中。

> [!WARNING]
> Azure 机器学习不支持使用启用了专用链接的 Azure Kubernetes 服务。

1. 登录到[Azure 机器学习 studio](https://ml.azure.com/)，然后选择你的订阅和工作区。

1. 选择左侧的“计算”  。

1. 从中心选择 "__推理群集__"，然后选择 __+__ 。

1. 在 "__新建推理群集__" 对话框中，选择 "__网络配置__" 下的 "__高级__"。

1. 若要将此计算资源配置为使用虚拟网络，请执行以下操作：

    1. 在“资源组”下拉列表中，选择包含虚拟网络的资源组。____
    1. 在“虚拟网络”下拉列表中，选择包含子网的虚拟网络。____
    1. 在“子网”下拉列表中选择子网。____
    1. 在“Kubernetes 服务地址范围”中，输入 Kubernetes 服务地址范围。____ 此地址范围使用无类域间路由 (CIDR) 表示法表示的 IP 范围来定义群集可用的 IP 地址。 此范围不得与任何子网 IP 范围重叠（例如 10.0.0.0/16）。
    1. 在“Kubernetes DNS 服务 IP 地址”框中，输入 Kubernetes DNS 服务 IP 地址。____ 此 IP 地址将分配给 Kubernetes DNS 服务。 此 IP 地址必须在 Kubernetes 服务地址范围内（例如 10.0.0.10）。
    1. 在“Docker 网桥地址”框中，输入 Docker 网桥地址。____ 此 IP 地址将分配给 Docker 网桥。 此 IP 地址不得在任何子网 IP 范围或 Kubernetes 服务地址范围内（例如 172.17.0.1/16）。

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

### <a name="use-private-ips-with-azure-kubernetes-service"></a>将专用 Ip 用于 Azure Kubernetes 服务

默认情况下，会将公共 IP 地址分配给 AKS 部署。 在虚拟网络中使用 AKS 时，可以改为使用专用 IP 地址。 只能从虚拟网络或加入网络内部访问专用 IP 地址。

通过将 AKS 配置为使用_内部负载均衡器_来启用专用 IP 地址。 

> [!IMPORTANT]
> 创建 Azure Kubernetes Service 群集时，无法启用专用 IP。 必须将其启用为对现有群集的更新。

以下代码段演示了如何**创建新的 AKS 群集**，然后将其更新为使用专用 IP/内部负载均衡器：

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
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

`body.json`命令引用的文件的内容类似于以下 JSON 文档：

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
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

> [!NOTE]
> 目前，在现有群集上执行__附加__操作时，不能配置负载均衡器。 必须首先附加群集，然后执行更新操作来更改负载均衡器。

有关在 AKS 中使用内部负载均衡器的详细信息，请参阅[将内部负载均衡器用于 Azure Kubernetes 服务](/azure/aks/internal-lb)。

## <a name="use-azure-container-instances-aci"></a>使用 Azure 容器实例（ACI）

部署模型时，将动态创建 Azure 容器实例。 若要启用 Azure 机器学习在虚拟网络中创建 ACI，必须为部署所使用的子网启用__子网委派__。

若要在虚拟网络中使用 ACI 到你的工作区，请执行以下步骤：

1. 若要在虚拟网络上启用子网委托，请使用 "[添加或删除子网委派" 一](../virtual-network/manage-subnet-delegation.md)文中的信息。 可以在创建虚拟网络时启用委派，或将其添加到现有网络。

    > [!IMPORTANT]
    > 启用委派时，请使用 `Microsoft.ContainerInstance/containerGroups` 作为 "__委托子网到服务__" 值。

2. 使用[Deploy_configuration AciWebservice （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)部署模型，使用 `vnet_name` 和 `subnet_name` 参数。 将这些参数设置为启用了委派的虚拟网络名称和子网。

## <a name="azure-firewall"></a>Azure 防火墙

有关将 Azure 机器学习与 Azure 防火墙一起使用的信息，请参阅在[Azure 防火墙后面使用 Azure 机器学习工作区](how-to-access-azureml-behind-firewall.md)。

## <a name="azure-container-registry"></a>Azure 容器注册表

> [!IMPORTANT]
> Azure 容器注册表（ACR）可以放在虚拟网络中，但必须满足以下先决条件：
>
> * Azure 机器学习工作区必须是企业版。 有关升级的信息，请参阅[升级到企业版](how-to-manage-workspace.md#upgrade)。
> * Azure 容器注册表必须是高级版。 有关升级的详细信息，请参阅[更改 sku](/azure/container-registry/container-registry-skus#changing-skus)。
> * Azure 容器注册表必须位于与用于定型或推理的存储帐户和计算目标相同的虚拟网络和子网中。
> * Azure 机器学习工作区必须包含[Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)。
>
>     当 ACR 位于虚拟网络后面时 Azure 机器学习无法使用它来直接生成 Docker 映像。 而是使用计算群集来构建映像。

1. 若要查找工作区的 Azure 容器注册表的名称，请使用以下方法之一：

    __Azure 门户__

    从工作区的 "概述" 部分，__注册表__值链接到 Azure 容器注册表。

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="工作区的 Azure 容器注册表" border="true":::

    __Azure CLI__

    如果已[为 Azure CLI 安装机器学习扩展](reference-azure-machine-learning-cli.md)，则可以使用 `az ml workspace show` 命令来显示工作区信息。

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    此命令会返回类似于 `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` 的值。 字符串的最后一部分是工作区的 Azure 容器注册表的名称。

1. 若要限制对虚拟网络的访问，请使用[为注册表配置网络访问](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)中的步骤。 添加虚拟网络时，为 Azure 机器学习资源选择虚拟网络和子网。

1. 使用 Azure 机器学习 Python SDK 配置计算群集以生成 docker 映像。 下面的代码段演示了如何执行此操作：

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > 存储帐户、计算群集和 Azure 容器注册表必须都位于虚拟网络的同一子网中。
    
    有关详细信息，请参阅[update （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)方法参考。

1. 必须应用以下 Azure 资源管理器模板。 此模板允许你的工作区与 ACR 通信。

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
    
## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage 第2代是一组功能，用于在 Azure Blob 存储基础上构建大数据分析。 它可用于存储用于为模型定型 Azure 机器学习的数据。 

若要在 Azure 机器学习工作区的虚拟网络中使用 Data Lake Storage 第2代，请使用以下步骤：

1. 创建 Azure Data Lake Storage 第2代帐户。 有关详细信息，请参阅[创建 Azure Data Lake Storage Gen2 的存储帐户](../storage/blobs/data-lake-storage-quickstart-create-account.md)。

1. 使用上一节中的步骤2-4，为[你的工作区使用存储帐户](#use-a-storage-account-for-your-workspace)，将该帐户放在虚拟网络中。

在虚拟网络中将 Azure 机器学习与 Data Lake Storage Gen 2 一起使用时，请使用以下指南：

* 如果使用__SDK 创建数据集__，并且运行代码的系统__不在虚拟网络中__，请使用 `validate=False` 参数。 此参数跳过验证，如果系统与存储帐户不在同一虚拟网络中，则会失败。 有关详细信息，请参阅[from_files （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)方法。

* 使用 Azure 机器学习计算实例或计算群集来使用数据集来训练模型时，该数据集必须与存储帐户位于同一虚拟网络中。

## <a name="key-vault-instance"></a>Key vault 实例 

Azure 机器学习使用与工作区关联的 Key Vault 实例来存储以下凭据：
* 关联的存储帐户连接字符串
* Azure 容器存储库实例的密码
* 数据存储的连接字符串

若要在虚拟网络后使用 Azure Key Vault Azure 机器学习试验功能，请执行以下步骤：

1. 转到与工作区关联的 Key Vault。

   [![与 Azure 机器学习工作区关联的密钥保管库](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. 在“Key Vault”页上的左侧窗格中，选择“防火墙和虚拟网络”。****____

   ![“Key Vault”窗格中的“防火墙和虚拟网络”部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. 在 "__防火墙和虚拟网络__" 页上，执行以下操作：
    - 在“允许的访问来源”  下，选择“所选网络”  。
    - 在“虚拟网络”下，选择“添加现有的虚拟网络”，以添加试验计算资源所在的虚拟网络。________
    - 在“允许受信任的 Microsoft 服务跳过此防火墙”下选择“是”。________

   [!["Key Vault" 窗格中的 "防火墙和虚拟网络" 部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

若要将虚拟网络中的 Azure Databricks 与工作区配合使用，必须满足以下要求：

> [!div class="checklist"]
> * 该虚拟网络必须与 Azure 机器学习工作区位于同一订阅和区域。
> * 如果工作区的 Azure 存储帐户也在虚拟网络中受保护，则它们必须与 Azure Databricks 群集位于同一虚拟网络中。
> * 除了 Azure Databricks 使用的 __databricks-private__ 和 __databricks-public__ 子网以外，还需要为虚拟网络创建 __default__ 子网。

有关在虚拟网络中使用 Azure Databricks 的具体信息，请参阅[在 Azure 虚拟网络中部署 Azure Databricks](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>虚拟机或 HDInsight 群集

> [!IMPORTANT]
> Azure 机器学习仅支持运行 Ubuntu 的虚拟机。

若要在工作区中使用虚拟网络中的虚拟机或 Azure HDInsight 群集，请执行以下步骤：

1. 使用 Azure 门户或 Azure CLI 创建 VM 或 HDInsight 群集，并将群集放入 Azure 虚拟网络。 有关详细信息，请参阅下列文章：
    * [创建和管理适用于 Linux Vm 的 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虚拟网络扩展 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 若要允许 Azure 机器学习与 VM 或群集上的 SSH 端口通信，请为网络安全组配置一个源条目。 SSH 端口通常是端口 22。 若要允许来自此源的流量，请执行以下操作：

    * 在“源”下拉列表中，选择“服务标记”。________

    * 在“源服务标记”下拉列表中，选择“AzureMachineLearning”。________

    * 在“源端口范围”下拉列表中，选择 __*__。____

    * 在“目标”下拉列表中，选择“任何”。________

    * 在“目标端口范围”下拉列表中，选择“22”。________

    * 在“协议”下，选择“任何”。________

    * 在“操作”下，选择“允许”。________

   ![用于在虚拟网络中的 VM 或 HDInsight 群集上执行试验的入站规则](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    保留网络安全组的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。

    如果你不想要使用默认出站规则，同时想要限制虚拟网络的出站访问，请参阅[限制来自虚拟网络的出站连接](#limiting-outbound-from-vnet)部分。

1. 将 VM 或 HDInsight 群集附加到 Azure 机器学习工作区。 有关详细信息，请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md)。


## <a name="next-steps"></a>后续步骤

* [设置训练环境](how-to-set-up-training-targets.md)
* [设置专用终结点](how-to-configure-private-link.md)
* [模型部署位置](how-to-deploy-and-where.md)
* [使用 TLS 通过 Azure 机器学习来保护 web 服务](how-to-secure-web-service.md)
