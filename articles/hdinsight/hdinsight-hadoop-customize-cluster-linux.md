---
title: 使用脚本操作自定义 Azure HDInsight 群集
description: 使用脚本操作将自定义组件添加到基于 Linux 的 HDInsight 群集。 脚本操作是 Bash 脚本，可用于自定义群集配置，或者添加 Hue、Solr 或 R 等其他服务和实用工具。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 41a57d1ad5d216797fc60ea13acff346734fdef8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433631"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>使用脚本操作自定义 Azure HDInsight 群集

Azure HDInsight 提供一个称为“脚本操作”的配置方法，该方法可以调用自定义脚本来自定义群集。  这些脚本用于安装附加组件和更改配置设置。 可以在创建群集期间或创建群集后使用脚本操作。

还可以将脚本操作作为 HDInsight 应用程序发布到 Azure 市场。 有关 HDInsight 应用程序的详细信息，请参阅[在 Azure 市场中发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)。

## <a name="permissions"></a>权限

对于已加入域的 HDInsight 群集，对群集使用脚本操作时需要两个 Apache Ambari 权限：

* **AMBARI.RUN\_CUSTOM\_COMMAND**。 默认情况下，Ambari 管理员角色具有此权限。
* **CLUSTER.RUN\_CUSTOM\_COMMAND**。 默认情况下，HDInsight 群集管理员和 Ambari 管理员都具有此权限。

有关处理已加入域的 HDInsight 的权限的详细信息，请参阅[使用企业安全性套餐管理 HDInsight 群集](./domain-joined/apache-domain-joined-manage.md)。

## <a name="access-control"></a>访问控制

如果你不是 Azure 订阅的管理员或所有者，则你的帐户必须对包含 HDInsight 群集的资源组至少拥有“参与者”访问权限。

