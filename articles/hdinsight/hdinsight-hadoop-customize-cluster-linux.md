---
title: 使用脚本操作自定义 Azure HDInsight 群集
description: 使用脚本操作将自定义组件添加到 HDInsight 群集。 脚本操作为 Bash 脚本，可用于自定义群集配置。 或添加其他服务和实用工具，如色相、Solr 或 R。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 09/02/2020
ms.openlocfilehash: b30a7822511dc6b4c3ae7e852cba49ebff6e24ad
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400851"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>使用脚本操作自定义 Azure HDInsight 群集

Azure HDInsight 提供名为脚本操作的配置方法，该方法可以调用自定义脚本来自定义群集****。 这些脚本用于安装其他组件以及更改配置设置。 可在创建群集期间或者之后使用脚本操作。

还可以将脚本操作作为 HDInsight 应用程序发布到 Azure 市场。 有关 HDInsight 应用程序的详细信息，请参阅[在 Azure 市场中发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)。

## <a name="permissions"></a>权限

对于已加入域的 HDInsight 群集，对群集使用脚本操作时需要两个 Apache Ambari 权限：

* **AMBARI.RUN\_CUSTOM\_COMMAND**。 默认情况下，Ambari 管理员角色具有此权限。
* **CLUSTER.RUN\_CUSTOM\_COMMAND**。 默认情况下，HDInsight 群集管理员和 Ambari 管理员都具有此权限。

有关处理已加入域的 HDInsight 的权限的详细信息，请参阅[使用企业安全性套餐管理 HDInsight 群集](./domain-joined/apache-domain-joined-manage.md)。

## <a name="access-control"></a>访问控制

如果你不是 Azure 订阅的管理员或所有者，则你的帐户必须对包含 HDInsight 群集的资源组至少拥有“参与者”访问权限。

