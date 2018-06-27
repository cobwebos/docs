---
title: 使用 Azure 数据工厂从 HDFS 复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从云或本地 HDFS 源复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 2c25bff60adc1f3d462cc6a437eab0d46f9fa413
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287865"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>使用 Azure 数据工厂从 HDFS 复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-hdfs-connector.md)
> * [版本 2 - 预览版](connector-hdfs.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 HDFS 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务第 1 版（已正式推出 (GA)），请参阅 [V1 中的 HDFS 连接器](v1/data-factory-hdfs-connector.md)。


## <a name="supported-capabilities"></a>支持的功能

可以将数据从 HDFS 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 HDFS 连接器支持：

- 使用 Windows (Kerberos) 或匿名身份验证复制文件。
- 使用 webhdfs 协议或内置 DistCp 支持复制文件。
- 按原样复制文件，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析/生成文件。

## <a name="prerequisites"></a>先决条件

若要从不可公开访问的 HDFS 复制数据，需要设置自承载集成运行时。 要了解详细信息，请参阅[自承载集成运行时](concepts-integration-runtime.md)一文。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 HDFS 的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

HDFS 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Hdfs**。 | 是 |
| url |HDFS 的 URL |是 |
| authenticationType | 允许的值为：Anonymous或 Windows。 <br><br> 若要对 HDFS 连接器使用 **Kerberos 身份验证**，请参阅[此部分](#use-kerberos-authentication-for-hdfs-connector)相应地设置本地环境。 |是 |
| userName |Windows 身份验证的用户名。 对于 Kerberos 身份验证，指定 `<username>@<domain>.com`。 |是（对于 Windows 身份验证） |
| password |Windows 身份验证的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是（对于 Windows 身份验证） |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：使用匿名身份验证**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：使用 Windows 身份验证**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 HDFS 数据集支持的属性列表。

要从 HDFS 复制数据，请将数据集的 type 属性设置为“FileShare”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：FileShare |是 |
| folderPath | 文件夹路径。 不支持通配符筛选器。 例如：folder/subfolder/ |是 |
| fileName |  指定“folderPath”下的文件的“名称或通配符筛选器”。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件名内具有通配符或此转义符，请使用 `^` 进行转义。 |否 |
| 格式 | 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>如果需要分析具有特定格式的文件，支持分析以下文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为：**最佳**和**最快**。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>如需复制具有给定名称的单个文件，请指定文件夹部分的 **folderPath** 和文件名部分的 **fileName**。<br>如需复制文件夹下的文件子集，请指定文件夹部分的 **folderPath** 和通配符筛选器部分的 **fileName**。

**示例：**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 HDFS 源支持的属性列表。

### <a name="hdfs-as-source"></a>以 HDFS 作为源

要从 HDFS 复制数据，请将复制活动中的源类型设置为“HdfsSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**HdfsSource** |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制/创建空的文件夹/子文件夹。<br/>允许的值为：true（默认）、false | 否 |
| distcpSettings | 使用 HDFS DistCp 时的属性组。 | 否 |
| resourceManagerEndpoint | Yarn ResourceManager 终结点 | 是（如果使用 DistCp） |
| tempScriptPath | 用于存储临时 DistCp 命令脚本的文件夹路径。 脚本文件由数据工厂生成，并在复制作业完成后删除。 | 是（如果使用 DistCp） |
| distcpOptions | 提供给 DistCp 命令的其他选项。 | 否 |

**示例：复制活动中使用 UNLOAD 的 HDFS 源**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

通过下一节，深入了解如何使用 DistCp 高效地从 HDFS 复制数据。

## <a name="use-distcp-to-copy-data-from-hdfs"></a>使用 DistCp 从 HDFS 复制数据

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) 是 Hadoop 本机命令行工具，用于在 Hadoop 群集中进行分布式复制。 运行 Distcp 命令时，该命令首先会列出所有要复制的文件，创建多个指向 Hadoop 群集的 Map 作业，每个 Map 作业会进行从源到接收器的二进制复制。

复制活动支持使用 DistCp 将文件按原样复制到 Azure Blob（包括[暂存复制](copy-activity-performance.md)）或 Azure Data Lake Store，在这种情况下，它可以充分利用群集的功能，而不必依赖于自承载集成运行时而运行。 它将提供更高的复制吞吐量，尤其是当群集非常强大时。 根据 Azure 数据工厂中的配置，复制活动会自动构造 distcp 命令，提交到 Hadoop 群集，以及监视复制状态。

### <a name="prerequsites"></a>先决条件

要使用 DistCp 将文件按原样从 HDFS 复制到 Azure Blob（包括暂存复制）或 Azure Data Lake Store，请确保 Hadoop 群集满足以下要求：

1. 启用了 MapReduce 和 Yarn 服务。
2. Yarn 版本为 2.5 或更高版本。
3. HDFS 服务器与目标数据存储，即 Azure Blob 或 Azure Data Lake Store 集成：

    - 从 Hadoop 2.7 起，为 Azure Blob FileSystem 提供本机支持。 只需在 Hadoop env config 中指定 jar 路径即可。
    - 从 Hadoop 3.0.0-alpha1 开始，包中附含 Azure Data Lake Store FileSystem。 如果 Hadoop 群集低于该版本，则需要从[此处](https://hadoop.apache.org/releases.html)将与 ADLS 相关的包 (azure-datalake-store.jar) 手动导入到群集，并在 Hadoop env config 中指定 jar 路径。

4. 在 HDFS 中准备临时文件夹。 此临时文件夹用于存储 DistCp shell 脚本，因此会占用 KB 级的空间。
5. 请确保 HDFS 链接的服务中提供的用户帐户具有以下权限：a) 在 Yarn 中提交应用程序；b) 有权在临时文件夹下创建子文件夹，冰有权对其中文件进行读/写操作。