如果创建 HDInsight 群集，则至少对 Azure 订阅拥有“参与者”访问权限的用户必须事先注册 HDInsight 的提供程序。 对订阅具有参与者访问权限的用户首次在订阅上创建资源时，会进行提供程序注册。 如果[使用 REST 注册提供程序](https://msdn.microsoft.com/library/azure/dn790548.aspx)，则无需创建资源也可完成该操作。

获取有关使用访问权限管理的详细信息：

* [Azure 门户中的访问管理入门](../role-based-access-control/overview.md)
* [使用角色分配管理对 Azure 订阅资源的访问权限](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>了解脚本操作

脚本操作是指在 HDInsight 群集的节点上运行的 Bash 脚本。 下面是脚本操作的特征和功能：

* 必须存储在可从 HDInsight 群集访问的 URI 上。 下面是可能的存储位置：
    
    * 对于常规群集：
    
      * ADLS Gen1:用于访问 Data Lake Storage 的服务主体 HDInsight 必须具有对脚本的读取访问权限。 存储在 Data Lake Storage Gen1 中的脚本的 URI 格式为 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`。
      
      * Azure 存储帐户中的一个 Blob，该存储帐户可以是 HDInsight 群集的主存储帐户，也可以是其附加存储帐户。 在创建群集期间，已将这两种存储帐户的访问权限都授予 HDInsight。

        > [!IMPORTANT]  
        > 因为它会导致后续脚本操作脚本存储在此处失败不会旋转此 Azure 存储帐户，在存储密钥。

      * 公共文件共享服务可通过 http:// 路径访问。 示例包括 Azure Blob、 GitHub、 OneDrive。

        有关示例 URI，请参阅[脚本操作脚本示例](#example-script-action-scripts)。

     * 对于 ESP 群集：
         
         * Wasb: / / 或 wasbs: / / 或 http [s]:// 支持 Uri。
            
* 可以限制为只对特定的节点类型运行， 例如头节点或工作节点。

* 可以是持久化或即席脚本。

    持久化脚本用于自定义通过缩放操作添加到群集的新工作节点。 进行缩放操作时，持久化脚本还可以将更改应用于其他节点类型， 例如头节点。

  > [!IMPORTANT]  
  > 持久化脚本操作必须有唯一的名称。

    即席脚本不会持久保存。 它们在运行后不会应用于添加到群集的工作节点。 然后可将即席脚本升级为持久化脚本，或将持久化脚本降级为即席脚本。

  > [!IMPORTANT]  
  > 创建群集期间使用的脚本操作会自动持久保存下来。
  >
  > 即使明确指出应予保存，也不会持久保存失败的脚本。

* 可以接受脚本在执行期间使用的参数。

* 在群集节点上以根级别权限运行。

* 可以通过 Azure 门户、Azure PowerShell、Azure 经典 CLI 或 HDInsight .NET SDK 使用。

群集保留已运行的所有脚本的历史记录。 需要查找要升级或降级的脚本的 ID 时，历史记录很有用。

> [!IMPORTANT]  
> 没有任何自动方式可撤销脚本操作所做的更改。 手动撤消更改，或者提供一个脚本来撤消更改。

### <a name="script-action-in-the-cluster-creation-process"></a>群集创建过程中的脚本操作

在群集创建期间使用的脚本操作与在现有群集上运行的脚本操作稍有不同：

* 该脚本将自动持久保存。

* 脚本失败可能会导致群集创建过程失败。

下图演示了在创建过程中运行脚本操作的时间：

![群集创建过程中的 HDInsight 群集自定义和阶段][img-hdi-cluster-states]

脚本在配置 HDInsight 时运行。 脚本在群集中的所有指定节点上并行运行。 它在节点上使用 root 特权运行。

> [!NOTE]  
> 可以执行停止和启动服务（包括 Apache Hadoop 相关服务）等操作。 如果停止服务，请确保 Ambari 服务及其他 Hadoop 相关服务在脚本完成运行之前正在运行。 这些服务必须在群集创建时，成功地确定群集的运行状况和状态。


在创建群集期间，可以同时使用多个脚本操作。 按照这些脚本的指定顺序调用它们。

> [!IMPORTANT]  
> 脚本操作必须在 60 分钟内完成，否则会超时。在群集预配期间，脚本将与其他安装和配置进程一同运行。 争用 CPU 时间和网络带宽等资源可能导致完成脚本所需的时间要长于在开发环境中所需的时间。
>
> 若要让运行脚本所花费的时间降到最低，请避免从源下载和编译应用程序等任务。 预编译应用程序，并将二进制文件存储在 Azure 存储中。


### <a name="script-action-on-a-running-cluster"></a>正在运行的群集上的脚本操作

在运行中群集上运行的脚本发生失败并不会自动导致群集更改为失败状态。 脚本完成后，群集应该恢复为“正在运行”状态。

> [!IMPORTANT]  
> 即使群集处于“正在运行”状态，失败的脚本也可能已损坏。 例如，脚本无法删除群集所需的文件。
>
> 使用 root 权限运行的脚本操作。 确保先了解脚本的作用，然后再将它应用到群集。

将脚本应用到群集时，群集状态将从“正在运行”更改为“已接受”。   然后，状态将更改为“HDInsight 配置”，最后恢复为“正在运行”，表示脚本成功。   脚本状态记录在脚本操作历史记录中。 此信息告知脚本是成功还是失败。 例如，`Get-AzHDInsightScriptActionHistory` PowerShell cmdlet 显示脚本的状态。 它会返回类似于以下文本的信息：

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> 如果在创建群集后更改群集用户、管理员和密码，针对此群集运行的脚本操作可能会失败。 如果任何持久性脚本操作以工作节点为目标，则缩放群集时，这些脚本可能失败。

## <a name="example-script-action-scripts"></a>脚本操作脚本示例

可以通过以下实用工具使用脚本操作脚本：

* Azure 门户
* Azure PowerShell
* Azure 经典 CLI
* HDInsight .NET SDK

HDInsight 提供了脚本用于在 HDInsight 群集上安装以下组件：

| 名称 | 脚本 |
| --- | --- |
| 添加 Azure 存储帐户 |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`。 请参阅[将其他存储帐户添加到 HDInsight](hdinsight-hadoop-add-storage.md)。 |
| 安装 Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`。 请参阅[在 HDInsight Hadoop 群集上安装并使用 Hue](hdinsight-hadoop-hue-linux.md)。 |
| 安装 Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`。 请参阅[在 HDInsight Hadoop 群集上安装 Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)。 |
| 预加载 Hive 库 |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`。 请参阅[创建 HDInsight 群集时添加自定义 Apache Hive 库](hdinsight-hadoop-add-hive-libraries.md)。 |

## <a name="use-a-script-action-during-cluster-creation"></a>在创建群集期间使用脚本操作

本部分说明了创建 HDInsight 群集时脚本操作的各种用法。

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>在创建群集期间从 Azure 门户使用脚本操作

1. 根据[使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他组件在 HDInsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)中所述开始创建群集。 创建群集期间，将会看到“群集摘要”页。  从“群集摘要”  页中选择__高级设置__的__编辑__链接。

    ![高级设置链接](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. 从“高级设置”部分中选择“脚本操作”   。 在“脚本操作”部分选择“+ 提交新项”。  

    ![提交新脚本操作](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. 使用“选择脚本”条目选择预制的脚本。  若要使用自定义脚本，请选择“自定义”  。 然后提供脚本的“名称”和“Bash 脚本 URI”。  

    ![在“选择脚本”窗体中添加脚本](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    下表描述了窗体上的各项元素：

    | 属性 | 值 |
    | --- | --- |
    | 选择脚本 | 要使用自己的脚本，请选择“自定义”  。 否则，请从提供的脚本中选择一个。 |
    | Name |指定脚本操作的名称。 |
    | Bash 脚本 URI |指定脚本的 URI。 |
    | Head/Worker/ZooKeeper 节点 |指定运行脚本的节点：“Head”、“Worker”或“ZooKeeper”。    |
    | Parameters |根据脚本的需要，指定参数。 |

    使用“持久保存此脚本操作”条目，确保在执行缩放操作期间应用该脚本  。

5. 选择“创建”  保存脚本。 然后可以使用“+ 提交新项”再添加一个脚本。 

    ![多个脚本操作](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    添加完脚本后，选择“选择”按钮，然后选择“下一步”按钮返回到“群集摘要”部分    。

3. 要创建群集，请从“群集摘要”部分中选择“创建”   。

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>从 Azure 资源管理器模板使用脚本操作

可通过 Azure 资源管理器模板使用脚本操作。 有关示例，请参阅[创建 HDInsight Linux 群集并运行脚本操作](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)。

此示例中使用以下代码添加脚本操作：

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

获取有关如何部署模板的详细信息：

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [使用资源管理器模板和 Azure CLI 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>在创建群集期间从 Azure PowerShell 使用脚本操作

本部分使用 [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) cmdlet 来调用脚本，以自定义群集。 开始之前，请确保安装并配置 Azure PowerShell。 若要使用这些 PowerShell 命令，需要 [AZ 模块](https://docs.microsoft.com/powershell/azure/overview)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下脚本演示如何在使用 PowerShell 创建群集时应用脚本操作：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

创建群集可能需要几分钟时间。

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>在创建群集期间从 HDInsight .NET SDK 使用脚本操作

HDInsight .NET SDK 提供客户端库，以方便从 .NET 应用程序使用 HDInsight。 有关代码示例，请参阅[使用 .NET SDK 在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action)。

## <a name="apply-a-script-action-to-a-running-cluster"></a>将脚本操作应用到正在运行的群集

本部分说明如何将脚本操作应用于正在运行的群集。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>从 Azure 门户将脚本操作应用到正在运行的群集

转到 [Azure 门户](https://portal.azure.com)：

1. 在左侧菜单中，选择“所有服务”。 

1. 在“ANALYTICS”下，选择“HDInsight 群集”   。

1. 从列表中选择你的群集。此时会打开默认视图。

1. 在默认视图中的“设置”下，选择“脚本操作”   。

1. 在“脚本操作”页顶部，选择“+ 提交新项”   。

    ![将脚本添加到正在运行的群集](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. 使用“选择脚本”条目选择预制的脚本。  若要使用自定义脚本，请选择“自定义”  。 然后提供脚本的“名称”和“Bash 脚本 URI”。  

    ![在“选择脚本”窗体中添加脚本](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    下表描述了窗体上的各项元素：

    | 属性 | 值 |
    | --- | --- |
    | 选择脚本 | 要使用自己的脚本，请选择“自定义”  。 否则，请选择提供的脚本。 |
    | 名称 |指定脚本操作的名称。 |
    | Bash 脚本 URI |指定脚本的 URI。 |
    | 头节点/辅助节点/Zookeeper 节点 |指定运行脚本的节点：“头节点”、“工作节点”或“ZooKeeper 节点”。    |
    | parameters |根据脚本的需要，指定参数。 |

    使用“持久保存此脚本操作”条目，确保在缩放操作中应用了脚本  。

5. 最后，选择“创建”按钮将脚本应用到群集。 

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>从 Azure PowerShell 将脚本操作应用到正在运行的群集

若要使用这些 PowerShell 命令，需要 [AZ 模块](https://docs.microsoft.com/powershell/azure/overview)。

以下示例演示如何将脚本操作应用于正在运行的群集：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

操作完成后，会收到类似于以下文本的信息：

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>从 Azure CLI 将脚本操作应用到正在运行的群集

开始之前，请确保安装并配置 Azure CLI。 有关详细信息，请参阅[安装 Azure 经典 CLI](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)。

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. 切换到 Azure 资源管理器模式：

    ```bash
    azure config mode arm
    ```

2. 对 Azure 订阅进行身份验证：

    ```bash
    azure login
    ```

3. 将脚本操作应用到正在运行的群集：

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    如果省略此命令的参数，系统会提示你指定参数。 如果指定了 `-u` 的脚本接受参数，可以使用 `-p` 参数来指定参数。

    有效的节点类型包括 `headnode`、`workernode` 和 `zookeeper`。 如果应将脚本应用到多个节点类型，请指定分号 `;` 分隔的类型。 例如，`-n headnode;workernode`。

    若要持久保存脚本，请添加 `--persistOnSuccess`。 也可以在以后使用 `azure hdinsight script-action persisted set` 持久保存脚本。

    作业完成后，会收到类似于以下文本的输出：

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>使用 REST API 将脚本操作应用到正在运行的群集

请参阅 [Azure HDInsight 中的群集 REST API](https://msdn.microsoft.com/library/azure/mt668441.aspx)。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>从 HDInsight .NET SDK 将脚本操作应用到正在运行的群集

有关使用 .NET SDK 将脚本应用到群集的示例，请参阅[针对正在运行的基于 Linux 的 HDInsight 群集应用脚本操作](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)。

## <a name="view-history-and-promote-and-demote-script-actions"></a>查看历史记录以及升级和降级脚本操作

### <a name="the-azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单中，选择“所有服务”。 

1. 在“ANALYTICS”下，选择“HDInsight 群集”   。

1. 从列表中选择你的群集。此时会打开默认视图。

1. 在默认视图中的“设置”下，选择“脚本操作”   。

4. 此群集的脚本历史记录显示在“脚本操作”部分。 此信息包含持久化脚本列表。 以下屏幕截图显示已在此群集上运行了 Solr 脚本。 该屏幕截图未显示任何持久化脚本。

    ![脚本操作](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. 选择历史记录中的脚本会显示此脚本的“属性”部分。  从屏幕顶部，可重新运行脚本或将它升级。

    ![脚本操作 - 属性](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. 还可以使用“脚本操作”部分条目右侧的省略号 **...** 来执行操作。

    ![脚本操作 - 省略号](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | 函数 |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |检索有关持久化脚本操作的信息。 |
| `Get-AzHDInsightScriptActionHistory` |检索已应用到群集的脚本操作的历史记录，或特定脚本的详细信息。 |
| `Set-AzHDInsightPersistedScriptAction` |将即席脚本操作升级为持久化脚本操作。 |
| `Remove-AzHDInsightPersistedScriptAction` |将持久化脚本操作降级为即席脚本操作。 |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` 不会撤消脚本执行的操作。 此 cmdlet 仅删除持久化标志。

以下示例脚本演示如何使用 cmdlet 来升级再降级脚本。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Azure 经典 CLI

| cmdlet | 函数 |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |检索持久化脚本操作的列表。 |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |检索有关特定持久化脚本操作的信息。 |
| `azure hdinsight script-action history list <clustername>` |检索已应用到群集的脚本操作的历史记录。 |
| `azure hdinsight script-action history show <clustername> <scriptname>` |检索有关特定脚本操作的信息。 |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |将即席脚本操作升级为持久化脚本操作。 |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |将持久化脚本操作降级为即席脚本操作。 |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` 不会撤消脚本执行的操作。 此 cmdlet 仅删除持久化标志。

### <a name="the-hdinsight-net-sdk"></a>HDInsight .NET SDK

有关使用 .NET SDK 从群集中检索脚本历史记录、升级或降级脚本的示例，请参阅[针对正在运行的基于 Linux 的 HDInsight 群集应用脚本操作](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)。

> [!NOTE]  
> 此示例还演示了如何使用 .NET SDK 安装 HDInsight 应用程序。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支持 HDInsight 群集上使用的开放源代码软件

Microsoft Azure HDInsight 服务使用围绕 Apache Hadoop 构建的开源技术生态系统。 Microsoft Azure 为开源技术提供常规级别的支持。 有关详细信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)的“支持范围”部分。  HDInsight 服务为内置组件提供附加的支持级别。

在 HDInsight 服务中可以使用两种类型的开源组件：

* **内置组件**。 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。 以下组件属于此类别：

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager。
  * Hive 查询语言 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)。
  * [Apache Mahout](https://mahout.apache.org/)。 
    
    [在 HDInsight 中可以使用哪些 Apache Hadoop 组件和版本？](hdinsight-component-versioning.md)中提供了群集组件的完整列表。

* **自定义组件**。 群集用户可以安装或者在工作负荷中使用由社区提供的或自己创建的任何组件。

> [!WARNING]  
> HDInsight 群集提供的组件受到完全支持。 Microsoft 支持部门可帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助进一步排查问题。 Microsoft 支持部门也许能够解决问题。 支持人员可能要求你参与可用的开源技术渠道，在该处可以找到该技术的深入专业知识。 可以使用许多社区站点， 例如[有关 HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [Stack Overflow](https://stackoverflow.com)。 
>
> Apache 项目还在 [Apache 网站](https://apache.org)上提供了项目站点， 例如 [Hadoop](https://hadoop.apache.org/)。

HDInsight 服务提供多种方式来使用自定义组件。 不论在群集上使用组件或安装组件的方式为何，均适用相同级别的支持。 以下列表介绍 HDInsight 群集上最常见的自定义组件用法：

1. **作业提交**。 Hadoop 或其他类型的作业可以提交到执行或使用自定义组件的群集。

2. **群集自定义**。 在群集创建期间，可以指定其他设置和安装在群集节点上的自定义组件。

3. **示例**。 对于常见的自定义组件，Microsoft 和其他人可能会提供演示如何在 HDInsight 群集上使用这些组件的示例。 我们不针对这些示例提供支持。

## <a name="troubleshooting"></a>故障排除

可以使用 Ambari web UI 查看脚本操作记录的信息。 如果在创建群集期间脚本失败，则与该群集关联的默认存储帐户中也会提供日志。 本部分提供有关如何使用这两个选项检索日志的信息。

### <a name="the-apache-ambari-web-ui"></a>Apache Ambari Web UI

1. 在浏览器中转到 https://CLUSTERNAME.azurehdinsight.net 。 将 **CLUSTERNAME** 替换为 HDInsight 群集名。

    出现提示时，为群集输入管理员帐户名 **admin** 和密码。 可能需要在 Web 表单中重新输入管理员凭据。

2. 从页面顶部栏中选择“操作”  条目。 此时会显示通过 Ambari 在群集上执行的当前操作和以前操作的列表。

    ![选中了“操作”的 Ambari Web UI 栏](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. 查找“操作”  列中包含 **run\_customscriptaction** 的条目。 这些条目是在运行脚本操作时创建的。

    ![操作的屏幕截图](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    若要查看 **STDOUT** 和 **STDERR** 输出，请选择 **run\customscriptaction** 条目，并通过链接向下钻取。 此输出是在脚本运行时生成的，可能包含有用的信息。

### <a name="access-logs-from-the-default-storage-account"></a>从默认的存储帐户访问日志

如果因脚本错误导致群集创建失败，则日志会保存在群集存储帐户中。

* 存储日志位于 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`。

    ![操作的屏幕截图](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    在此目录下，日志分别针对**头节点**、**工作器节点**和 **Zookeeper 节点**进行组织。 请看以下示例：

    * **头节点**：`<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **工作节点**：`<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper 节点**：`<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 相应主机的所有 **stdout** 和 **stderr** 将上传到存储帐户。 每个脚本操作各有一个 **output-\*.txt** 和 **errors-\*.txt**。 **output-*.txt** 文件包含有关在主机上运行的脚本的 URI 信息。 以下文本是此信息的示例：

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 有可能重复创建了同名的脚本操作群集。 在这种情况下，可以根据 **DATE** 文件夹名称来区分相关的日志。 例如，在不同日期创建的群集 **mycluster** 的文件夹结构类似于以下日志条目：

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 如果在同一天创建同名的脚本操作群集，可以使用唯一的前缀来标识相关日志。

* 如果在临近晚上 12:00（午夜）时创建群集，则日志可能跨越两天。 在这种情况下，会看到同一群集有两个不同的日期文件夹。

* 将日志上传到默认容器可能需要 5 分钟，特别是对于大型群集。 因此，如果想要访问日志，则不应在脚本操作失败时立即删除群集。

### <a name="ambari-watchdog"></a>Ambari 监视程序

> [!WARNING]  
> 不要在基于 Linux 的 HDInsight 群集上更改 Ambari 监视程序 (hdinsightwatchdog) 的密码。 更改此帐户的密码将无法在 HDInsight 群集上运行新脚本操作。

### <a name="cant-import-name-blobservice"></a>无法导入名称 BlobService

__症状__。 脚本操作失败。 在 Ambari 中查看该操作时，显示类似于以下错误的文本：

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__原因__。 如果升级 HDInsight 群集中随附的 Python Azure 存储客户端，则会发生此错误。 HDInsight 需要 Azure 存储客户端 0.20.0。

__解决方法__。 若要解决此错误，请使用 `ssh` 手动连接到每个群集节点。 运行以下命令重新安装正确的存储客户端版本：

```bash
sudo pip install azure-storage==0.20.0
```

有关使用 SSH 连接到群集的信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>历史记录未显示创建群集期间使用的脚本

如果群集是在 2016 年 3 月 15 日之前创建的，则脚本操作历史记录中可能不显示任何条目。 调整群集大小后，脚本会出现在脚本操作历史记录中。

有两种例外情况：

* 群集是在 2015 年 9 月 1 日之前创建的。 这是脚本操作的推出时间。 在此日期之前创建的任何群集都不可能是使用脚本操作创建的。

* 创建群集期间使用了多个脚本操作。 或者，将相同的名称和相同的 URI 用于多个脚本，但将不同的参数用于多个脚本。 在这种情况下，将收到以下错误：

    由于现有脚本中的脚本名称有冲突，因此无法在此群集上运行任何新脚本操作。 创建群集时提供的脚本名称全都必须唯一。 现有脚本在调整大小时运行。

## <a name="next-steps"></a>后续步骤

* [为 HDInsight 开发脚本操作脚本](hdinsight-hadoop-script-actions-linux.md)
* [在 HDInsight 群集上安装并使用 Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [将其他存储添加到 HDInsight 群集中](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "群集创建过程中的阶段"