至少具有对 Azure 订阅的参与者访问权限的用户必须之前已注册提供程序。 在对订阅具有参与者访问权限的用户创建资源时，会进行提供程序注册。 对于不创建资源的情况，请参阅[使用 REST 注册提供程序](https://msdn.microsoft.com/library/azure/dn790548.aspx)。

获取有关使用访问权限管理的详细信息：

* [Azure 门户中的访问管理入门](../role-based-access-control/overview.md)
* [使用角色分配管理对 Azure 订阅资源的访问权限](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>了解脚本操作

脚本操作是指在 HDInsight 群集的节点上运行的 Bash 脚本。 下面是脚本操作的特征和功能：

* 必须存储在可从 HDInsight 群集访问的 URI 上。 下面是可能的存储位置：

    * 对于常规群集：

      * ADLS Gen1：服务主体 HDInsight 使用访问 Data Lake Storage 必须对脚本具有读取访问权限。 存储在 Data Lake Storage Gen1 中的脚本的 URI 格式为 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`。

      * Azure 存储帐户中的一个 Blob，该存储帐户可以是 HDInsight 群集的主存储帐户，也可以是其附加存储帐户。 在创建群集期间，已将这两种存储帐户的访问权限都授予 HDInsight。

        > [!IMPORTANT]  
        > 请勿在此 Azure 存储帐户上轮换存储密钥，因为这会导致对存储在其中的脚本执行后续脚本操作失败。

      * 可通过 http:// 路径访问的公共文件共享服务。 例如 Azure Blob、GitHub、OneDrive。 有关示例 URI，请参阅[脚本操作脚本示例](#example-script-action-scripts)。

     * 对于具有 ESP 的群集，支持 wasb:// 或 wasbs:// 或 http[s]:// URI。

* 可以限制为只对特定的节点类型运行， 例如头节点或工作节点。

* 可以是持久的，也可以是 `ad hoc` 。

    持久化脚本操作必须有唯一的名称。 持久化脚本用于自定义通过缩放操作添加到群集的新工作节点。 进行缩放操作时，持久化脚本还可以将更改应用于其他节点类型， 例如头节点。

    `Ad hoc` 脚本不会持久保存。 创建群集期间使用的脚本操作自动持久保存下来。 它们在运行后不会应用于添加到群集的工作节点。 然后，可以将 `ad hoc` 脚本升级为持久化脚本，或将持久化脚本降级为 `ad hoc` 脚本。 即使明确指出应予保存，也不会持久保存失败的脚本。

* 可以接受脚本在执行期间使用的参数。

* 在群集节点上以根级别权限运行。

* 可以通过 Azure 门户、Azure PowerShell、Azure CLI 或 HDInsight .NET SDK 使用。

* 删除或修改 VM 上的服务文件的脚本操作可能会影响服务的运行状况和可用性。

群集保留已运行的所有脚本的历史记录。 需要查找要升级或降级的脚本的 ID 时，历史记录很有用。

> [!IMPORTANT]  
> 没有任何自动方式可撤销脚本操作所做的更改。 需要手动还原更改，或提供可还原更改的脚本。

### <a name="script-action-in-the-cluster-creation-process"></a>群集创建过程中的脚本操作

在群集创建期间使用的脚本操作与在现有群集上运行的脚本操作稍有不同：

* 该脚本将自动持久保存。

* 脚本失败可能会导致群集创建过程失败。

下图演示了在创建过程中运行脚本操作的时间：

![群集创建期间的 HDInsight 群集自定义和阶段][img-hdi-cluster-states]

在配置 HDInsight 时运行脚本。 脚本在群集中的所有指定节点上并行运行。 它在节点上使用 root 特权运行。

可以执行停止和启动服务（包括 Apache Hadoop 相关服务）等操作。 如果停止服务，请确保 Ambari 及其他 Hadoop 相关服务在脚本完成之前正在运行。 这些所需的服务在群集创建时确定群集的运行状况和状态。

在创建群集期间，可以同时使用多个脚本操作。 这些脚本将按其指定顺序调用。

> [!IMPORTANT]  
> 脚本操作必须在 60 分钟内完成，否则会超时。在群集预配期间，脚本与其他安装和配置进程一同运行。 争用 CPU 时间和网络带宽等资源可能导致完成脚本所需的时间要长于在开发环境中所需的时间。
>
> 若要让运行脚本所花费的时间降到最低，请避免从源下载和编译应用程序等任务。 预编译应用程序，并将二进制文件存储在 Azure 存储中。

### <a name="script-action-on-a-running-cluster"></a>正在运行的群集上的脚本操作

已在运行的群集上发生的脚本失败并不会自动导致群集更改为失败状态。 脚本完成后，群集应该恢复为“正在运行”状态。 即使群集处于“正在运行”状态，失败的脚本也可能已损坏。 例如，脚本无法删除群集所需的文件。

使用 root 权限运行的脚本操作。 确保先了解脚本的作用，然后再将它应用到群集。

将脚本应用到群集时，群集状态将从“正在运行”更改为“已接受”。**** **** 然后，状态将更改为“HDInsight 配置”，最后恢复为“正在运行”，表示脚本成功。**** **** 脚本状态记录在脚本操作历史记录中。 此信息告知脚本是成功还是失败。 例如，`Get-AzHDInsightScriptActionHistory` PowerShell cmdlet 显示脚本的状态。 此命令返回类似于以下文本的信息：

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> 如果在创建群集后更改群集用户、管理员和密码，针对此群集运行的脚本操作可能会失败。 如果任何持久性脚本操作以工作节点为目标，则缩放群集时，这些脚本可能失败。

## <a name="example-script-action-scripts"></a>脚本操作脚本示例

可以通过以下实用工具使用脚本操作脚本：

* Azure 门户
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight 提供了脚本用于在 HDInsight 群集上安装以下组件：

| 名称 | 脚本 |
| --- | --- |
| 添加 Azure 存储帐户 |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. 请参阅[将其他存储帐户添加到 HDInsight](hdinsight-hadoop-add-storage.md)。 |
| 安装 Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. 请参阅[在 HDInsight Hadoop 群集上安装并使用 Hue](hdinsight-hadoop-hue-linux.md)。 |
| 预加载 Hive 库 |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. 请参阅[创建 HDInsight 群集时添加自定义 Apache Hive 库](hdinsight-hadoop-add-hive-libraries.md)。 |

## <a name="script-action-during-cluster-creation"></a>群集创建期间的脚本操作

本部分说明了创建 HDInsight 群集时脚本操作的各种用法。

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>在创建群集期间从 Azure 门户使用脚本操作

1. 按照[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)中的说明，开始创建群集。 从“配置 + 定价”**** 选项卡中，选择“+ 添加脚本操作”****。

    ![Azure 门户群集脚本操作](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. 使用“选择脚本”条目选择预制的脚本。____ 若要使用自定义脚本，请选择“自定义”____。 然后提供脚本的“名称”和“Bash 脚本 URI”。____ ____

    ![在“选择脚本”窗体中添加脚本](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    下表描述窗体中的元素：

    | 属性 | Value |
    | --- | --- |
    | 选择脚本 | 要使用自己的脚本，请选择“自定义”____。 否则，请从提供的脚本中选择一个。 |
    | 名称 |指定脚本操作的名称。 |
    | Bash 脚本 URI |指定脚本的 URI。 |
    | Head/Worker/ZooKeeper |指定运行脚本的节点：“头节点”、“工作节点”或“ZooKeeper 节点”。**** **** **** |
    | parameters |根据脚本的需要，请指定参数。 |

    使用“持久保存此脚本操作”条目，确保在执行缩放操作期间应用该脚本____。

1. 选择“创建”保存脚本。____ 然后可以使用“+ 提交新项”再添加一个脚本。____

    ![HDInsight 多脚本操作](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    添加完脚本后，将返回到“配置 + 定价”**** 选项卡。

1. 照常完成其余的群集创建步骤。

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

本部分使用 [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) cmdlet 来调用脚本，以自定义群集。 开始之前，请确保安装并配置 Azure PowerShell。 若要使用这些 PowerShell 命令，需要 [AZ 模块](https://docs.microsoft.com/powershell/azure/)。

以下脚本演示如何在使用 PowerShell 创建群集时应用脚本操作：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

创建群集可能需要几分钟时间。

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>在创建群集期间从 HDInsight .NET SDK 使用脚本操作

HDInsight .NET SDK 提供客户端库，以方便从 .NET 应用程序使用 HDInsight。 有关代码示例，请参阅[脚本操作](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions)。

## <a name="script-action-to-a-running-cluster"></a>将脚本操作应用到正在运行的群集

本部分说明如何将脚本操作应用于正在运行的群集。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>从 Azure 门户将脚本操作应用到正在运行的群集

1. 登录到 [Azure 门户](https://portal.azure.com)并找到群集。

1. 在默认视图中的“设置”下，选择“脚本操作”**** ****。

1. 在“脚本操作”页顶部，选择“+ 提交新项” 。

    ![将脚本添加到正在运行的群集](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. 使用“选择脚本”条目选择预制的脚本。____ 若要使用自定义脚本，请选择“自定义”____。 然后提供脚本的“名称”和“Bash 脚本 URI”。____ ____

    ![在“选择脚本”窗体中添加脚本](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    下表描述窗体中的元素：

    | 属性 | Value |
    | --- | --- |
    | 选择脚本 | 要使用自己的脚本，请选择“自定义”____。 否则，请选择提供的脚本。 |
    | 名称 |指定脚本操作的名称。 |
    | Bash 脚本 URI |指定脚本的 URI。 |
    | 头节点/辅助角色/Zookeeper |指定运行脚本的节点：“头节点”、“工作节点”或“ZooKeeper 节点”。**** **** **** |
    | parameters |根据脚本的需要，请指定参数。 |

    使用“持久保存此脚本操作”条目，确保在缩放操作中应用了脚本____。

1. 最后，选择“创建”按钮将脚本应用到群集。

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>从 Azure PowerShell 将脚本操作应用到正在运行的群集

若要使用这些 PowerShell 命令，需要 [AZ 模块](https://docs.microsoft.com/powershell/azure/)。 以下示例演示如何将脚本操作应用于正在运行的群集：

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

操作完成后，会收到类似于以下文本的信息：

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>从 Azure CLI 将脚本操作应用到正在运行的群集

开始之前，请确保安装并配置 Azure CLI。 确保安装了最新版本。 有关详细信息，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 对 Azure 订阅进行身份验证：

    ```azurecli
    az login
    ```

1. 将脚本操作应用到正在运行的群集：

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    有效角色为 `headnode`、`workernode`、`zookeepernode`、`edgenode`。 如果应将脚本应用到多个节点类型，请用空格分隔角色。 例如，`--roles headnode workernode`。

    若要持久保存脚本，请添加 `--persist-on-success`。 以后也可以使用 `az hdinsight script-action promote` 来持久保存脚本。

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>使用 REST API 将脚本操作应用到正在运行的群集

请参阅 [Azure HDInsight 中的群集 REST API](https://msdn.microsoft.com/library/azure/mt668441.aspx)。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>从 HDInsight .NET SDK 将脚本操作应用到正在运行的群集

有关使用 .NET SDK 将脚本应用到群集的示例，请参阅[针对正在运行的基于 Linux 的 HDInsight 群集应用脚本操作](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)。

## <a name="view-history-and-promote-and-demote-script-actions"></a>查看历史记录以及升级和降级脚本操作

### <a name="the-azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)并找到群集。

1. 在默认视图中的“设置”下，选择“脚本操作”**** ****。

1. 此群集的脚本历史记录显示在“脚本操作”部分。 此信息包括持久化脚本的列表。 以下屏幕截图显示已在此群集上运行了 Solr 脚本。 该屏幕截图未显示任何持久化脚本。

    ![门户脚本操作提交历史记录](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. 选择历史记录中的脚本会显示此脚本的“属性”部分。**** 从屏幕顶部，可重新运行脚本或将它升级。

    ![脚本操作属性升级](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. 还可以使用“脚本操作”部分条目右侧的省略号“...”**** 来执行操作。

    ![永久性脚本操作删除](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | 函数 |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |检索有关持久化脚本操作的信息。 此 cmdlet 不会撤消脚本执行的操作，而只会删除持久化标志。|
| `Get-AzHDInsightScriptActionHistory` |检索已应用到群集的脚本操作的历史记录，或特定脚本的详细信息。 |
| `Set-AzHDInsightPersistedScriptAction` |将 `ad hoc` 脚本操作升级为持久化脚本操作。 |
| `Remove-AzHDInsightPersistedScriptAction` |将持久化脚本操作降级为 `ad hoc` 操作。 |

以下示例脚本演示如何使用 cmdlet 来升级再降级脚本。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| 命令 | 说明 |
| --- | --- |
| [`az hdinsight script-action delete`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |删除对群集的指定持久化脚本操作。 此命令不会撤消脚本执行的操作，而只会删除持久化标志。|
|[`az hdinsight script-action execute`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|在指定的 HDInsight 群集上执行脚本操作。|
| [`az hdinsight script-action list`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |列出指定群集的所有持久化脚本操作。 |
|[`az hdinsight script-action list-execution-history`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|列出指定群集的所有脚本的执行历史记录。|
|[`az hdinsight script-action promote`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|将指定的即席脚本执行升级为持久化脚本。|
|[`az hdinsight script-action show-execution-details`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|获取给定脚本执行 ID 的脚本执行详细信息。|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

有关使用 .NET SDK 从群集中检索脚本历史记录、升级或降级脚本的示例，请参阅[针对正在运行的基于 Linux 的 HDInsight 群集应用脚本操作](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)。

> [!NOTE]  
> 此示例还演示了如何使用 .NET SDK 安装 HDInsight 应用程序。

## <a name="next-steps"></a>后续步骤

* [为 HDInsight 开发脚本操作脚本](hdinsight-hadoop-script-actions-linux.md)
* [将其他存储添加到 HDInsight 群集中](hdinsight-hadoop-add-storage.md)
* [排查脚本操作问题](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "群集创建期间的阶段"
