---
title: 配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集
description: 了解如何在 Azure HDInsight 群集中使用 Azure Data Lake Storage Gen2。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623155"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集

Azure Data Lake Storage Gen2 是一种专用于在 Azure Blob 存储基础上进行大数据分析的云存储服务。 Data Lake Storage Gen2 结合了 Azure Blob 存储和 Azure Data Lake Storage Gen1 功能。 生成的服务提供了 Azure Data Lake Storage Gen1 中的功能，例如文件系统语义、目录级别和文件级别的安全性、可伸缩性以及低成本、分层存储、高可用性和灾难恢复功能从 Azure Blob 存储。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

Data Lake Storage Gen2 作为存储选项，几乎所有 Azure HDInsight 群集类型都作为默认存储帐户和其他存储帐户。 但 HBase 只能有一个 Data Lake Storage Gen2 帐户。

有关使用 Data Lake Storage Gen2 的群集创建选项的完整比较，请参阅[比较用于 Azure HDInsight 群集的存储选项](hdinsight-hadoop-compare-storage-options.md)。

> [!Note]  
> 选择 "Data Lake Storage Gen2 作为**主存储类型**后，不能选择 Data Lake Storage Gen1 帐户作为附加存储。

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>通过 Azure 门户 Data Lake Storage Gen2 创建群集

若要创建使用存储 Data Lake Storage Gen2 的 HDInsight 群集，请按照以下步骤配置 Data Lake Storage Gen2 帐户。

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

创建用户分配的托管标识（如果还没有）。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左上角单击 "**创建资源**"。
1. 在 "搜索" 框中，键入**user user** ，然后单击 "**用户分配的托管标识**"。
1. 单击“创建”。
1. 输入托管标识的名称，选择正确的订阅、资源组和位置。
1. 单击“创建”。

有关 Azure HDInsight 中托管标识的工作方式的详细信息，请参阅[Azure hdinsight 中的托管标识](hdinsight-managed-identities.md)。

