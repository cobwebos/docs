---
title: 使用脚本操作自定义 HDInsight 群集 — Azure | Microsoft Docs
description: 使用脚本操作将自定义组件添加到基于 Linux 的 HDInsight 群集。 脚本操作是 Bash 脚本，可用于自定义群集配置，或者添加 Hue、Solr 或 R 等其他服务和实用工具。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: bc8078a1681b8977a0748f633df02beb2f2bdc8a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>使用脚本操作自定义基于 Linux 的 HDInsight 群集

HDInsight 提供一个称为**脚本操作**的配置选项，该选项可调用用于自定义群集的自定义脚本。 这些脚本用于安装附加组件和更改配置设置。 可以在创建群集期间或创建群集后使用脚本操作。

> [!IMPORTANT]
> 只有基于 Linux 的 HDInsight 群集能够在运行中的群集上使用脚本操作。
>
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

还可以将脚本操作作为 HDInsight 应用程序发布到 Azure Marketplace。 本文档中的某些示例将演示如何使用 PowerShell 和 .NET SDK 的脚本操作命令来安装 HDInsight 应用程序。 有关 HDInsight 应用程序的详细信息，请参阅[将 HDInsight 应用程序发布到 Azure Marketplace 中](hdinsight-apps-publish-applications.md)。

## <a name="permissions"></a>权限

如果使用的是已加入域的 HDInsight 群集，对群集使用脚本操作时需要两个 Ambari 权限：

* **AMBARI.RUN\_CUSTOM\_COMMAND**：默认情况下，Ambari 管理员角色具有此权限。
* **CLUSTER.RUN\_CUSTOM\_COMMAND**：默认情况下，HDInsight 群集管理员和 Ambari 管理员都具有此权限。

有关处理已加入域的 HDInsight 权限的详细信息，请参阅[管理已加入域的 HDInsight 群集](./domain-joined/apache-domain-joined-manage.md)。

## <a name="access-control"></a>访问控制

如果不是 Azure 订阅的管理员/所有者，则帐户必须对包含 HDInsight 群集的资源组至少具有**参与者**访问权限。

