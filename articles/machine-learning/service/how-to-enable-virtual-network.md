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
ms.date: 01/08/2019
ms.openlocfilehash: fe51f4589075cb275e867c943c5d7df3e8d5d4a0
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795027"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>在 Azure 虚拟网络中安全运行试验和推理

本文介绍如何在虚拟网络中运行试验和推理。 虚拟网络充当安全边界，可将 Azure 资源与公共 Internet 相隔离。 你也可以将 Azure 虚拟网络加入本地网络。 在虚拟网络中可以安全训练模型，以及访问用于推断的已部署模型。

Azure 机器学习服务依赖于其他 Azure 服务提供计算资源。 计算资源（计算目标）用于训练和部署模型。 可在虚拟网络中创建这些计算目标。 例如，可以使用 Microsoft Data Science Virtual Machine 来训练模型，然后将该模型部署到 Azure Kubernetes 服务 (AKS)。 有关虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

## <a name="prerequisites"></a>必备组件

本文档假定您熟悉 Azure 虚拟网络和 IP 网络一般情况下。 本文档还假定你已创建的虚拟网络和子网将用于你的计算资源。 如果您不熟悉 Azure 虚拟网络，阅读以下文章以了解该服务：

* [IP 寻址](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [快速入门：创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [筛选网络流量](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>工作区的存储帐户

> [!IMPORTANT]
> 您可以将只有在执行试验时，附加到虚拟网络背后的 Azure 机器学习服务工作区的存储帐户。 推断需要无限制的访问的存储帐户。 如果不确定是否已修改这些设置，请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)中的“更改默认网络访问规则”。 使用步骤进行推断时允许来自所有网络访问。

若要使用 Azure 机器学习试验使用 Azure 存储的虚拟网络背后的功能，请按照以下步骤：

1. 创建示例试验计算。 机器学习计算后的虚拟网络或附加到工作区 ex 试验计算。 HDInsight 群集或虚拟机。 有关详细信息，请参阅[使用机器学习计算](#use-machine-learning-compute)并[使用虚拟机或 HDInsight 群集](#use-a-virtual-machine-or-hdinsight-cluster)本文档中的部分
2. 请转到附加到工作区的存储。 ![显示连接到 Azure 机器学习服务工作区的 Azure 存储的 Azure 门户的映像](./media/how-to-enable-virtual-network/workspace-storage.png)
3. 在 Azure 存储页上，选择__防火墙和虚拟网络__。 ![映像的 Azure 门户显示防火墙和虚拟网络在 Azure 存储页上的部分](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. 上__防火墙和虚拟网络__页上，选择以下：
    - 选择“所选网络”。
    - 下__虚拟网络__选择__将现有虚拟网络添加__添加试验计算所驻留的虚拟网络。 （请参阅步骤 1。）
    - 选择__允许受信任的 Microsoft 服务访问此存储帐户__。
![映像的 Azure 门户显示防火墙和虚拟网络下 Azure 存储页](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. 在运行时试验，试验代码中，更改运行的配置为使用 blob 存储：
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>你的工作区的密钥保管库
Azure 机器学习服务使用与工作区关联的 Key Vault 实例来存储不同类型的凭据：
* 关联的存储帐户连接字符串
* 到 Azure 容器存储库实例密码
* 连接字符串应用于数据存储。 

若要使用 Azure 机器学习试验使用密钥保管库的虚拟网络背后的功能，请按照以下步骤：
1. 请转到与工作区关联的密钥保管库。 ![显示与 Azure 机器学习服务工作区相关联的密钥保管库的 Azure 门户的映像](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. 在密钥保管库页上，选择__防火墙和虚拟网络__部分。 ![映像的 Azure 门户显示防火墙和虚拟网络在密钥保管库页上的部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. 上__防火墙和虚拟网络__页上，选择以下：
    - 选择“所选网络”。
    - 下__虚拟网络__选择__添加现有虚拟网络__添加试验计算所驻留的虚拟网络。
    - 选择__允许受信任的 Microsoft 服务跳过此防火墙__。
![映像的 Azure 门户显示防火墙和虚拟网络在密钥保管库页](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>使用机器学习计算

若要在虚拟网络中使用 Azure 机器学习计算，请使用以下信息了解网络要求：

- 该虚拟网络必须与 Azure 机器学习服务工作区位于同一订阅和区域。

- 为机器学习计算群集指定的子网必须提供足够的未分配 IP 地址，以容纳面向该群集的 VM 数目。 如果该子网没有足够的未分配 IP 地址，则只分配该群集的一部分资源。

- 如果你打算通过限制流量来保护虚拟网络，请为机器学习计算服务保持打开某些端口。 有关详细信息，请参阅[所需的端口](#mlcports)。

- 检查对虚拟网络的订阅或资源组实施的安全策略或锁定是否限制了管理虚拟网络所需的权限。

- 若要将多个机器学习计算群集放入一个虚拟网络，可能需要请求提高一个或多个资源的配额。

    机器学习计算自动在包含虚拟网络的资源组中分配更多网络资源。 对于每个机器学习计算群集，Azure 机器学习服务将分配以下资源：

    - 一个网络安全组 (NSG)

    - 一个公共 IP 地址

    - 一个负载均衡器

  这些资源受订阅的[资源配额](https://docs.microsoft.com/azure/azure-subscription-service-limits)限制。

### <a id="mlcports"></a>所需的端口

机器学习计算目前使用 Azure Batch 服务在指定的虚拟网络中预配 VM。 子网必须允许来自 Batch 服务的入站通信。 这种通信用于在机器学习计算节点上计划运行，以及与 Azure 存储和其他资源进行通信。 Batch 在附加到 VM 的网络接口 (NIC) 级别添加 NSG。 这些 NSG 自动配置允许以下流量的入站和出站规则：

- 入站 TCP 流量，端口 29876 和 29877 从上的__服务标记__的__BatchNodeManagement__。

    ![显示使用 BatchNodeManagement 服务标记的入站的规则的 Azure 门户的映像](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- （可选）在端口 22，以允许远程访问的入站的 TCP 流量。 仅当你想要在公共 IP 上使用 ssh 建立连接，才需要此是。
 
- 任何端口上通往虚拟网络的出站流量。

- 任何端口上通往 Internet 的出站流量。 

在 Batch 配置的 NSG 中修改或添加入站/出站规则时，请务必小心。 如果 NSG 阻止与计算节点通信，则机器学习计算服务会将计算节点的状态设置为不可用。

不需在子网级别指定 NSG，因为 Batch 会配置其自己的 NSG。 但是，如果指定的子网具有关联的 NSG 和/或防火墙，请如前所述配置入站和出站安全规则。 

下面的屏幕截图显示了如何在 Azure 门户中查找的 NSG 规则配置：

![机器学习计算的入站 NSG 规则屏幕截图](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![机器学习计算的出站 NSG 规则屏幕截图](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a> 限制从虚拟网络的出站连接

如果您不想要使用的默认出站规则，并且想要限制你的虚拟网络的出站访问权限，请执行以下步骤：

- 拒绝使用 NSG 规则的出站 internet 连接 

- 限制出站通信到 Azure 存储 (使用__服务标记__的__Storage.Region_Name__ ex。 Storage.EastUS)，Azure 容器注册表 (使用__服务标记__的__AzureContainerRegistry.Region_Name__ ex。 AzureContainerRegistry.EastUS)，和 Azure 机器学习服务 (使用__服务标记__的__AzureMachineLearning__)

下面的屏幕截图显示了如何在 Azure 门户中查找的 NSG 规则配置：

![机器学习计算的出站 NSG 规则屏幕截图](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)




### <a name="create-machine-learning-compute-in-a-virtual-network"></a>在虚拟网络中创建机器学习计算

若要使用 Azure 门户创建机器学习计算群集，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择你的 Azure 机器学习服务工作区。

1. 在“应用程序”部分，选择“计算”。 然后选择“添加计算”。 

    ![如何在 Azure 机器学习服务中添加计算](./media/how-to-enable-virtual-network/add-compute.png)

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
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

创建过程完成后，可以使用该群集训练模型。 有关详细信息，请参阅[选择并使用用于训练的计算目标](how-to-set-up-training-targets.md)。

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>使用虚拟机或 HDInsight 群集

若要将虚拟网络中的虚拟机或 Azure HDInsight 群集与工作区配合使用，请执行以下步骤：

> [!IMPORTANT]
> Azure 机器学习服务仅支持运行 Ubuntu 的虚拟机。

1. 使用 Azure 门户或 Azure CLI 创建 VM 或 HDInsight 群集，并将其放入 Azure 虚拟网络。 有关详细信息，请参阅以下文档：
    * [为 Linux VM 创建和管理 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虚拟网络扩展 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. 若要允许 Azure 机器学习服务与 VM 或群集上的 SSH 端口通信，必须为 NSG 配置一个源条目。 SSH 端口通常是端口 22。 若要允许来自此源的流量，请使用以下信息：

    * __源__：选择“服务标记”。

    * __源服务标记__：选择“AzureMachineLearning”。

    * __源端口范围__：选择 *。

    * __目标__：选择“任何”。

    * __目标端口范围__：选择“22”。

    * __协议__：选择“任何”。

    * __操作__：选择“允许”。

   ![用于在虚拟网络中的 VM 或 HDInsight 群集上执行试验的入站规则的屏幕截图](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    保留 NSG 的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。

    如果您不想要使用的默认出站规则，并且想要限制你的虚拟网络的出站访问权限，请参阅[限制从虚拟网络的出站连接](#limiting-outbound-from-vnet)
    
1. 将 VM 或 HDInsight 群集附加到 Azure 机器学习服务工作区。 有关详细信息，请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md)。

## <a name="use-azure-kubernetes-service"></a>使用 Azure Kubernetes 服务

> [!IMPORTANT]
> 在继续执行后续步骤之前，请检查先决条件，并为群集规划 IP 寻址。 有关详细信息，请参阅[在 Azure Kubernetes 服务中配置高级网络](https://docs.microsoft.com/azure/aks/configure-advanced-networking)。
> 

> 保留 NSG 的默认出站规则。 有关详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的“默认安全规则”。
>
> Azure Kubernetes 服务和 Azure 虚拟网络应位于同一区域。

若要将虚拟网络中的 Azure Kubernetes 服务添加到工作区，请在 Azure 门户中执行以下步骤：

1. 控件具有虚拟网络的入站规则启用了 Azure 机器学习服务使用请确保 NSG 组__服务标记__的__AzureMachineLearning__

    ![如何在 Azure 机器学习服务中添加计算](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. 在 [Azure 门户](https://portal.azure.com)中，选择你的 Azure 机器学习服务工作区。

1. 在“应用程序”部分，选择“计算”。 然后选择“添加计算”。 

    ![如何在 Azure 机器学习服务中添加计算](./media/how-to-enable-virtual-network/add-compute.png)

1. 若要将此计算资源配置为使用虚拟网络，请使用以下选项：

    - __网络配置__：选择“高级”。

    - __资源组__：选择包含该虚拟网络的资源组。

    - __虚拟网络__：选择包含子网的虚拟网络。

    - __子网__：选择子网。

    - __Kubernetes 服务地址范围__：选择 Kubernetes 服务地址范围。 此地址范围使用 CIDR 表示法 IP 范围来定义群集可用的 IP 地址。 此范围不得与任何子网 IP 范围重叠。 例如：10.0.0.0/16。

    - __Kubernetes DNS 服务 IP 地址__：选择 Kubernetes DNS 服务 IP 地址。 此 IP 地址将分配给 Kubernetes DNS 服务。 此 IP 地址必须在 Kubernetes 服务地址范围内。 例如：10.0.0.10。

    - __Docker 网桥地址__：选择 Docker 网桥地址。 此 IP 地址将分配给 Docker 网桥。 此 IP 地址不得在任何子网 IP 范围或 Kubernetes 服务地址范围内。 例如：172.17.0.1/16。

   ![Azure 机器学习服务：机器学习计算虚拟网络设置](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 控件具有虚拟网络的入站规则，以便它可以从调用，虚拟网络外部的评分终结点启用请确保 NSG 组

    ![如何在 Azure 机器学习服务中添加计算](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > 如果虚拟网络中已有一个 AKS 群集，可将此群集附加到工作区。 有关详细信息，请参阅[如何部署 AKS](how-to-deploy-to-aks.md)。

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
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

创建过程完成后，可在虚拟网络后的 AKS 群集上执行推理。 有关详细信息，请参阅[如何部署 AKS](how-to-deploy-to-aks.md)。

## <a name="next-steps"></a>后续步骤

* [设置训练环境](how-to-set-up-training-targets.md)
* [模型部署位置](how-to-deploy-and-where.md)
* [使用 SSL 安全地部署模型](how-to-secure-web-service.md)

