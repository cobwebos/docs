---
title: 使用 Azure Data Box 将数据从本地 HDFS 存储到 Azure 存储
description: 将数据从本地 HDFS 存储迁移到 Azure 存储
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595403"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>使用 Azure Data Box 将数据从本地 HDFS 存储迁移到 Azure 存储

通过使用 Data Box 设备，可以从本地 HDFS 存储到 Azure 存储 （blob 存储或数据湖存储第 2 代） Hadoop 群集的迁移数据。 您可以选择从 80 TB 的数据框或 770 TB 数据框过大。

本文可帮助你完成这些任务：

> [!div class="checklist"]
> * 准备迁移你的数据。
> * 将数据复制到数据框或数据框大量的设备。
> * 将设备发回 Microsoft。
> * 将移动到数据湖存储第 2 代上的数据。

## <a name="prerequisites"></a>先决条件

您需要以下操作来完成迁移。

* 两个存储帐户;一个具有分层命名空间上，启用，不一。

* 包含源数据的本地 Hadoop 群集。

* [Azure Data Box 设备](https://azure.microsoft.com/services/storage/databox/)。

  * [订购 Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered)或[数据框繁重](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)。 时排序你的设备，请务必选择存储帐户**不**具有分层命名空间对其启用。 这是因为 Data Box 设备尚不支持直接引入到 Azure 数据湖存储第 2 代。 你将需要复制到存储帐户，然后执行到 ADLS 第 2 代帐户的另一个副本。 下面的步骤中提供相关说明。

  * 连接电缆并连接你[Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)或[数据框大量](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up)到本地网络。

如果一切就绪，让我们开始。

## <a name="copy-your-data-to-a-data-box-device"></a>将数据复制到 Data Box 设备

如果你的数据适合于单个 Data Box 设备，然后会将数据复制到 Data Box 设备。 

如果数据大小超出 Data Box 设备的容量，然后使用[可选过程将数据拆分跨多个 Data Box 设备](#appendix-split-data-across-multiple-data-box-devices)，然后执行此步骤。 

要将数据从本地 HDFS 存储复制到 Data Box 设备，将进行一些设置，并使用[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)工具。

请按照下列步骤将通过 REST Api 的 Blob/对象存储的数据复制到 Data Box 设备。 REST API 接口将使设备出现作为 HDFS 存储到群集。

1. 通过 REST 将数据复制之前，标识连接到数据框或数据框高上的 REST 接口的安全性和连接基元。 登录到本地 web UI 的数据框中，并转到**连接和复制**页。 针对 Azure 存储帐户为你的设备，在**访问设置**、 查找和选择**REST**。

    !["连接并复制"页](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 在访问存储帐户和上传数据对话框中，复制**Blob 服务终结点**并**存储帐户密钥**。 从 blob 服务终结点，省略`https://`和尾部反斜杠。

    在这种情况下，终结点是： `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`。 将使用的 URI 的主机部分是： `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`。 有关示例，请参阅如何[连接到基于 http 的 REST](/azure/databox/data-box-deploy-copy-data-via-rest)。 

     !["访问存储帐户和将数据上传"对话框](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 添加终结点和到数据框或数据框大量的节点 IP 地址`/etc/hosts`每个节点上。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    如果为 DNS 使用其他一些机制，则应确保可以解析终结点数据。

4. 设置 shell 变量`azjars`到的位置`hadoop-azure`和`azure-storage`jar 文件。 您可以找到 Hadoop 安装目录下的这些文件。

    若要确定这些文件是否存在，请使用以下命令： `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`。 替换为`<hadoop_install_dir>`占位符替换为 Hadoop 安装的目录的路径。 请务必使用完全限定的路径。

    示例：

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 创建想要用于数据复制的存储容器。 此命令的一部分，你还应指定目标目录。 此时，这可能是虚拟的目标目录。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 替换为`<blob_service_endpoint>`占位符替换为 blob 服务终结点的名称。

    * 替换为`<account_key>`替换你的帐户的访问密钥的占位符。

    * 替换为`<container-name>`与你的容器名称的占位符。

    * 替换为`<destination_directory>`占位符替换你想要复制到你的数据的目录的名称。

6. 运行 list 命令以确保已创建了容器和目录。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * 替换为`<blob_service_endpoint>`占位符替换为 blob 服务终结点的名称。

   * 替换为`<account_key>`替换你的帐户的访问密钥的占位符。

   * 替换为`<container-name>`与你的容器名称的占位符。

7. 到前面创建的容器，可将数据 Hadoop HDFS 复制到数据框 Blob 存储。 如果找不到要复制到的目录，命令会自动创建它。

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 替换为`<blob_service_endpoint>`占位符替换为 blob 服务终结点的名称。

    * 替换为`<account_key>`替换你的帐户的访问密钥的占位符。

    * 替换为`<container-name>`与你的容器名称的占位符。

    * 替换为`<exlusion_filelist_file>`包含排除的文件列表的文件同名的占位符。

    * 替换为`<source_directory>`与包含你想要复制的数据的目录的名称的占位符。

    * 替换为`<destination_directory>`占位符替换你想要复制到你的数据的目录的名称。

    `-libjars`选项用于使`hadoop-azure*.jar`和依赖项`azure-storage*.jar`文件可供`distcp`。 某些群集已发生此问题。

    下面的示例演示如何将`distcp`命令用于将数据复制。

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    若要提高复制速度：

    * 请尝试更改映射器数。 (上面的示例使用`m`= 4 的映射器。)

    * 请尝试在运行多个`distcp`并行。

    * 请记住较大的文件比小型文件的更好地执行。

## <a name="ship-the-data-box-to-microsoft"></a>寄送到 Microsoft 的数据框

按照以下步骤来准备并寄回给 Microsoft Data Box 设备。

1. 首先，[准备将传送你的数据框或数据框大量](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)。

2. 设备准备完成后，下载物料清单文件。 将使用这些 BOM 或清单文件更高版本以验证数据上传到 Azure。

3. 关闭设备，请拔下电缆。

4. 安排 UPS 取件。

    * Data Box 设备，请参阅[交付你的数据框](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

    * 对于数据框大量设备，请参阅[交付数据框大量](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)。

5. Microsoft 会收到你的设备、 连接到数据中心网络并将数据上载到 （与层次结构禁用的命名空间） 指定的存储帐户之后当您放置设备顺序。 验证对物料清单文件将数据上载到 Azure。 现在可以将这些数据移到数据湖存储第 2 代存储帐户。

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>将数据移到 Azure 数据湖存储第 2 代

到 Azure 存储帐户已有的数据。 现在会将数据复制到 Azure Data Lake 存储帐户，并将访问权限应用到文件和目录。

> [!NOTE]
> 如果使用 Azure 数据湖存储第 2 代作为数据存储，则需要此步骤。 如果使用的只是 blob 存储帐户层次结构命名空间不作为数据存储区，可以跳过此部分。

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>将数据复制到 Azure Data Lake 存储 Gen 2 帐户

通过使用 Azure 数据工厂，或通过使用基于 Azure 的 Hadoop 群集，可以将数据复制。

* 若要使用 Azure 数据工厂，请参阅[Azure 数据工厂将数据移到 ADLS 第 2 代](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)。 请确保指定**Azure Blob 存储**作为源。

* 若要使用基于 Azure 的 Hadoop 群集，运行以下 DistCp 命令：

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * 替换`<source_account>`和`<dest_account>`的源和目标存储帐户名称的占位符。

    * 替换`<source_container>`和`<dest_container>`占位符替换源和目标容器的名称。

    * 替换`<source_path>`和`<dest_path>`占位符替换源和目标目录路径。

    * 替换为`<source_account_key>`替换包含的数据的存储帐户的访问密钥的占位符。

    此命令从你的存储帐户复制数据和元数据到数据湖存储第 2 代存储帐户。

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>创建你的 Azure 数据湖存储第 2 代帐户的服务主体

若要创建服务主体，请参阅[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

* 执行该文中[将应用程序分配给角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)部分中的步骤时，请确保将“存储 Blob 数据参与者”  角色分配给服务主体。

* 执行中的步骤时[获取的值以便在登录](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)部分的文章、 应用程序 ID 和客户端密钥值到文本文件。 很快就会需要这些值。

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>生成复制具有任务权限的文件的列表

从本地 Hadoop 群集，运行以下命令：

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

此命令将生成具有任务权限复制的文件的列表。

> [!NOTE]
> 根据在 HDFS 中的文件数，此命令可能需要很长时间才能运行。

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>生成的标识列表并将它们映射到 Azure Active Directory （添加） 标识

1. 下载`copy-acls.py`脚本。 请参阅[下载帮助器脚本并设置边缘节点运行这些](#download-helper-scripts)本文的部分。

2. 运行以下命令来生成的唯一标识列表。

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   此脚本将生成名为的文件`id_map.json`，其中包含需要映射到添加基于标识的标识。

3. 在文本编辑器中打开 `id_map.json` 文件。

4. 对于在文件中显示每个 JSON 对象，更新`target`使用相应的 AAD 用户主体名称 (UPN) 或对象 Id (OID) 的属性映射标识。 完成后，保存文件。 你将需要此文件在下一步。

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>将权限应用到复制的文件和应用标识映射

运行此命令将权限应用到的数据复制到的数据湖存储第 2 代帐户：

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* 将 `<storage-account-name>` 占位符替换为存储帐户的名称。

* 替换为`<container-name>`与你的容器名称的占位符。

* 替换`<application-id>`和`<client-secret>`占位符替换你创建服务主体时收集的应用程序 ID 和客户端机密。

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>附录：将数据拆分跨多个 Data Box 设备

移动数据到 Data Box 设备上的之前，你将需要下载某些帮助器脚本，请确保你的数据进行组织，到 Data Box 设备，使其适合排除任何不必要的文件。

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>下载帮助器脚本并设置运行它们的边缘节点

1. 从本地 Hadoop 群集的头节点或边缘，运行以下命令：

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   此命令克隆 GitHub 存储库，其中包含帮助器脚本。

2. 请确保具有[jq](https://stedolan.github.io/jq/)在本地计算机上安装的包。

   ```bash
   
   sudo apt-get install jq
   ```

3. 安装[请求](http://docs.python-requests.org/en/master/)python 包。

   ```bash
   
   pip install requests
   ```

4. 集上所需的脚本将执行权限。

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>确保你的数据进行组织以适合 Data Box 设备

如果你的数据的大小超过了单个 Data Box 设备的大小，你可以拆分为可以存储到多个 Data Box 设备上的组文件。

如果你的数据未超过单个 Data Box 设备的大小，则可以继续学习下一节。

1. 使用提升的权限运行`generate-file-list`脚本下载的上一节中的指南。

   下面是命令参数的说明：

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

2. 复制生成的文件列出了到 HDFS，以便它们可以访问[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)作业。

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>排除不必要的文件

你将需要从 DisCp 作业中排除某些目录。 例如，排除包含状态信息保留运行的群集的目录。

在计划来启动 DistCp 作业的本地 Hadoop 群集，创建用于指定你想要排除的目录列表中的文件。

以下是一个示例：

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>后续步骤

了解 HDInsight 群集时，数据湖存储第 2 代的工作原理。 请参阅[配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)。
