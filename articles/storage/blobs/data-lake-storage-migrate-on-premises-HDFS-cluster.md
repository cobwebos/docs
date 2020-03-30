---
title: 使用 Azure 数据框从上置 HDFS 存储迁移到 Azure 存储
description: 将数据从本地 HDFS 存储迁移到 Azure 存储
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301994"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>使用 Azure 数据框从上置 HDFS 存储迁移到 Azure 存储

通过使用数据盒设备，可以从 Hadoop 群集的本地 HDFS 存储将数据迁移到 Azure 存储（Blob 存储或数据存储第 2 代）。 您可以从 80 TB 数据盒或 770 TB 数据盒中选择重。

本文可帮助您完成以下任务：

> [!div class="checklist"]
> * 准备迁移数据。
> * 将数据复制到数据盒或数据盒重设备。
> * 将设备运回 Microsoft。
> * 对文件和目录应用访问权限（仅限数据存储第 2 代）

## <a name="prerequisites"></a>先决条件

您需要这些内容才能完成迁移。

* 一个 Azure 存储帐户。

* 包含源数据的本地 Hadoop 群集。

* [Azure 数据框设备](https://azure.microsoft.com/services/storage/databox/)。

  * [订购您的数据框](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered)或[数据框重](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)。 

  * 将[数据盒](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)或[数据盒连接到](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up)本地网络。

如果你准备好了，我们开始吧。

## <a name="copy-your-data-to-a-data-box-device"></a>将数据复制到数据盒设备

如果数据适合单个数据盒设备，则将数据复制到数据盒设备。 

如果数据大小超过数据盒设备的容量，请使用[可选过程跨多个数据盒设备拆分数据](#appendix-split-data-across-multiple-data-box-devices)，然后执行此步骤。 

要将数据从本地 HDFS 存储复制到数据盒设备，您需要设置一些内容，然后使用[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)工具。

按照以下步骤将数据通过 Blob/对象存储的 REST API 复制到数据盒设备。 REST API 接口将使设备显示为群集的 HDFS 存储。

1. 在通过 REST 复制数据之前，请标识安全和连接基元以连接到数据盒或数据盒上的 REST 接口。 登录到数据框的本地 Web UI，然后转到 **"连接"和"复制**"页。 针对设备的 Azure 存储帐户，在 **"访问设置**"、"查找"下并选择**REST**。

    !["连接和复制"页面](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 在"访问存储帐户和上载数据对话框中，复制**Blob 服务终结点**和**存储帐户密钥**。 从 blob 服务终结点中，省略`https://`和尾随斜杠。

    在这种情况下，终结点为： `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`。 要使用的 URI 的主机部分是： `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`。 有关示例，请参阅如何通过[http 连接到 REST。](/azure/databox/data-box-deploy-copy-data-via-rest) 

     !["访问存储帐户和上传数据"对话框](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 在每个节点`/etc/hosts`上添加终结点和数据框或数据盒重节点 IP 地址。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    如果您使用的是 DNS 的其他机制，则应确保可以解析数据框终结点。

4. 将 shell`azjars`变量设置为`hadoop-azure`和`azure-storage`jar 文件的位置。 您可以在 Hadoop 安装目录下找到这些文件。

    要确定这些文件是否存在，请使用以下命令： `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`。 将`<hadoop_install_dir>`占位符替换为安装 Hadoop 的目录的路径。 请务必使用完全限定的路径。

    示例：

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 创建要用于数据复制的存储容器。 还应指定目标目录作为此命令的一部分。 此时，这可能是虚拟目标目录。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 将`<blob_service_endpoint>`占位符替换为 blob 服务终结点的名称。

    * 将`<account_key>`占位符替换为帐户的访问密钥。

    * 将`<container-name>`占位符替换为容器的名称。

    * 将`<destination_directory>`占位符替换为要将数据复制到的目录的名称。

6. 运行列表命令以确保已创建容器和目录。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * 将`<blob_service_endpoint>`占位符替换为 blob 服务终结点的名称。

   * 将`<account_key>`占位符替换为帐户的访问密钥。

   * 将`<container-name>`占位符替换为容器的名称。

7. 将数据从 Hadoop HDFS 复制到数据盒 Blob 存储中，复制到之前创建的容器中。 如果未找到要复制到的目录，则命令会自动创建它。

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 将`<blob_service_endpoint>`占位符替换为 blob 服务终结点的名称。

    * 将`<account_key>`占位符替换为帐户的访问密钥。

    * 将`<container-name>`占位符替换为容器的名称。

    * 将`<exlusion_filelist_file>`占位符替换为包含文件排除列表的文件的名称。

    * 将`<source_directory>`占位符替换为包含要复制数据的目录的名称。

    * 将`<destination_directory>`占位符替换为要将数据复制到的目录的名称。

    该`-libjars`选项用于使`hadoop-azure*.jar`和 从属`azure-storage*.jar`文件可供`distcp`使用。 某些群集可能已发生这种情况。

    下面的示例演示如何使用命令`distcp`来复制数据。

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    要提高复制速度，

    * 尝试更改映射器的数量。 （上述示例使用`m`= 4 个映射器。

    * 尝试并行运行`distcp`多个。

    * 请记住，大型文件的性能优于小文件。

## <a name="ship-the-data-box-to-microsoft"></a>将数据框运送到微软

按照以下步骤准备数据框设备并将其运送到 Microsoft。

1. 首先，[准备在数据箱或数据盒中发货。](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)

2. 设备准备完成后，下载 BOM 文件。 稍后将使用这些物料清单或清单文件来验证上载到 Azure 的数据。

3. 关闭设备并卸下电缆。

4. 安排 UPS 取件。

    * 有关数据框设备，请参阅["运送数据框](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)"。

    * 有关数据盒重设备，请参阅[将数据框装出重](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)。

5. Microsoft 收到您的设备后，它将连接到数据中心网络，并将数据上载到您下设备订单时指定的存储帐户。 根据 BOM 文件验证所有数据是否上载到 Azure。 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>对文件和目录应用访问权限（仅限数据存储第 2 代）

您已经将数据放入 Azure 存储帐户中。 现在，您将对文件和目录应用访问权限。

> [!NOTE]
> 仅当使用 Azure 数据湖存储 Gen2 作为数据存储时，才需要此步骤。 如果只使用没有分层命名空间的 Blob 存储帐户作为数据存储，则可以跳过此部分。

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>为 Azure 数据湖存储 Gen2 帐户创建服务主体

要创建服务主体，请参阅[：使用门户创建可以访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

* 执行该文中[将应用程序分配给角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)部分中的步骤时，请确保将“存储 Blob 数据参与者”**** 角色分配给服务主体。

* 在"[获取值"中的步骤进行文章的登录时](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，将应用程序 ID 和客户端机密值保存到文本文件中。 很快就会需要这些值。

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>生成具有其权限的复制文件的列表

从本地 Hadoop 群集运行此命令：

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

此命令生成具有其权限的复制文件的列表。

> [!NOTE]
> 根据 HDFS 中的文件数，此命令可能需要很长时间才能运行。

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>生成标识列表并将其映射到 Azure 活动目录 （ADD） 标识

1. 下载`copy-acls.py`脚本。 请参阅[下载帮助程序脚本并设置边缘节点以运行](#download-helper-scripts)本文部分。

2. 运行此命令以生成唯一标识的列表。

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   此脚本生成一个名为`id_map.json`的文件，该文件包含需要映射到基于 ADD 的标识的标识。

3. 在文本编辑器中打开 `id_map.json` 文件。

4. 对于文件中显示的每个 JSON 对象，使用适当的映射`target`标识更新 AAD 用户主体名称 （UPN） 或 ObjectId （OID） 的属性。 完成后，保存该文件。 在下一步中，您将需要此文件。

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>应用复制文件的权限并应用标识映射

运行此命令以将权限应用于复制到数据存储库 Gen2 帐户的数据：

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* 将 `<storage-account-name>` 占位符替换为存储帐户的名称。

* 将`<container-name>`占位符替换为容器的名称。

* 将`<application-id>`和`<client-secret>`占位符替换为创建服务主体时收集的应用程序 ID 和客户端密钥。

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>附录：跨多个数据框设备拆分数据

在将数据移动到数据盒设备之前，您需要下载一些帮助程序脚本，确保将数据组织起来以适合数据盒设备，并排除任何不必要的文件。

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>下载帮助程序脚本并设置边缘节点以运行它们

1. 从本地 Hadoop 群集的边缘或头节点运行此命令：

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   此命令克隆包含帮助器脚本的 GitHub 存储库。

2. 确保本地计算机上安装了[jq](https://stedolan.github.io/jq/)包。

   ```bash
   
   sudo apt-get install jq
   ```

3. 安装[请求](https://2.python-requests.org/en/master/)python 包。

   ```bash
   
   pip install requests
   ```

4. 设置对所需脚本执行权限的权限。

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>确保将数据组织起来以适合数据盒设备

如果数据的大小超过单个数据盒设备的大小，则可以将文件拆分为组，以便存储到多个数据盒设备上。

如果数据不超过单个数据盒设备的大小，则可以继续下一节。

1. 使用提升的权限，请按照`generate-file-list`上一节中的指导运行下载的脚本。

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

2. 将生成的文件列表复制到 HDFS，以便[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)作业可以访问它们。

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>排除不必要的文件

您需要从 DisCp 作业中排除某些目录。 例如，排除包含使群集保持运行的状态信息的目录。

在计划启动 DistCp 作业的本地 Hadoop 群集上，创建一个文件，指定要排除的目录列表。

下面是一个示例：

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>后续步骤

了解数据存储第 2 代如何与 HDInsight 群集配合使用。 请参阅[将 Azure 数据存储第 2 代与 Azure HDInsight 群集一起](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)使用 。