![创建用户分配的托管标识](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>创建 Data Lake Storage Gen2 帐户

创建 Azure Data Lake Storage Gen2 存储帐户。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左上角单击 "**创建资源**"。
1. 在搜索框中，键入 "**存储**"，然后单击 "**存储帐户**"。
1. 单击“创建”。
1. 在 "**创建存储帐户**" 屏幕上：
    1. 选择正确的订阅和资源组。
    1. 输入 Data Lake Storage Gen2 帐户的名称。 有关存储帐户命名约定的详细信息，请参阅[Azure 资源的命名约定](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage)。
    1. 单击 "**高级**" 选项卡。
    1. 在**Data Lake Storage Gen2**下，单击 "**分层命名空间**" 旁边的 "**启用**"。
    1. 单击“查看 + 创建”。
    1. 单击“创建”

有关存储帐户创建过程中的其他选项的详细信息，请参阅[快速入门：创建 Azure Data Lake Storage Gen2 的存储帐户](../storage/blobs/data-lake-storage-quickstart-create-account.md)。

![显示 Azure 门户中存储帐户创建情况的屏幕截图](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>为 Data Lake Storage Gen2 帐户上的托管标识设置权限

将托管标识分配给存储帐户上的**存储 Blob 数据所有者**角色。

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。
1. 选择存储帐户，然后选择 "**访问控制（IAM）** " 以显示该帐户的访问控制设置。 选择“角色分配”选项卡以查看角色分配列表。

    ![显示存储访问控制设置的屏幕截图](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. 选择 " **+ 添加角色分配**" 按钮以添加新角色。
1. 在 "**添加角色分配**" 窗口中，选择 "**存储 Blob 数据所有者**" 角色。 然后，选择具有托管标识和存储帐户的订阅。 接下来，搜索并找到之前创建的用户分配托管标识。 最后，选择托管标识，它将在 "**所选成员**" 下列出。

    ![显示如何分配 RBAC 角色的屏幕截图](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. 选择“保存”。 所选的用户分配的标识现在列在所选角色下。
1. 此初始设置完成后，可通过门户创建群集。 群集必须与存储帐户位于同一 Azure 区域中。 在 "群集创建" 菜单的 "**存储**" 选项卡中，选择以下选项：

    * 对于 "**主存储类型**"，请选择**Azure Data Lake Storage Gen2**。
    * 在 "**主存储帐户**" 下，搜索并选择新创建的 Data Lake Storage Gen2 存储帐户。

    * 在 "**标识**" 下，选择新创建的用户分配的托管标识。

        ![用于配合使用 Data Lake Storage Gen2 和 Azure HDInsight 的存储设置](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * 若要在存储帐户级别添加辅助 Data Lake Storage Gen2 帐户，只需将之前创建的托管标识分配到要添加的新 Data Lake Storage Gen2 存储帐户。 请注意，不支持通过 HDInsight 上的 "其他存储帐户" 边栏选项卡添加辅助 Data Lake Storage Gen2 帐户。
    > * 可以在 HDInsight 使用的 Azure 存储帐户上启用 GRS 或 ZRS。 但是，不支持对 GRS 或 ZRS 辅助终结点创建群集。


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>通过 Azure CLI Data Lake Storage Gen2 创建群集

您可以[下载示例模板文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)并[下载示例参数文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)。 使用以下模板和 Azure CLI 代码片段之前，请将以下占位符替换为正确的值：

| 占位符 | 说明 |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure 订阅的 ID |
| `<RESOURCEGROUPNAME>` | 要在其中创建新群集和存储帐户的资源组。 |
| `<MANAGEDIDENTITYNAME>` | 将获得 Azure Data Lake Storage Gen2 帐户的权限的托管标识的名称。 |
| `<STORAGEACCOUNTNAME>` | 要创建的新 Azure Data Lake Storage Gen2 帐户。 |
| `<CLUSTERNAME>` | HDInsight 群集的名称。 |
| `<PASSWORD>` | 使用 SSH 和 Ambari 仪表板登录到群集所用的密码。 |

下面的代码片段执行以下初始步骤：

1. 登录到 Azure 帐户。
1. 设置将在其中完成创建操作的活动订阅。
1. 为新的部署活动创建新的资源组。
1. 创建用户分配的托管标识。
1. 将扩展添加到 Azure CLI 以使用 Data Lake Storage Gen2 的功能。
1. 使用 `--hierarchical-namespace true` 标志创建新的 Data Lake Storage Gen2 帐户。

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

接下来，登录到门户。 按照[使用 Azure 门户](hdinsight-hadoop-use-data-lake-storage-gen2.md)中的步骤3中所述，将新的用户分配的托管标识添加到存储帐户上的 "**存储 Blob 数据参与者**" 角色。

为用户分配的托管标识分配角色后，请使用以下代码段部署模板。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>通过 Data Lake Storage Gen2 创建群集 Azure PowerShell

当前不支持使用 PowerShell 创建具有 Azure Data Lake Storage Gen2 的 HDInsight 群集。

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 中 Data Lake Storage Gen2 的访问控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支持哪些类型的权限？

Data Lake Storage Gen2 使用支持基于角色的访问控制（RBAC）和类似 POSIX 的访问控制列表（Acl）的访问控制模型。 Data Lake Storage Gen1 仅支持访问控制列表，以便控制对数据的访问。

RBAC 使用角色分配将权限集有效地应用于 Azure 资源的用户、组和服务主体。 通常情况下，这些 Azure 资源限制为顶级资源（例如，Azure 存储帐户）。 对于 Azure 存储，以及 Data Lake Storage Gen2，此机制已扩展到文件系统资源。

 有关使用 RBAC 的文件权限的详细信息，请参阅[Azure 基于角色的访问控制（RBAC）](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)。

有关 Acl 的文件权限的详细信息，请参阅对[文件和目录的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>如何实现在 Data Lake Storage Gen2 中控制对我的数据的访问？

你的 HDInsight 群集能够访问 Data Lake Storage Gen2 中的文件是通过托管标识控制的。 托管标识是在 Azure Active Directory （Azure AD）中注册的标识，其凭据由 Azure 管理。 利用托管标识，无需在 Azure AD 或维护凭据（如证书）中注册服务主体。

Azure 服务具有两种类型的托管标识：系统分配和用户分配。 HDInsight 使用用户分配的托管标识来访问 Data Lake Storage Gen2。 用户分配的托管标识作为独立的 Azure 资源创建。 在创建过程中，Azure 会在由所用订阅信任的 Azure AD 租户中创建一个标识。 在创建标识后，可以将标识分配到一个或多个 Azure 服务实例。

用户分配标识的生命周期与它所分配到的 Azure 服务实例的生命周期是分开管理的。 有关托管标识的详细信息，请参阅[Azure 资源的托管标识如何工作？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>如何实现使用 Hive 或其他服务为 Azure AD 用户设置 Data Lake Storage Gen2 查询数据的权限？

若要设置用户查询数据的权限，请使用 Azure AD 安全组作为 Acl 中分配的主体。 不要直接将文件访问权限分配给单个用户或服务主体。 当你使用 Azure AD 安全组来控制权限流时，你可以添加和删除用户或服务主体，而无需将 Acl 重新应用到整个目录结构。 只需要从相应的 Azure AD 安全组添加或删除用户。 Acl 不会被继承，因此重新应用 Acl 需要在每个文件和子目录上更新 ACL。

## <a name="access-files-from-the-cluster"></a>从群集访问文件

可以通过多种方式从 HDInsight 群集访问 Data Lake Storage Gen2 中的文件。

* **使用完全限定的名称**。 使用此方法时，需要提供要访问的文件的完整路径。

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **使用缩短的路径格式**。 利用此方法，你可以将路径替换为群集根路径，如下所示：

    ```
    abfs:///<file.path>/
    ```

* **使用相对路径**。 使用此方法时，仅需提供要访问的文件的相对路径。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>数据访问示例

示例基于与群集头节点的[ssh 连接](./hdinsight-hadoop-linux-use-ssh-unix.md)。 这些示例使用全部三个 URI 方案。 将 `CONTAINERNAME` 和 `STORAGEACCOUNT` 替换为相关值

#### <a name="a-few-hdfs-commands"></a>几个 hdfs 命令

1. 在本地存储中创建文件。

    ```bash
    touch testFile.txt
    ```

1. 在群集存储上创建目录。

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 将数据从本地存储复制到群集存储。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 列出群集存储中的目录内容。

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>创建 Hive 表

出于说明目的，显示了三个文件位置。 对于实际执行，只使用其中一个 `LOCATION` 条目。

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 与 Data Lake Storage Gen2 预览版集成 - ACL 和安全更新](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)
* [教程：使用 Azure HDInsight 中的交互式查询提取、转换和加载数据](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