此外，如果创建的是 HDInsight 群集，至少对 Azure 订阅具有**参与者**访问权限的用户必须之前已注册 HDInsight 的提供程序。 对订阅具有参与者访问权限的用户首次在订阅上创建资源时，会进行提供程序注册。 不[使用 REST 注册提供程序](https://msdn.microsoft.com/library/azure/dn790548.aspx)创建资源也可完成该操作。

有关使用访问管理的详细信息，请参阅以下文档：

* [Azure 门户中的访问管理入门](../active-directory/role-based-access-control-what-is.md)
* [使用角色分配管理对 Azure 订阅资源的访问权限](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>了解脚本操作

脚本操作是指在 HDInsight 群集的节点上运行的 Bash 脚本。 下面是脚本操作的特征和功能。

* 必须存储在可从 HDInsight 群集访问的 URI 上。 下面是可能的存储位置：

    * HDInsight 群集可访问的 **Azure Data Lake Store** 帐户。 有关将 Azure Data Lake Store 与 HDInsight 配合使用的信息，请参阅[创建包含 Data Lake Store 的 HDInsight 群集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。

        使用存储在 Data Lake Store 中的脚本时，URI 格式为 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`。

        > [!NOTE]
        > 用于访问 Data Lake Store 的服务主体 HDInsight 必须具有对脚本的读取访问权限。

    * **Azure 存储帐户**中的一个 blob，该存储帐户可以是 HDInsight 群集的主存储帐户，也可以是其附加存储帐户。 在创建群集期间，已将这两种存储帐户的访问权限都授予 HDInsight。

    * 公共文件共享服务，例如 Azure Blob、GitHub、OneDrive、Dropbox，等等。

        有关示例 URI，请参阅[脚本操作脚本示例](#example-script-action-scripts)部分。

        > [!WARNING]
        > HDInsight 仅支持通用 Azure 存储帐户。 它当前不支持 __Blob 存储__帐户类型。

* 可限制为**只对特定的节点类型运行**，例如头节点或辅助角色节点。

* 可以是**持久化**或**即席**。

    **持久化**脚本用于自定义通过缩放操作添加到群集的新工作节点。 进行缩放操作时，持久化脚本还可以将更改应用于其他节点类型，如头节点。

  > [!IMPORTANT]
  > 持久化脚本操作必须有唯一的名称。

    **即席**脚本不会持久保存。 它们在运行后不会应用于添加到群集的辅助节点。 以后可将即席脚本升级为持久化脚本，或将持久化脚本降级为即席脚本。

  > [!IMPORTANT]
  > 创建群集期间使用的脚本操作会自动持久保存下来。
  >
  > 即使明确指出应予保存，也不会持久保存失败的脚本。

* 可以接受脚本在执行期间使用的**参数**。

* 在群集节点上以**根级别权限**运行。

* 可以通过 **Azure 门户**、**Azure PowerShell**、**Azure CLI v1.0** 或 **HDInsight .NET SDK** 使用

群集保留已运行的所有脚本的历史记录。 需要查找要升级或降级的脚本的 ID 时，历史记录很有用。

> [!IMPORTANT]
> 没有任何自动方式可撤销脚本操作所做的更改。 手动撤消更改，或者提供一个脚本来撤消更改。

### <a name="script-action-in-the-cluster-creation-process"></a>群集创建过程中的脚本操作

在群集创建期间使用的脚本操作与在现有群集上运行的脚本操作稍有不同：

* 该脚本将**自动持久保存**。

* 脚本**失败**可能会导致群集创建过程失败。

下图演示了在创建过程中执行脚本操作的时间：

![群集创建过程中的 HDInsight 群集自定义和阶段][img-hdi-cluster-states]

脚本在配置 HDInsight 时运行。 脚本在群集中的所有指定节点上并行运行，并且在节点上使用 root 权限运行。

> [!NOTE]
> 可以执行停止和启动服务（包括 Hadoop 相关服务）等操作。 如果停止服务，必须确保 Ambari 服务及其他 Hadoop 相关服务在脚本完成运行之前正在运行。 这些服务必须在群集创建时，成功地确定群集的运行状况和状态。


在创建群集期间，可以同时使用多个脚本操作。 按照这些脚本的指定顺序调用它们。

> [!IMPORTANT]
> 脚本操作必须在 60 分钟内完成，否则会超时。 在群集预配期间，脚本将与其他安装和配置进程一同运行。 争用 CPU 时间和网络带宽等资源可能导致完成脚本所需的时间要长于在开发环境中所需的时间。
>
> 若要让运行脚本所花费的时间降到最低，请避免从源下载和编译应用程序等任务。 预编译应用程序，并将二进制文件存储在 Azure 存储中。


### <a name="script-action-on-a-running-cluster"></a>正在运行的群集上的脚本操作

在运行中群集上运行的脚本发生失败并不会自动导致群集更改为失败状态。 脚本完成后，群集应该恢复“正在运行”状态。

> [!IMPORTANT]
> 即使群集处于“正在运行”状态，失败的脚本也可能已损坏。 例如，脚本无法删除群集所需的文件。
>
> 使用 root 权限运行的脚本操作。 确保先了解脚本的作用，然后再将它应用到群集。

将脚本应用到群集时，如果脚本运行成功，群集状态将从“正在运行”更改为“已接受”，再更改为“HDInsight 配置”，最后回到“正在运行”。 脚本状态记录在脚本操作历史记录中，可以使用此信息确定脚本是成功还是失败。 例如，可以使用 `Get-AzureRmHDInsightScriptActionHistory` PowerShell cmdlet 来查看脚本的状态。 它会返回类似于以下文本的信息：

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]
> 如果在创建群集后更改群集用户 (admin) 的密码，针对此群集运行的脚本操作可能会失败。 如果任何持久性脚本操作以辅助节点为目标，则缩放群集时，这些脚本可能失败。

## <a name="example-script-action-scripts"></a>脚本操作脚本示例

可以通过以下实用工具使用脚本操作脚本：

* Azure 门户
* Azure PowerShell
* Azure CLI v1.0
* HDInsight .NET SDK

HDInsight 提供了脚本用于在 HDInsight 群集上安装以下组件：

| 名称 | 脚本 |
| --- | --- |
| **添加 Azure 存储帐户** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh。请参阅 [将其他存储添加到 HDInsight 群集中](hdinsight-hadoop-add-storage.md). |
| **安装 Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh。请参阅 [在 HDInsight 群集上安装并使用 Hue](hdinsight-hadoop-hue-linux.md). |
| **安装 Presto** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh。请参阅 [在 HDInsight 群集上安装并使用 Presto](hdinsight-hadoop-install-presto.md). |
| **安装 Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh。请参阅 [在 HDInsight 群集上安装并使用 Solr](hdinsight-hadoop-solr-install-linux.md). |
| **安装 Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh。请参阅 [在 HDInsight 群集上安装并使用 Giraph](hdinsight-hadoop-giraph-install-linux.md). |
| **预加载 Hive 库** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh。请参阅 [在 HDInsight 群集上添加 Hive 库](hdinsight-hadoop-add-hive-libraries.md). |
| **安装或更新 Mono** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash。 请参阅 [在 HDInsight 上安装或更新 Mono](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>在创建群集期间使用脚本操作

本部分例举了创建 HDInsight 群集时脚本操作的各种用法。

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>在创建群集期间从 Azure 门户使用脚本操作

1. 根据[在 HDInsight 中创建 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)中的说明开始创建群集。 到达“群集摘要”部分时停止。

2. 从“群集摘要”部分中选择“高级设置”的编辑链接。

    ![高级设置链接](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. 从“高级设置”部分中选择“脚本操作”。 从“脚本操作”部分中选择“+ 提交新项”

    ![提交新脚本操作](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. 使用“选择脚本”条目选择预制的脚本。 若要使用自定义脚本，请选择“自定义”，然后为脚本提供“名称”和“Bash 脚本 URI”。

    ![在“选择脚本”窗体中添加脚本](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    下表描述了窗体上的各项元素：

    | 属性 | 值 |
    | --- | --- |
    | 选择脚本 | 要使用自己的脚本，请选择“自定义”。 否则，请从提供的脚本中选择一个。 |
    | 名称 |指定脚本操作的名称。 |
    | Bash 脚本 URI |指定脚本的 URI。 |
    | 头节点/辅助节点/Zookeeper 节点 |指定在其上运行脚本的节点（**头**节点、**工作**节点或 **ZooKeeper** 节点）。 |
    | parameters |根据脚本的需要，指定参数。 |

    使用“持久保存此脚本操作”条目，确保在缩放操作中应用了脚本。

5. 选择“创建”保存脚本。 然后可以使用“+ 提交新项”再添加一个脚本。

    ![多个脚本操作](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    添加完脚本后，使用“选择”按钮，然后使用“下一步”按钮返回到“群集摘要”部分。

3. 要创建群集，请从“群集摘要”部分中选择“创建”。

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>从 Azure 资源管理器模板使用脚本操作

可通过 Azure 资源管理器模板使用脚本操作。 请参阅 [https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/](https://azure.microsoft.com/en-us/resources/templates/hdinsight-linux-run-script-action/) 查看相关示例。

在此示例中，使用了以下代码添加脚本操作：

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

有关如何部署模板的信息，请参阅以下文档：

* [使用模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [使用模板和 Azure CLI 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>在创建群集期间从 Azure PowerShell 使用脚本操作

本部分使用 [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) cmdlet 来调用脚本，以自定义群集。 在继续前，确保已安装并配置 Azure PowerShell。 有关配置工作站以运行 HDInsight PowerShell cmdlet 的信息，请参阅[安装和配置 Azure PowerShell](/powershell/azure/overview)。

以下脚本演示如何在使用 PowerShell 创建群集时应用脚本操作：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

创建群集可能需要几分钟时间。

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>在创建群集期间从 HDInsight .NET SDK 使用脚本操作

HDInsight .NET SDK 提供客户端库，可简化从 .NET 应用程序中使用 HDInsight 的操作。 有关代码示例，请参阅[使用 .NET SDK 在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action)。

## <a name="apply-a-script-action-to-a-running-cluster"></a>将脚本操作应用到正在运行的群集

在本部分中，了解如何将脚本操作应用于正在运行的群集。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>从 Azure 门户将脚本操作应用到正在运行的群集

1. 在 [Azure 门户](https://portal.azure.com)中，选择 HDInsight 群集。

2. 从 HDInsight 群集概述中，选择“脚本操作”磁贴。

    ![脚本操作磁贴](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > 也可以从“设置”部分中依次选择“所有设置”和“脚本操作”。

3. 从“脚本操作”部分顶部选择“提交新项”。

    ![将脚本添加到正在运行的群集](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. 使用“选择脚本”条目选择预制的脚本。 若要使用自定义脚本，请选择“自定义”，然后为脚本提供“名称”和“Bash 脚本 URI”。

    ![在“选择脚本”窗体中添加脚本](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    下表描述了窗体上的各项元素：

    | 属性 | 值 |
    | --- | --- |
    | 选择脚本 | 要使用自己的脚本，请选择“自定义”。 否则，请选择提供的脚本。 |
    | 名称 |指定脚本操作的名称。 |
    | Bash 脚本 URI |指定脚本的 URI。 |
    | 头节点/辅助节点/Zookeeper 节点 |指定在其上运行脚本的节点（**头**节点、**工作**节点或 **ZooKeeper** 节点）。 |
    | parameters |根据脚本的需要，指定参数。 |

    使用“持久保存此脚本操作”条目，确保在缩放操作中应用了脚本。

5. 最后，使用“创建”按钮将脚本应用到群集。

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>从 Azure PowerShell 将脚本操作应用到正在运行的群集

在继续前，确保已安装并配置 Azure PowerShell。 有关配置工作站以运行 HDInsight PowerShell cmdlet 的信息，请参阅[安装和配置 Azure PowerShell](/powershell/azure/overview)。

以下示例演示如何将脚本操作应用于正在运行的群集：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

操作完成后，会收到类似于下方文本的信息：

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>从 Azure CLI 将脚本操作应用到正在运行的群集

在继续前，确保已安装并配置 Azure CLI。 有关详细信息，请参阅[安装 Azure CLI 1.0](../cli-install-nodejs.md)。

> [!IMPORTANT]
> HDInsight 需要 Azure CLI 1.0。 目前 Azure CLI 2.0 未提供针对使用 HDInsight 的命令。

1. 要切换到 Azure 资源管理器模式，请在命令行中使用以下命令：

    ```bash
    azure config mode arm
    ```

2. 使用以下命令向 Azure 订阅进行身份验证。

    ```bash
    azure login
    ```

3. 使用以下命令将脚本操作应用到正在运行的群集

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    如果省略此命令的参数，系统会提示指定参数。 如果以 `-u` 指定的脚本接受参数，可以使用 `-p` 参数来指定参数。

    有效的节点类型包括 `headnode`、`workernode` 和 `zookeeper`。 如果应将脚本应用到多个节点类型，请指定“;”分隔的类型。 例如，`-n headnode;workernode`。

    若要持久保存脚本，请添加 `--persistOnSuccess`。 也可以在以后使用 `azure hdinsight script-action persisted set` 持久保存脚本。

    作业完成后，会收到类似于以下文本的输出：

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>使用 REST API 将脚本操作应用到正在运行的群集

请参阅[在运行中的群集上运行脚本操作](https://msdn.microsoft.com/library/azure/mt668441.aspx)。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>从 HDInsight .NET SDK 将脚本操作应用到正在运行的群集

有关使用 .NET SDK 向群集应用脚本的示例，请参阅 [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)。

## <a name="view-history-promote-and-demote-script-actions"></a>查看历史记录以及升级和降级脚本操作

### <a name="using-the-azure-portal"></a>使用 Azure 门户

1. 在 [Azure 门户](https://portal.azure.com)中，选择 HDInsight 群集。

2. 从 HDInsight 群集概述中，选择“脚本操作”磁贴。

    ![脚本操作磁贴](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > 也可以从“设置”部分中依次选择“所有设置”和“脚本操作”。

4. 此群集的脚本历史记录显示在“脚本操作”部分上。 此信息包含持久化脚本列表。 在下面的屏幕截图中，可以看到 Solr 脚本已在此群集上运行。 该屏幕截图不显示任何持久化脚本。

    ![“脚本操作”部分](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. 选择历史记录中的脚本会显示此脚本的“属性”部分。 从屏幕顶部，可重新运行脚本或将它升级。

    ![脚本操作属性](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. 还可以使用“脚本操作”部分条目右侧的“...”来执行操作。

    ![脚本操作 ... 用法](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

| 使用以下 cmdlet... | 可以... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |检索有关持久化脚本操作的信息 |
| Get-AzureRmHDInsightScriptActionHistory |检索已应用到群集的脚本操作的历史记录，或特定脚本的详细信息 |
| Set-AzureRmHDInsightPersistedScriptAction |将即席脚本操作升级为持久化脚本操作 |
| Remove-AzureRmHDInsightPersistedScriptAction |将持久化脚本操作降级为即席脚本操作 |

> [!IMPORTANT]
> 使用 `Remove-AzureRmHDInsightPersistedScriptAction` 不会撤消脚本执行的操作。 此 cmdlet 仅删除持久化标志。

以下示例脚本演示如何使用 cmdlet 来升级再降级脚本。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>使用 Azure CLI

| 使用以下 cmdlet... | 可以... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |检索持久化脚本操作的列表 |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |检索有关特定持久化脚本操作的信息 |
| `azure hdinsight script-action history list <clustername>` |检索已应用到群集的脚本操作的历史记录 |
| `azure hdinsight script-action history show <clustername> <scriptname>` |检索有关特定脚本操作的信息 |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |将即席脚本操作升级为持久化脚本操作 |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |将持久化脚本操作降级为即席脚本操作 |

> [!IMPORTANT]
> 使用 `azure hdinsight script-action persisted delete` 不会撤消脚本执行的操作。 此 cmdlet 仅删除持久化标志。

### <a name="using-the-hdinsight-net-sdk"></a>使用 HDInsight .NET SDK

有关使用 .NET SDK 从群集中检索脚本历史记录、升级或降级脚本的示例，请参阅 [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)。

> [!NOTE]
> 本示例还演示了如何使用 .NET SDK 安装 HDInsight 应用程序。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支持 HDInsight 群集上使用的开放源代码软件

Microsoft Azure HDInsight 服务使用围绕 Hadoop 构建的开源技术生态系统。 Microsoft Azure 为开源技术提供常规级别的支持。 有关详细信息，请参阅 [Azure 支持常见问题解答网站](https://azure.microsoft.com/support/faq/)的“支持范围”部分。 HDInsight 服务为内置组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

* **内置组件** - 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。 例如，Yarn ResourceManager、Hive 查询语言 (HiveQL) 及 Mahout 库均属于此类别。 [HDInsight 提供的 Hadoop 群集版本有哪些新功能](hdinsight-component-versioning.md)中提供了群集组件的完整列表。
* **自定义组件** - 作为群集用户，可以安装，或者在工作负荷中使用由社区提供的或自己创建的任何组件。

> [!WARNING]
> HDInsight 群集提供的组件受到完全支持。 Microsoft 支持部门可帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。 Microsoft 支持部门也许能够解决问题，也可能要求你参与可用的开放源代码技术渠道，获取该技术的深入专业知识。 有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [http://stackoverflow.com](http://stackoverflow.com)。此外，Apache 项目在 [http://apache.org](http://apache.org) 上提供了项目站点，例如 [Hadoop](http://hadoop.apache.org/)。

HDInsight 服务提供多种方式来使用自定义组件。 不论在群集上使用组件或安装组件的方式为何，均适用相同级别的支持。 以下列表介绍 HDInsight 群集上最常见的自定义组件用法：

1. 作业提交 - Hadoop 或其他类型的作业可以提交到执行或使用自定义组件的群集。

2. 群集自定义 - 在群集创建期间，可以指定其他设置和安装在群集节点上的自定义组件。

3. 示例 - 对于常见的自定义组件，Microsoft 和其他人可能会提供演示如何在 HDInsight 群集上使用这些组件的示例。 我们不针对这些示例提供支持。

## <a name="troubleshooting"></a>故障排除

可以使用 Ambari web UI 查看脚本操作记录的信息。 如果在创建群集期间脚本失败，则与该群集关联的默认存储帐户中也会提供日志。 本部分提供有关如何使用这两个选项检索日志的信息。

### <a name="using-the-ambari-web-ui"></a>使用 Ambari Web UI

1. 在浏览器中，导航到 https://CLUSTERNAME.azurehdinsight.net。 将 CLUSTERNAME 替换为 HDInsight 群集的名称。

    出现提示时，为群集输入管理员帐户名 (admin) 和密码。 可能需要在 Web 窗体中重新输入管理员凭据。

2. 从页面顶部栏中选择“操作”条目。 随即显示通过 Ambari 在群集上执行的当前操作和以前操作的列表。

    ![选中了“操作”的 Ambari Web UI 栏](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. 查找“操作”列中包含 **run\_customscriptaction** 的条目。 这些条目是在运行脚本操作时创建的。

    ![操作的屏幕截图](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    若要查看 STDOUT 和 STDERR 输出，请选择 run\customscriptaction 条目，并通过链接向下钻取。 此输出是在脚本运行时生成的，可能包含有用的信息。

### <a name="access-logs-from-the-default-storage-account"></a>从默认的存储帐户访问日志

如果因脚本错误导致群集创建失败，则日志会保存在群集存储帐户中。

* 存储日志位于 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`。

    ![操作的屏幕截图](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    在此目录下，日志分别针对头节点、辅助节点和 Zookeeper 节点进行整理。 下面是一些示例：

    * **头节点** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **辅助角色节点** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper 节点** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 相应主机的所有 stdout 和 stderr 将上传到存储帐户。 每个脚本操作各有一个 **output-\*.txt** 和 **errors-\*.txt**。 output-*.txt 文件包含有关在主机上运行的脚本的 URI 信息。 以下文本是此信息的示例：

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 有可能重复创建了同名的脚本操作群集。 在这种情况下，可以根据 DATE 文件夹名称来区分相关的日志。 例如，在不同日期创建的群集 (mycluster) 的文件夹结构类似于以下日志条目：

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 如果在同一天创建同名的脚本操作群集，可以使用唯一的前缀来标识相关日志。

* 如果在临近晚上 00:00（午夜）时创建群集，则日志可能跨越两天。 在这种情况下，会看到同一群集有两个不同的日期文件夹。

* 将日志上传到默认容器可能需要 5 分钟，特别是对于大型群集。 因此，如果想要访问日志，则不应在脚本操作失败时立即删除群集。

### <a name="ambari-watchdog"></a>Ambari 监视程序

> [!WARNING]
> 不要在基于 Linux 的 HDInsight 群集上更改 Ambari 监视程序 (hdinsightwatchdog) 的密码。 更改此帐户的密码将无法在 HDInsight 群集上运行新脚本操作。

### <a name="cant-import-name-blobservice"></a>无法导入名称 BlobService

__症状__：脚本操作失败。 在 Ambari 中查看该操作时，会显示类似于以下错误的文本：

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__原因__：如果升级 HDInsight 群集中随附的 Python Azure 存储客户端，则会发生此错误。 HDInsight 需要 Azure 存储客户端 0.20.0。

__解决方法__：若要解决此错误，请使用 `ssh` 手动连接到每个群集节点，并使用以下命令重新安装正确的存储客户端版本：

```bash
sudo pip install azure-storage==0.20.0
```

有关使用 SSH 连接到群集的信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>历史记录未显示创建群集期间使用的脚本

如果群集是在 2016 年 3 月 15 日之前创建的，则脚本操作历史记录中可能不显示任何条目。 调整群集大小后，脚本会出现在脚本操作历史记录中。

有两种例外情况：

* 如果群集是在 2015 年 9 月 1 日之前创建的。 这是脚本操作的推出时间。 在此日期之前创建的任何群集都不可能是使用脚本操作创建的。

* 如果在创建群集期间使用了多个脚本操作，并将相同的名称、相同的 URI 用于多个脚本，但将不同的参数用于多个脚本。 在这种情况下，会收到以下错误：

    由于现有脚本中的脚本名称有冲突，因此无法在此群集上运行任何新脚本操作。 创建群集时提供的脚本名称全都必须唯一。 现有脚本在重设大小时运行。

## <a name="next-steps"></a>后续步骤

* [为 HDInsight 开发脚本操作脚本](hdinsight-hadoop-script-actions-linux.md)
* [在 HDInsight 群集上安装并使用 Solr](hdinsight-hadoop-solr-install-linux.md)
* [在 HDInsight 群集上安装并使用 Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [将其他存储添加到 HDInsight 群集中](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "群集创建期间的阶段"
