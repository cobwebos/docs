---
title: 在虚拟网络中运行试验和推理
titleSuffix: Azure Machine Learning service
description: 在 Azure 虚拟网络中安全运行机器学习试验和推理。 了解如何为模型训练创建计算目标以及如何在虚拟网络中进行推理。 了解受保护虚拟网络的要求，例如，需要打开的入站和出站端口。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: 9bd56984f088ab16fc5d80c588afce2cdc31240b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848116"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>安全地在 Azure 虚拟网络中运行试验和推理

本文介绍如何在虚拟网络中运行试验和推理。 虚拟网络充当安全边界，可将 Azure 资源与公共 Internet 相隔离。 你也可以将 Azure 虚拟网络加入本地网络。 使用该功能, 您可以安全地训练模型并访问已部署的模型进行推理。 推理或模型计分是部署模型用于预测的阶段, 最常见的是生产数据。

Azure 机器学习服务依赖于其他 Azure 服务提供计算资源。 计算资源（计算目标）用于训练和部署模型。 可在虚拟网络中创建这些计算目标。 例如，可以使用 Microsoft Data Science Virtual Machine 来训练模型，然后将该模型部署到 Azure Kubernetes 服务 (AKS)。 有关虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

本文提供了有关**高级安全设置**的详细信息, 对于基本或实验性用例, 不是必需的。 本文中的各节提供了不同方案的配置信息, 但不需要按顺序或整体完成。

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 机器学习服务[工作区](how-to-manage-workspace.md), 请创建一个。 本文档假定你熟悉 Azure 虚拟网络和一般的 IP 网络。 本文档假定你已创建要用于计算资源的虚拟网络和子网。 如果你不熟悉 Azure 虚拟网络, 请阅读以下文章了解该服务:

