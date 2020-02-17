---
title: 通过 Azure Data Box 从本地 HDFS 存储迁移到 Azure 存储
description: 将数据从本地 HDFS 存储迁移到 Azure 存储
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 990b4afa6bdb63e626be0272553aea408afb864f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368671"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>通过 Azure Data Box 从本地 HDFS 存储迁移到 Azure 存储

可以通过使用 Data Box 设备，将 Hadoop 群集的本地 HDFS 存储中的数据迁移到 Azure 存储（blob 存储或 Data Lake Storage Gen2）。 可以从 80 TB Data Box 或 770-TB Data Box Heavy 中进行选择。

本文将帮助你完成以下任务：

> [!div class="checklist"]
> * 准备迁移数据。
> * 将数据复制到 Data Box 或 Data Box Heavy 设备。
> * 将设备寄回给 Microsoft。
> * 应用对文件和目录的访问权限（仅 Data Lake Storage Gen2）

## <a name="prerequisites"></a>必备条件

完成迁移需要执行以下任务。

* 一个 Azure 存储帐户。

* 包含源数据的本地 Hadoop 群集。

* [Azure Data Box 设备](https://azure.microsoft.com/services/storage/databox/)。

  * [排序 Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered)或[Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)。 

  * 将[Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)或[Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up)连接到本地网络。

如果已准备就绪，请启动。

## <a name="copy-your-data-to-a-data-box-device"></a>将数据复制到 Data Box 设备

如果你的数据适合单个 Data Box 设备，则会将数据复制到 Data Box 设备。 

如果数据大小超出了 Data Box 设备的容量，请使用[可选的过程将数据拆分到多个 Data Box 设备上](#appendix-split-data-across-multiple-data-box-devices)，然后执行此步骤。 

若要将数据从本地 HDFS 存储复制到 Data Box 设备，请设置几项，然后使用[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)工具。

请按照以下步骤操作，将数据通过 Blob/对象存储的 REST Api 复制到 Data Box 设备。 REST API 接口会使设备在群集中显示为 HDFS 存储。

1. 通过 REST 复制数据之前，请确定要连接到 Data Box 或 Data Box Heavy 上的 REST 接口的安全性和连接基元。 登录到 Data Box 的本地 web UI，并中转到 "**连接和复制**" 页面。 根据设备的 Azure 存储帐户，在 "**访问设置**" 下，找到并选择 " **REST**"。

    !["连接和复制" 页面](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 在 "访问存储帐户和上传数据" 对话框中，复制 " **Blob 服务终结点**" 和 "**存储帐户密钥**"。 从 blob 服务终结点中，省略 `https://` 和尾随斜杠。

    在这种情况下，终结点为： `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`。 要使用的 URI 的主机部分为： `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`。 有关示例，请参阅如何[通过 Http 连接到 REST](/azure/databox/data-box-deploy-copy-data-via-rest)。 

     !["访问存储帐户和上传数据" 对话框](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 将终结点和 Data Box 或 Data Box Heavy 节点 IP 地址添加到每个节点上的 `/etc/hosts`。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    如果对 DNS 使用其他某种机制，应确保可以解析 Data Box 终结点。

4. 将 shell 变量 `azjars` 设置为 `hadoop-azure` 和 `azure-storage` jar 文件的位置。 可以在 Hadoop 安装目录下找到这些文件。

    若要确定这些文件是否存在，请使用以下命令： `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`。 将 `<hadoop_install_dir>` 占位符替换为已安装 Hadoop 的目录的路径。 请确保使用完全限定的路径。

    示例：

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 创建要用于数据复制的存储容器。 还应在此命令中指定目标目录。 此时这可能是一个虚拟的目标目录。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 将 `<blob_service_endpoint>` 占位符替换为你的 blob 服务终结点的名称。

    * 将 `<account_key>` 占位符替换为帐户的访问密钥。

    * 将 `<container-name>` 占位符替换为你的容器的名称。

    * 将 `<destination_directory>` 占位符替换为要将数据复制到的目录的名称。

6. 运行 list 命令以确保已创建容器和目录。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * 将 `<blob_service_endpoint>` 占位符替换为你的 blob 服务终结点的名称。

   * 将 `<account_key>` 占位符替换为帐户的访问密钥。

   * 将 `<container-name>` 占位符替换为你的容器的名称。

7. 将数据从 Hadoop HDFS 复制到你之前创建的容器中 Data Box Blob 存储。 如果找不到要复制到的目录，则该命令会自动创建它。

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 将 `<blob_service_endpoint>` 占位符替换为你的 blob 服务终结点的名称。

    * 将 `<account_key>` 占位符替换为帐户的访问密钥。

    * 将 `<container-name>` 占位符替换为你的容器的名称。

    * 将 `<exlusion_filelist_file>` 占位符替换为包含文件排除列表的文件的名称。

    * 将 `<source_directory>` 占位符替换为包含要复制的数据的目录的名称。

    * 将 `<destination_directory>` 占位符替换为要将数据复制到的目录的名称。

    `-libjars` 选项用于使 `hadoop-azure*.jar` 和依赖 `azure-storage*.jar` 文件可用于 `distcp`。 对于某些群集，可能已发生此情况。

    下面的示例演示如何使用 `distcp` 命令来复制数据。

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    提高复制速度：

    * 尝试更改映射器数。 （上面的示例使用 `m` = 4 映射器。）

    * 尝试并行运行多个 `distcp`。

    * 请记住，大型文件的性能比小文件更好。

## <a name="ship-the-data-box-to-microsoft"></a>向 Microsoft 发送 Data Box

按照以下步骤准备 Data Box 设备并将其寄送到 Microsoft。

1. 首先，[准备交付 Data Box 或 Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)。

2. 完成设备准备后，下载 BOM 文件。 稍后将使用这些 BOM 或清单文件来验证上传到 Azure 的数据。

3. 关闭设备并拔下电缆。

4. 安排 UPS 取件。

    * 有关 Data Box 设备，请参阅[发运 Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

    * 有关 Data Box Heavy 设备，请参阅[发运 Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)。

5. Microsoft 收到你的设备后，它将连接到数据中心网络，并且数据将上传到你在你放置设备顺序时指定的存储帐户。 针对所有数据都上载到 Azure 的 BOM 文件进行验证。 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>应用对文件和目录的访问权限（仅 Data Lake Storage Gen2）

已将数据导入到 Azure 存储帐户。 现在，将访问权限应用于文件和目录。

> [!NOTE]
> 仅当使用 Azure Data Lake Storage Gen2 作为数据存储时，才需要执行此步骤。 如果只使用不带分层命名空间的 blob 存储帐户作为数据存储区，则可以跳过此部分。

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>为 Azure Data Lake Storage Gen2 帐户创建服务主体

若要创建服务主体，请参阅[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

* 执行该文中[将应用程序分配给角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)部分中的步骤时，请确保将“存储 Blob 数据参与者”角色分配给服务主体。

* 执行项目的 "[获取值](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)" 部分中的步骤时，将应用程序 ID 和客户端密码值保存到文本文件中。 很快就会需要这些值。

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>生成已复制文件的列表及其权限

在本地 Hadoop 群集中，运行以下命令：

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

此命令生成已复制文件的列表及其权限。

> [!NOTE]
> 运行此命令可能需要很长时间，具体取决于 HDFS 中的文件数。

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>生成标识列表，并将其映射到 Azure Active Directory （添加）标识

1. 下载 `copy-acls.py` 脚本。 请参阅本文中的[下载帮助器脚本并设置边缘节点以运行它们](#download-helper-scripts)。

2. 运行此命令以生成唯一标识的列表。

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   此脚本将生成一个名为 `id_map.json` 的文件，其中包含需要映射到基于添加的标识的标识。

3. 在文本编辑器中打开 `id_map.json` 文件。

4. 对于文件中显示的每个 JSON 对象，使用适当的映射标识更新 AAD 用户主体名称（UPN）或 ObjectId （OID）的 `target` 属性。 完成后，保存该文件。 下一步需要用到此文件。

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>将权限应用于复制的文件和应用标识映射

运行以下命令，对复制到 Data Lake Storage Gen2 帐户的数据应用权限：

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* 将 `<storage-account-name>` 占位符替换为存储帐户的名称。

* 将 `<container-name>` 占位符替换为你的容器的名称。

* 将 `<application-id>` 和 `<client-secret>` 占位符替换为在创建服务主体时收集的应用程序 ID 和客户端机密。

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>附录：跨多个 Data Box 设备拆分数据

将数据移到 Data Box 设备之前，需要下载一些帮助程序脚本，确保将数据组织到 Data Box 设备上，并排除所有不必要的文件。

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>下载帮助程序脚本并设置边缘节点以运行它们

1. 在你的本地 Hadoop 群集的边缘或头节点上运行以下命令：

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   此命令克隆包含 helper 脚本的 GitHub 存储库。

2. 请确保在本地计算机上安装了[jq](https://stedolan.github.io/jq/)包。

   ```bash
   
   sudo apt-get install jq
   ```

3. 安装[请求](https://2.python-requests.org/en/master/)python 包。

   ```bash
   
   pip install requests
   ```

4. 设置所需脚本的执行权限。

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>确保对数据进行组织，使其适合 Data Box 设备

如果数据的大小超出单个 Data Box 设备的大小，则可以将文件拆分成多个组，并将其存储到多个 Data Box 设备上。

如果数据未超出单一 Data Box 设备的大小，则可以转到下一节。

1. 使用提升的权限，按照上一部分中的指导运行下载的 `generate-file-list` 脚本。

   下面是对命令参数的说明：

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. 将生成的文件列表复制到 HDFS，以便[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)作业可以访问这些列表。

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>排除不必要的文件

你需要从 DisCp 作业中排除某些目录。 例如，排除包含保持群集运行的状态信息的目录。

在计划启动 DistCp 作业的本地 Hadoop 群集上，创建一个文件来指定要排除的目录列表。

下面是一个示例：

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>后续步骤

了解 Data Lake Storage Gen2 如何与 HDInsight 群集配合使用。 请参阅[配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)。