### <a name="configurations"></a>配置

下面是复制活动配置的示例，它使用 DistCp 将数据从 HDFS 复制到 Azure Blob：

**示例：**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>对 HDFS 连接器使用 Kerberos 身份验证

设置本地环境有两个选项，以便在 HDFS 连接器中使用 Kerberos 身份验证。 可以选择更适合情况的选项。
* 选项 1：[在 Kerberos 领域中加入自承载集成运行时计算机](#kerberos-join-realm)
* 选项 2：[启用 Windows 域和 Kerberos 领域之间的相互信任](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>选项 1：在 Kerberos 领域中加入自承载集成运行时计算机

#### <a name="requirements"></a>要求

* 需将自承载集成运行时计算机加入 Kerberos 领域，而不能将其加入任何 Windows 域。

#### <a name="how-to-configure"></a>如何配置

**在自承载集成运行时计算机上：**

1.  运行 **Ksetup** 实用工具以配置 Kerberos KDC 服务器和领域。

    由于 Kerberos 领域不同于 Windows 域，计算机必须配置为工作组的成员。 这可以通过按如下所示设置 Kerberos 领域和添加 KDC 服务器来实现。 根据需要将 *REALM.COM* 替换为自己的领域。

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    执行这 2 个命令后，**重启**计算机。

2.  使用 **Ksetup** 命令验证配置。 输出应如下所示：

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**在 Azure 数据工厂中：**

* 使用 **Windows 身份验证**配置 HDFS 连接器，以及用于连接到 HDFS 数据源的 Kerberos 主体名称和密码。 查看配置详细信息中的 [HDFS 链接服务属性](#linked-service-properties)部分。

### <a name="kerberos-mutual-trust"></a>选项 2：启用 Windows 域和 Kerberos 领域之间的相互信任

#### <a name="requirements"></a>要求

*   自承载集成运行时计算机必须加入 Windows 域。
*   需要可更新域控制器的设置的权限。

#### <a name="how-to-configure"></a>如何配置

> [!NOTE]
> 根据需要将下面教程中的 REALM.COM 和 AD.COM 替换为自己的领域和域控制器。

**在 KDC 服务器上：**

1.  编辑 **krb5.conf** 文件中的 KDC 配置，以便让 KDC 信任引用以下配置模板的 Windows 域。 默认情况下，该配置位于 **/etc/krb5.conf**。

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  配置后**重新启动** KDC 服务。

2.  使用以下命令准备 KDC 服务器中名为 **krbtgt/REALM.COM@AD.COM** 的主体：

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  在 **hadoop.security.auth_to_local** HDFS 服务配置文件中，添加 `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`。

**在域控制器上：**

1.  运行以下 **Ksetup** 命令添加一个领域条目：

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  建立从 Windows 域到 Kerberos 领域的信任关系。 [password] 是主体 **krbtgt/REALM.COM@AD.COM** 的密码。

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  选择在 Kerberos 中使用的加密算法。

    1. 转到“服务器管理器”>“组策略管理”>“域”>“组策略对象”>“默认或活动的域策略”，单击“编辑”。

    2. 在“组策略管理编辑器”弹出窗口中，转到“计算机配置”>“策略”>“Windows 设置”>“安全设置”>“本地策略”>“安全选项”，配置“网络安全: 配置 Kerberos 允许使用的加密类型”。

    3. 选择连接到 KDC 时要使用的加密算法。 通常情况下，可以简单地选择所有选项。

        ![配置 Kerberos 的加密类型](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. 使用 **Ksetup** 命令可指定要在特定领域上使用的加密算法。

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  在域帐户和 Kerberos 主体之间创建映射，以便在 Windows 域中使用 Kerberos 主体。

    1. 启动管理工具 >“Active Directory 用户和计算机”。

    2. 通过单击“查看” > “高级功能”配置高级功能。

    3. 找到要创建映射的帐户，并右键单击以查看“名称映射”> 单击“Kerberos 名称”选项卡。

    4. 从领域中添加主体。

        ![映射安全标识](media/connector-hdfs/map-security-identity.png)

**在自承载集成运行时计算机上：**

* 运行以下 **Ksetup** 命令添加一个领域条目。

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**在 Azure 数据工厂中：**

* 使用 **Windows 身份验证**配置 HDFS 连接器，以及用于连接到 HDFS 数据源的域帐户或 Kerberos 主体。 查看配置详细信息中的 [HDFS 链接服务属性](#linked-service-properties)部分。


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
