---
title: 在虚拟网络中运行试验和推理
titleSuffix: Azure Machine Learning service
description: 在 Azure 虚拟网络中运行机器学习试验和推理。 了解如何创建用于模型定型的计算目标, 以及如何在虚拟网络中运行推理。 了解安全虚拟网络的要求, 如需要入站和出站端口。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: bd70957671c11137465225aa3bbb046b12a2c650
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966907"
---
# <a name="run-experiments-and-inference-securely-within-an-azure-virtual-network"></a>在 Azure 虚拟网络中安全地运行试验和推理

本文介绍如何在虚拟网络中运行试验和推理 (或模型评分)。 虚拟网络充当安全边界，可将 Azure 资源与公共 Internet 相隔离。 你也可以将 Azure 虚拟网络加入本地网络。 通过加入网络, 可以安全地训练模型并访问已部署的模型以进行推断。 推理或模型计分是部署模型用于预测的阶段, 最常见的是生产数据。

Azure 机器学习服务依赖于其他 Azure 服务提供计算资源。 计算资源 (或计算目标) 用于定型和部署模型。 可以在虚拟网络中创建目标。 例如, 你可以使用 Microsoft Data Science Virtual Machine 训练模型, 然后将模型部署到 Azure Kubernetes 服务 (AKS)。 有关虚拟网络的详细信息, 请参阅[Azure 虚拟网络概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

本文提供了有关*高级安全设置*的详细信息, 以及基本或实验用例所不需要的信息。 本文的某些部分提供各种方案的配置信息。 您无需按顺序或完整地完成说明。

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 机器学习服务[工作区](how-to-manage-workspace.md), 请创建一个。 本文假设一般都熟悉 Azure 虚拟网络服务和 IP 网络。 本文还假定你已创建要用于计算资源的虚拟网络和子网。 如果你不熟悉 Azure 虚拟网络服务, 可以在以下文章中了解相关信息:

* [IP 寻址](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [快速入门：创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [筛选网络流量](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="use-a-storage-account-for-your-workspace"></a>使用工作区的存储帐户

若要为虚拟网络中的工作区使用 Azure 存储帐户, 请执行以下操作:

1. 在虚拟网络后创建试验计算实例 (例如机器学习计算实例), 或将试验计算实例附加到工作区 (例如, HDInsight 群集或虚拟机)。 

   有关详细信息, 请参阅本文中的 "使用机器学习计算实例" 和 "使用虚拟机或 HDInsight 群集" 部分。

1. 在 Azure 门户中, 请前往附加到工作区的存储。 

   ![附加到 Azure 机器学习服务工作区的存储](./media/how-to-enable-virtual-network/workspace-storage.png)

1. 在 " **Azure 存储空间**" 页上, 选择 "__防火墙和虚拟网络__"。 

   !["Azure 存储" 页上的 "防火墙和虚拟网络" 区域 Azure 门户](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 在 "__防火墙和虚拟网络__" 页上, 执行以下操作:
    - 选择“所选网络”。
    - 在 "__虚拟网络__" 下, 选择 "__添加现有虚拟网络__" 链接。 此操作将添加试验计算实例所在的虚拟网络 (请参阅步骤 1)。
    - 选中 "__允许受信任的 Microsoft 服务访问此存储帐户__" 复选框。

   ![Azure 门户中的 "防火墙和虚拟网络" 窗格](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

1. 运行试验时, 请在试验代码中更改运行配置以使用 Azure Blob 存储:

    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> 你可以在虚拟网络中将 Azure 机器学习服务的_默认存储帐户_设置_为仅用于试验_。
>
> 你可以在虚拟网络中将_非默认存储帐户_置于_仅用于试验_。
>
> 用于_推理_的默认和非默认存储帐户都必须具有_对存储帐户的无限制访问权限_。
>
> 如果不确定是否修改了这些设置, 请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)中的 "更改默认网络访问规则" 一节。 按照说明允许在推断过程中从所有网络进行访问, 或使用模型评分。

## <a name="use-a-key-vault-instance-with-your-workspace"></a>将密钥保管库实例与工作区配合使用

Azure 机器学习服务使用与工作区关联的密钥保管库实例来存储以下凭据:
* 关联的存储帐户连接字符串
* 密码到 Azure 容器存储库实例
* 数据存储的连接字符串

若要在虚拟网络后使用 Azure Key Vault Azure 机器学习试验功能, 请执行以下操作:
1. 中转到与工作区关联的密钥保管库。 

   ![与 Azure 机器学习服务工作区关联的密钥保管库](./media/how-to-enable-virtual-network/workspace-key-vault.png)

1. 在 " **Key Vault** " 页上的左窗格中, 选择 "__防火墙和虚拟网络__"。 

   !["Key Vault" 窗格中的 "防火墙和虚拟网络" 部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. 在 "__防火墙和虚拟网络__" 页上, 执行以下操作:
    - 在“允许的访问来源”下，选择“所选网络”。
    - 在 "__虚拟网络__" 下, 选择 "__添加现有虚拟网络__" 以添加试验计算实例所在的虚拟网络。
    - 在 "__允许受信任的 Microsoft 服务跳过此防火墙__" 下, 选择 __"是"__ 。

   !["Key Vault" 窗格中的 "防火墙和虚拟网络" 部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)

## <a name="use-a-machine-learning-compute-instance"></a>使用机器学习计算实例

若要在虚拟网络中使用 Azure 机器学习计算实例, 请考虑以下网络要求:

- 该虚拟网络必须与 Azure 机器学习服务工作区位于同一订阅和区域。

- 为计算群集指定的子网必须具有足够的未分配 IP 地址, 才能容纳群集目标 Vm 的数量。 如果该子网没有足够的未分配 IP 地址，则只分配该群集的一部分资源。

- 如果打算通过限制流量来保护虚拟网络, 请为计算服务留出一些打开的端口。 有关详细信息，请参阅[所需的端口](#mlcports)部分。

- 查看虚拟网络的订阅或资源组的安全策略或锁定是否限制了管理虚拟网络的权限。

- 如果要在一个虚拟网络中放置多个计算群集, 则可能需要为一个或多个资源请求增加配额。

    机器学习计算实例会自动分配包含虚拟网络的资源组中的其他网络资源。 对于每个计算群集, 服务分配以下资源:

    - 一个网络安全组

    - 一个公共 IP 地址

    - 一个负载均衡器

  这些资源受订阅的[资源配额](https://docs.microsoft.com/azure/azure-subscription-service-limits)限制。

### <a id="mlcports"></a>所需的端口

机器学习计算目前使用 Azure Batch 服务在指定的虚拟网络中预配 VM。 子网必须允许来自 Batch 服务的入站通信。 此通信用于计划在机器学习计算节点上运行, 并与 Azure 存储和其他资源通信。 Batch 服务在附加到 Vm 的网络接口 (Nic) 级别添加网络安全组 (Nsg)。 这些 NSG 自动配置允许以下流量的入站和出站规则：

- 来自__BatchNodeManagement__的__服务标记__的端口29876和29877上的入站 TCP 流量。

    ![使用 BatchNodeManagement 服务标记的入站规则](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- 可有可无端口22上的入站 TCP 流量, 允许远程访问。 仅当要在公共 IP 上使用 SSH 进行连接时, 才使用此端口。

- 任何端口上通往虚拟网络的出站流量。

- 任何端口上通往 Internet 的出站流量。

在 Batch 配置的 NSG 中修改或添加入站或出站规则时，请务必小心。 如果 NSG 阻止与计算节点通信, 则计算服务会将计算节点的状态设置为 "不可用"。

不需要在子网级别指定 Nsg, 因为 Azure Batch 服务配置其自己的 Nsg。 但是, 如果指定的子网具有关联的 Nsg 或防火墙, 请按前面所述配置入站和出站安全规则。

下图显示了 Azure 门户中的 NSG 规则配置:

![机器学习计算的入站 NSG 规则](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![机器学习计算的出站 NSG 规则](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>限制来自虚拟网络的出站连接

如果你不想使用默认的出站规则, 并且想要限制虚拟网络的出站访问权限, 请执行以下操作:

- 使用 NSG 规则拒绝出站 internet 连接。

- 限制出站流量:
   - Azure 存储, 使用__Region_Name__的__服务标记__(例如 EastUS)
   - Azure 容器注册表, 使用__AzureContainerRegistry. Region_Name__的__服务标记__(例如 AzureContainerRegistry EastUS)
   - Azure 机器学习服务, 使用__AzureMachineLearning__的__服务标记__

下图显示了 Azure 门户中的 NSG 规则配置:

![机器学习计算的出站 NSG 规则](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>用户定义的用于强制隧道的路由

如果在机器学习计算中使用强制隧道, 请将[用户定义的路由 (udr)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)添加到包含计算资源的子网。

* 为每个 IP 地址建立 UDR, 这些 IP 地址由存在资源的区域中的 Azure Batch 服务使用。 这些 Udr 允许批处理服务与计算节点通信, 以便进行任务计划。 若要获取 Batch 服务的 IP 地址列表, 请与 Azure 支持部门联系。

* 到 Azure 存储的出站流量不得被本地网络设备阻止。 具体而言, url 的格式`<account>.table.core.windows.net`为、 `<account>.queue.core.windows.net`和`<account>.blob.core.windows.net`。

添加 Udr 时, 请为每个相关的批 IP 地址前缀定义路由, 并将 "__下一跃点类型__" 设置为 " __Internet__"。 下图显示了 Azure 门户中的此 UDR 的示例:

![地址前缀的 UDR 示例](./media/how-to-enable-virtual-network/user-defined-route.png)

有关详细信息, 请参阅[在虚拟网络中创建 Azure Batch 池](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)。

### <a name="create-a-machine-learning-compute-cluster-in-a-virtual-network"></a>在虚拟网络中创建机器学习计算群集

若要创建机器学习计算群集, 请执行以下操作:

1. 在 [Azure 门户](https://portal.azure.com)中，选择你的 Azure 机器学习服务工作区。

1. 在__应用程序__部分, 选择 "__计算__", 然后选择 "__添加计算__"。

1. 若要将此计算资源配置为使用虚拟网络, 请执行以下操作:

    a. 对于 "__网络配置__", 选择 "__高级__"。

    b. 在 "__资源组__" 下拉列表中, 选择包含虚拟网络的资源组。

    c. 在 "__虚拟网络__" 下拉列表中, 选择包含子网的虚拟网络。

    d. 在 "__子网__" 下拉列表中, 选择要使用的子网。

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

创建过程完成后, 在试验中使用群集来训练模型。 有关详细信息，请参阅[选择并使用用于训练的计算目标](how-to-set-up-training-targets.md)。

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>使用虚拟机或 HDInsight 群集

若要在工作区中使用虚拟网络中的虚拟机或 Azure HDInsight 群集, 请执行以下操作:

1. 使用 Azure 门户或 Azure CLI 创建 VM 或 HDInsight 群集, 并将群集放置在 Azure 虚拟网络中。 有关详细信息，请参阅以下文章：
    * [为 Linux VM 创建和管理 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虚拟网络扩展 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 若要允许 Azure 机器学习服务与 VM 或群集上的 SSH 端口通信, 请为网络安全组配置源条目。 SSH 端口通常是端口 22。 若要允许来自此源的流量, 请执行以下操作:

    * 在 "__源__" 下拉列表中, 选择 "__服务标记__"。

    * 在 "__源服务标记__" 下拉列表中, 选择 " __AzureMachineLearning__"。

    * 在 "__源端口范围__" 下拉列表中, 选择 __*__ 。

    * 在 "__目标__" 下拉列表中, 选择 "__任意__"。

    * 在 "__目标端口范围__" 下拉列表中, 选择 " __22__"。

    * 在 "__协议__" 下, 选择 "__任意__"。

    * 在 "__操作__" 下, 选择 "__允许__"。

   ![用于在 VM 或虚拟网络中的 HDInsight 群集上执行试验的入站规则](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    保留网络安全组的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。

    如果你不想使用默认的出站规则, 并且想要限制对虚拟网络的出站访问权限, 请参阅[限制虚拟网络的出站连接](#limiting-outbound-from-vnet)部分。

1. 将 VM 或 HDInsight 群集附加到 Azure 机器学习服务工作区。 有关详细信息，请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md)。

> [!IMPORTANT]
> Azure 机器学习服务仅支持运行 Ubuntu 的虚拟机。

## <a name="use-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes 服务 (AKS)

若要将虚拟网络中的 AKS 添加到工作区, 请执行以下操作:

> [!IMPORTANT]
> 开始以下过程之前, 请检查系统必备组件, 并规划群集的 IP 寻址。 有关详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中配置高级网络](https://docs.microsoft.com/azure/aks/configure-advanced-networking)。
>
> 保留 NSG 的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。
>
> AKS 实例和 Azure 虚拟网络应位于同一区域。

1. 在[Azure 门户](https://portal.azure.com)中, 请确保控制虚拟网络的 NSG 具有一个入站规则, 该规则是使用__AzureMachineLearning__作为**源**为 Azure 机器学习服务启用的。

    !["Azure 机器学习服务" 添加计算窗格](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. 选择 Azure 机器学习服务工作区。

1. 在__应用程序__部分, 选择 "__计算__", 然后选择 "__添加计算__"。

1. 若要将此计算资源配置为使用虚拟网络, 请执行以下操作:

    - 对于 "__网络配置__", 选择 "__高级__"。

    - 在 "__资源组__" 下拉列表中, 选择包含虚拟网络的资源组。

    - 在 "__虚拟网络__" 下拉列表中, 选择包含子网的虚拟网络。

    - 在 "__子网__" 下拉列表中, 选择子网。

    - 在 " __Kubernetes 服务地址范围__" 框中, 输入 Kubernetes 服务地址范围。 此地址范围使用无类域间路由 (CIDR) 表示法 IP 范围来定义可用于群集的 IP 地址。 它不得与任何子网 IP 范围 (例如 10.0.0.0/16) 重叠。

    - 在 " __KUBERNETES dns 服务 ip 地址__" 框中, 输入 Kubernetes DNS 服务 ip 地址。 此 IP 地址将分配给 Kubernetes DNS 服务。 它必须位于 Kubernetes service address 范围内 (例如, 10.0.0.10)。

    - 在 " __docker 桥地址__" 框中, 输入 docker 桥地址。 此 IP 地址将分配给 Docker 网桥。 它不得位于任何子网 IP 范围或 Kubernetes 服务地址范围内 (例如, 172.17.0.1/16)。

   ![Azure 机器学习服务：机器学习计算虚拟网络设置](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 确保控制虚拟网络的 NSG 组已为评分终结点启用了入站安全规则, 以便可以从虚拟网络外部调用它。

    ![入站安全规则](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > 如果虚拟网络中已有一个 AKS 群集，可将此群集附加到工作区。 有关详细信息，请参阅[如何部署 AKS](how-to-deploy-to-aks.md)。

你还可以使用 Azure 机器学习 SDK 在虚拟网络中添加 AKS。 以下代码在名为`default` `mynetwork`的虚拟网络的子网中创建新的 AKS 实例:

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

创建过程完成后, 可以在虚拟网络后面的 AKS 群集上运行推理或模型评分。 有关详细信息，请参阅[如何部署 AKS](how-to-deploy-to-aks.md)。

## <a name="next-steps"></a>后续步骤

* [设置训练环境](how-to-set-up-training-targets.md)
* [模型部署位置](how-to-deploy-and-where.md)
* [使用 SSL 安全地部署模型](how-to-secure-web-service.md)

