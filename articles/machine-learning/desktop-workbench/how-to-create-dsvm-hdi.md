---
title: 如何创建 DSVM 和 HDI 作为 Azure 机器学习的计算目标
description: 创建 DSVM 和 HDI Spark 群集作为 Azure 机器学习试验的计算目标。
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 1229a66ec84b4272337a5dd1e17942e46b25e9a0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>创建 DSVM 和 HDI Spark 群集作为计算目标

可以通过添加基于 Ubuntu 的 DSVM（数据科学虚拟机）和适用于 Azure HDInsight 群集的 Apache Spark 等附加计算目标，轻松纵向扩展或横向扩展机器学习试验。 本文逐步讲解在 Azure 中创建这些计算目标的步骤。 有关 Azure ML 计算目标的详细信息，请参阅 [Azure 机器学习试验服务概述](experimentation-service-configuration.md)。

>[!NOTE]
>在继续操作之前，需确保拥有在 Azure 中创建 VM 和 HDI 群集等资源的适当权限。 此外，这两种资源可能会根据配置消耗许多计算核心。 请确保订阅能够为虚拟 CPU 核心提供足够的容量。 始终可以联系 Azure 支持部门，以增加订阅中允许的最大核心数。

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>在 Azure 门户中创建 Ubuntu DSVM

可以从 Azure 门户创建 DSVM。 

1. 从 https://portal.azure.com 登录到 Azure 门户
2. 单击“+新建”链接，搜索“适用于 Linux 的数据科学虚拟机”。
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. 在列表中选择“适用于 Linux (Ubuntu)的数据科学虚拟机”，并遵照屏幕说明创建 DSVM。

>[!IMPORTANT]
>确保选择“密码”作为“身份验证类型”。

![使用密码](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>使用 azure-cli 创建 Ubuntu DSVM

还可以使用 Azure 资源管理模板来部署 DSVM。

>[!NOTE]
>以下所有命令假设是从 Azure 机器学习项目的根目录发出的。

首先，使用偏好的文本编辑器在 `docs` 文件夹中创建 `mydsvm.json` 文件。 （如果项目根文件夹中没有 `docs` 文件夹，请创建该文件夹。）我们使用此文件来配置 Azure 资源管理模板的一些基本参数。 

将以下 JSON 代码片段复制并粘贴到 `mydsvm.json` 文件，并填写相应的值：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

对于 _vmSize_ 字段，可以使用 [Ubuntu DSVM Azure 资源管理模板](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json)中所列的任何受支持 VM 大小。 我们建议使用以下大小之一作为 Azure 机器学习的计算目标。 


>[!TIP]
> 对于[深入学习工作负载](how-to-use-gpu.md)，可以部署到 GPU 支持的 VM。

- [常规用途 VM](/virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [GPU 支持的 VM](/virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

详细了解这些 [Azure 中的 Linux 虚拟机大小](../../virtual-machines/linux/sizes.md)及其[定价信息](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

在 Azure ML Workbench 应用中，单击“文件” --> “打开命令提示符”或单击“打开 PowerShell”菜单项启动 CLI 窗口。 

>[!NOTE]
>也可以在装有 az-cli 的任何命令行环境中执行此操作。

在命令行窗口中输入以下命令：

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>附加 DSVM 计算目标
创建 DSVM 后，可将其附加到 Azure 机器学习项目。

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
现在，应已准备好在此 DSVM 上运行试验。

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>解除分配 DSVM 并随后将其重启
完成 Azure 机器学习中的计算任务时，可以解除分配 DSVM。 此操作会关闭 VM，释放计算资源，但会保留虚拟磁盘。 解除分配 VM 后，不会产生计算费用。

解除分配 VM：

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

若要重新激活 VM，请使用 `az ml start` 命令：

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>扩展 DSVM OS 磁盘
Ubuntu DSVM 附带了 50GB OS 磁盘和 100GB 数据磁盘。 Docker 将其映像存储在数据磁盘上，因为该处有更多空间可用。 如果将该磁盘用作 Azure ML 的计算目标，Docker 引擎会将 Docker 映像拉取到其中，并在其顶层生成 conda 层，因此会用尽该磁盘。 可能需要将 OS 磁盘扩展到更大的大小（例如 200 GB），以免在执行的中途出现“磁盘已满”错误。 请参考[如何使用 Azure CLI 扩展 Linux VM 上的虚拟硬盘](../../virtual-machines/linux/expand-disks.md)了解如何通过 azure-cli 轻松执行此操作。 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>在 Azure 门户中为 Azure HDInsight 群集创建 Apache Spark

若要运行横向扩展 Spark 作业，需在 Azure 门户中为 Azure HDInsight 群集创建 Apache Spark。

1. 从 https://portal.azure.com 登录到 Azure 门户
2. 单击“+新建”链接，搜索“HDInsight”。

    ![查找 hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. 在列表中选择“HDInsight”，单击“创建”按钮。
4. 在“基本信息”配置屏幕上的“群集类型”设置中，确保选择“Spark”作为“群集类型”，选择“Linux”作为“操作系统”，选择“Spark 2.1.0 (HDI 3.6)”作为“版本”。

    ![配置 hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >请注意，在上面的屏幕中，群集包含“群集登录用户名”字段和“安全外壳(SSH)用户名”字段。 这是两个不同的用户标识，不过，为方便起见，可以针对两个登录名指定相同的密码。 “群集登录用户名”用于登录到 HDI 群集的管理 Web UI。 “SSH 登录用户名”用于登录到群集的头节点，Azure 机器学习调度 Spark 作业时需要此用户名。

5. 选择所需的群集大小和节点大小，并完成创建向导。 最长可能需要 30 分钟来完成群集预配。 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>附加 HDI Spark 群集计算目标

创建 Spark HDI 群集后，可将其附加到 Azure 机器学习项目。

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
现在，应已准备好在此 Spark 群集上运行试验。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：
- [Azure 机器学习试验服务概述](experimentation-service-configuration.md)
- [Azure Machine Learning Workbench 试验服务配置文件](experimentation-service-configuration-reference.md)
- [适用于 Azure HDInsight 群集的 Apache Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [数据科学虚拟机](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