* [IP 寻址](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [快速入门：创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [筛选网络流量](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>工作区的存储帐户

若要在虚拟网络中使用工作区的 Azure 存储帐户, 请使用以下步骤:

1. 创建试验计算, 例如 在虚拟网络后机器学习计算, 或将试验计算附加到工作区 (例如) HDInsight 群集或虚拟机。 有关详细信息, 请参阅[使用机器学习计算](#use-machine-learning-compute)和使用本文档中[的虚拟机或 HDInsight 群集](#use-a-virtual-machine-or-hdinsight-cluster)部分
2. 前往附加到工作区的存储。 ![显示附加到 Azure 机器学习服务工作区的 Azure 存储的 Azure 门户图像](./media/how-to-enable-virtual-network/workspace-storage.png)
3. 在 "Azure 存储空间" 页上, 选择 "__防火墙和虚拟网络__"。 ![显示 "Azure 存储" 页上的 "防火墙和虚拟网络" 部分的 Azure 门户的图像](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. 在 "__防火墙和虚拟网络__" 页上, 选择以下条目:
    - 选择“所选网络”。
    - 在 "__虚拟网络__" 下, 选择 "__添加现有虚拟网络__" 以添加试验计算所在的虚拟网络。 (请参阅步骤1。)
    - 选择 "__允许受信任的 Microsoft 服务访问此存储帐户__"。
![显示 Azure 存储下的 "防火墙" 和 "虚拟网络" 页面的 Azure 门户图像](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

5. 运行试验时, 请在试验代码中更改运行配置以使用 blob 存储:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> __只有执行试验时__, Azure 机器学习服务的__默认存储帐户__才能放置在虚拟网络中。
>
> 还可以将__非默认存储帐户__放置在虚拟网络中, 但__仅用于试验__。
>
> 用于__推理__的默认或非默认存储帐户必须对__存储帐户具有不受限制的访问权限__。
>
> 如果不确定是否已修改这些设置，请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)中的“更改默认网络访问规则”。 使用这些步骤可以在推断过程中或在模型评分时允许来自所有网络的访问。

## <a name="key-vault-for-your-workspace"></a>工作区的密钥保管库

Azure 机器学习服务使用与工作区关联的 Key Vault 实例来存储各种类型的凭据:
* 关联的存储帐户连接字符串
* 密码到 Azure 容器存储库实例
* 数据存储的连接字符串。

若要在虚拟网络后使用 Key Vault Azure 机器学习试验功能, 请遵循以下步骤:
1. 中转到与工作区关联的 Key Vault。 ![显示与 Azure 机器学习服务工作区关联的 Key Vault 的 Azure 门户图像](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. 在 "Key Vault" 页上, 选择 "__防火墙和虚拟网络__" 部分。 ![Key Vault 页上显示 "防火墙和虚拟网络" 部分的 Azure 门户的图像](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. 在 "__防火墙和虚拟网络__" 页上, 选择以下条目:
    - 选择“所选网络”。
    - 在 "__虚拟网络__" 下, 选择 "__添加现有虚拟网络__" 以添加试验计算所在的虚拟网络。
    - 选择 "__允许受信任的 Microsoft 服务跳过此防火墙__"。
![Key Vault 下显示 "防火墙和虚拟网络" 页的 Azure 门户的图像](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)


## <a name="use-machine-learning-compute"></a>使用机器学习计算

若要在虚拟网络中使用 Azure 机器学习计算, 请考虑以下有关网络要求的信息:

- 该虚拟网络必须与 Azure 机器学习服务工作区位于同一订阅和区域。

- 为计算群集指定的子网必须具有足够的未分配 IP 地址, 才能容纳群集目标 Vm 的数量。 如果该子网没有足够的未分配 IP 地址，则只分配该群集的一部分资源。

- 如果打算通过限制流量来保护虚拟网络, 请为计算服务留出一些打开的端口。 有关详细信息，请参阅[所需的端口](#mlcports)。

- 检查对虚拟网络的订阅或资源组实施的安全策略或锁定是否限制了管理虚拟网络所需的权限。

- 如果要在一个虚拟网络中放置多个计算群集, 可能需要为一个或多个资源请求一个配额增加。

    Azure 机器学习计算会自动分配包含虚拟网络的资源组中的其他网络资源。 对于每个计算群集, 服务分配以下资源:

    - 一个网络安全组

    - 一个公共 IP 地址

    - 一个负载均衡器

  这些资源受订阅的[资源配额](https://docs.microsoft.com/azure/azure-subscription-service-limits)限制。

### <a id="mlcports"></a>所需的端口

机器学习计算目前使用 Azure Batch 服务在指定的虚拟网络中预配 VM。 子网必须允许来自 Batch 服务的入站通信。 这种通信用于在机器学习计算节点上计划运行，以及与 Azure 存储和其他资源进行通信。 Batch 在附加到 Vm 的网络接口 (**NIC**) 级别添加网络安全组 (**NSG**)。 这些 NSG 自动配置允许以下流量的入站和出站规则：

- 来自__BatchNodeManagement__的__服务标记__的端口29876和29877上的入站 TCP 流量。

    ![显示使用 BatchNodeManagement 服务标记的入站规则的 Azure 门户图像](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- 可有可无端口22上的入站 TCP 流量, 允许远程访问。 仅当要在公共 IP 上使用 SSH 进行连接时, 才需要此端口。

- 任何端口上通往虚拟网络的出站流量。

- 任何端口上通往 Internet 的出站流量。

在 Batch 配置的 NSG 中修改或添加入站/出站规则时，请务必小心。 如果 NSG 阻止与计算节点通信, 则计算服务会将计算节点的状态设置为 "不可用"。

不需要在子网级别指定 Nsg, 因为 Azure Batch 服务配置其自己的 Nsg。 但是，如果指定的子网具有关联的 NSG 和/或防火墙，请如前所述配置入站和出站安全规则。

以下屏幕截图显示了 NSG 规则配置在 Azure 门户中的显示方式:

![机器学习计算的入站 NSG 规则屏幕截图](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![机器学习计算的出站 NSG 规则屏幕截图](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>限制来自虚拟网络的出站连接

如果你不想使用默认的出站规则并想要限制虚拟网络的出站访问权限, 请使用以下步骤:

- 使用 NSG 规则拒绝出站 internet 连接

- 限制到 Azure 存储的出站流量 (使用存储的__服务标记__) __。 Region_Name__ ex EastUS)、Azure 容器注册表 (使用__AzureContainerRegistry. Region_Name__的__服务标记__ AzureContainerRegistry. EastUS) 和 Azure 机器学习 service (使用__AzureMachineLearning__的__服务标记__)

以下屏幕截图显示了 NSG 规则配置在 Azure 门户中的显示方式:

![机器学习计算的出站 NSG 规则屏幕截图](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>用户定义的用于强制隧道的路由

如果使用强制隧道与 Azure 机器学习计算, 则必须将[用户定义的路由 (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)添加到包含计算资源的子网。

* 必须为资源所在区域中 Azure Batch 服务使用的每个 IP 地址建立用户定义的路由。 这些 Udr 允许批处理服务与计算节点通信, 以便进行任务计划。 若要获取 Batch 服务的 IP 地址列表, 请联系 Azure 支持部门。

* 到 Azure 存储的出站流量 (具体而言, 本地`<account>.table.core.windows.net`网络`<account>.queue.core.windows.net`设备不得`<account>.blob.core.windows.net`阻止格式为、和的 url)。

添加用户定义的路由时, 请为每个相关的批 IP 地址前缀定义路由, 并将 "__下一跃点类型__" 设置为 " __Internet__"。 下图显示了 Azure 门户中的此 UDR 的示例:

![地址前缀的用户定义路由示例](./media/how-to-enable-virtual-network/user-defined-route.png)

有关详细信息, 请参阅在[虚拟网络中创建 Azure Batch 池一](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)文。

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>在虚拟网络中创建机器学习计算

若要使用 Azure 门户创建 Azure 机器学习计算群集, 请执行以下步骤:

1. 在 [Azure 门户](https://portal.azure.com)中，选择你的 Azure 机器学习服务工作区。

1. 在“应用程序”部分，选择“计算”。 然后选择“添加计算”。

1. 若要将此计算资源配置为使用虚拟网络，请使用以下选项：

    - __网络配置__：选择“高级”。

    - __资源组__：选择包含该虚拟网络的资源组。

    - __虚拟网络__：选择包含子网的虚拟网络。

    - __子网__：选择要使用的子网。

   ![显示机器学习计算的虚拟网络设置的屏幕截图](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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

    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

创建过程完成后, 在试验中使用群集来训练模型。 有关详细信息，请参阅[选择并使用用于训练的计算目标](how-to-set-up-training-targets.md)。

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>使用虚拟机或 HDInsight 群集

若要在工作区中使用虚拟网络中的虚拟机或 Azure HDInsight 群集, 请执行以下步骤:

1. 使用 Azure 门户或 Azure CLI 创建 VM 或 HDInsight 群集，并将其放入 Azure 虚拟网络。 有关详细信息，请参阅以下文档：
    * [为 Linux VM 创建和管理 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虚拟网络扩展 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 若要允许 Azure 机器学习服务与 VM 或群集上的 SSH 端口通信, 你必须为网络安全组配置源条目。 SSH 端口通常是端口 22。 使用以下步骤可允许来自此源的流量:

    * __源__：选择“服务标记”。

    * __源服务标记__：选择“AzureMachineLearning”。

    * __源端口范围__：选择 *。

    * __目标__：选择“任何”。

    * __目标端口范围__：选择“22”。

    * __协议__：选择“任何”。

    * __操作__：选择“允许”。

   ![用于在虚拟网络中的 VM 或 HDInsight 群集上执行试验的入站规则的屏幕截图](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    保留网络安全组的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。

    如果你不想使用默认的出站规则并想要限制虚拟网络的出站访问权限, 请参阅[限制来自虚拟网络的出站连接](#limiting-outbound-from-vnet)

1. 将 VM 或 HDInsight 群集附加到 Azure 机器学习服务工作区。 有关详细信息，请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md)。

> [!IMPORTANT]
> Azure 机器学习服务仅支持运行 Ubuntu 的虚拟机。

## <a name="use-azure-kubernetes-service"></a>使用 Azure Kubernetes 服务

若要将虚拟网络中的 Azure Kubernetes 服务添加到工作区, 请按照 Azure 门户中的以下步骤进行操作:

1. 确保控制虚拟网络的网络安全组 (NSG) 已为使用__AzureMachineLearning__作为**源**的 Azure 机器学习服务启用了入站规则。

    ![如何在 Azure 机器学习服务中添加计算](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. 在 [Azure 门户](https://portal.azure.com)中，选择你的 Azure 机器学习服务工作区。

1. 在“应用程序”部分，选择“计算”。 然后选择“添加计算”。

1. 若要将此计算资源配置为使用虚拟网络，请使用以下选项：

    - __网络配置__：选择“高级”。

    - __资源组__：选择包含该虚拟网络的资源组。

    - __虚拟网络__：选择包含子网的虚拟网络。

    - __子网__：选择子网。

    - __Kubernetes 服务地址范围__：选择 Kubernetes 服务地址范围。 此地址范围使用 CIDR 表示法 IP 范围来定义群集可用的 IP 地址。 此范围不得与任何子网 IP 范围重叠。 例如：10.0.0.0/16。

    - __Kubernetes DNS 服务 IP 地址__：选择 Kubernetes DNS 服务 IP 地址。 此 IP 地址将分配给 Kubernetes DNS 服务。 此 IP 地址必须在 Kubernetes 服务地址范围内。 例如：10.0.0.10。

    - __Docker 网桥地址__：选择 Docker 网桥地址。 此 IP 地址将分配给 Docker 网桥。 此 IP 地址不得在任何子网 IP 范围或 Kubernetes 服务地址范围内。 例如：172.17.0.1/16。

   ![Azure 机器学习服务：机器学习计算虚拟网络设置](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 确保控制虚拟网络的 NSG 组已为评分终结点启用了入站安全规则, 以便可以从虚拟网络外部调用它。

    ![如何在 Azure 机器学习服务中添加计算](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > 如果虚拟网络中已有一个 AKS 群集，可将此群集附加到工作区。 有关详细信息，请参阅[如何部署 AKS](how-to-deploy-to-aks.md)。

> [!IMPORTANT]
> 在继续执行上述步骤之前, 请检查群集的先决条件和规划群集的 IP 地址。 有关详细信息，请参阅[在 Azure Kubernetes 服务中配置高级网络](https://docs.microsoft.com/azure/aks/configure-advanced-networking)。
>
>
> 保留 NSG 的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。
>
> Azure Kubernetes 服务和 Azure 虚拟网络应位于同一区域。

也可以使用 **Azure 机器学习 SDK** 来添加虚拟网络中的 Azure Kubernetes 服务。 以下代码在名为 `mynetwork` 的虚拟网络的 `default` 子网中创建新的 Azure Kubernetes 服务实例：

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

创建过程完成后, 可以在虚拟网络后面的 AKS 群集上进行推理/评分。 有关详细信息，请参阅[如何部署 AKS](how-to-deploy-to-aks.md)。

## <a name="next-steps"></a>后续步骤

* [设置训练环境](how-to-set-up-training-targets.md)
* [模型部署位置](how-to-deploy-and-where.md)
* [使用 SSL 安全地部署模型](how-to-secure-web-service.md)

