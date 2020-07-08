---
title: 使用 Azure 数据工厂从 HDFS 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从云或本地 HDFS 源复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: 5ec6778e3e00a85a2fa7d43383df5c2ce6c47faa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629503"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 HDFS 服务器复制数据
> [!div class="op_single_selector" title1="选择要使用的数据工厂服务的版本："]
> * [版本 1](v1/data-factory-hdfs-connector.md)
> * [当前版本](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何从 Hadoop 分布式文件系统 (HDFS) 服务器复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持 HDFS 连接器：

- [复制活动](copy-activity-overview.md)和[支持的源和接收器矩阵](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

具体而言，HDFS 连接器支持：

- 使用 Windows (Kerberos) 或匿名身份验证复制文件 。
- 使用 webhdfs 协议或内置 DistCp 支持复制文件 。
- 按原样复制文件，或者通过使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析或生成文件来复制文件。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> 请确保集成运行时可以访问 Hadoop 群集的所有 [名称节点服务器]:[名称节点端口] 和 [数据节点服务器]:[数据节点端口]。 默认 [名称节点端口] 为 50070，默认 [数据节点端口] 为 50075。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 HDFS 的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

HDFS 链接服务支持以下属性：

| Property | 描述 | 必需 |
|:--- |:--- |:--- |
| type | *type* 属性必须设置为 *Hdfs*。 | 是 |
| url |HDFS 的 URL |是 |
| authenticationType | 允许的值为 Anonymous 或 Windows 。 <br><br> 若要设置本地环境，请参阅[对 HDFS 连接器使用 Kerberos 身份验证](#use-kerberos-authentication-for-the-hdfs-connector)部分。 |是 |
| userName |Windows 身份验证的用户名。 对于 Kerberos 身份验证，请指定 **\<username>@\<domain>.com**。 |是（对于 Windows 身份验证） |
| password |Windows 身份验证的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是（对于 Windows 身份验证） |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 若要了解详细信息，请参阅[先决条件](#prerequisites)部分。 如果未指定集成运行时，服务会使用默认的 Azure Integration Runtime。 |否 |

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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅 [Azure 数据工厂中的数据集](concepts-datasets-linked-services.md)。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

HTTP 支持基于格式的数据集中 `location` 设置下的以下属性：

| Property   | 描述                                                  | 必需 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 type 属性必须设置为 HdfsLocation。 | 是      |
| folderPath | 文件夹的路径。 如果要使用通配符来筛选文件夹，请跳过此设置并在活动源设置中指定路径。 | 否       |
| fileName   | 指定的 folderPath 下的文件名。 如果要使用通配符来筛选文件，请跳过此设置并在活动源设置中指定文件名。 | 否       |

**示例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅 [Azure 数据工厂中的管道和活动](concepts-pipelines-activities.md)。 本部分提供 HDFS 源支持的属性列表。

### <a name="hdfs-as-source"></a>以 HDFS 作为源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

HDFS 支持基于格式的复制源中 `storeSettings` 设置下的以下属性：

| 属性                 | 描述                                                  | 必需                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 *type* 属性必须设置为 **HdfsReadSettings**。 | 是                                           |
| 找到要复制的文件 |  |  |
| 选项 1：静态路径<br> | 从数据集中指定的文件夹或文件路径复制。 若要复制文件夹中的所有文件，请另外将 `wildcardFileName` 指定为 `*`。 |  |
| 选项 2：通配符<br>- wildcardFolderPath | 带有通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果实际文件夹名内具有通配符或此转义字符，请使用 `^` 进行转义。 <br>如需更多示例，请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)。 | 否                                            |
| 选项 2：通配符<br>- wildcardFileName | 指定的 folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为 `*`（匹配零个或零个以上的字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。  如需更多示例，请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)。 | 是 |
| 选项 3：文件列表<br>- fileListPath | 表示要复制指定文件集。 指向一个文本文件，其中包含要复制的文件列表（每行一个文件，带有数据集中所配置路径的相对路径）。<br/>使用此选项时，请不要在数据集中指定文件名。 如需更多示例，请参阅[文件列表示例](#file-list-examples)。 |否 |
| ***其他设置*** |  | |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 `recursive` 设置为 *true* 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 <br>允许的值为 *true*（默认值）和 *false*。<br>如果你配置了 `fileListPath`，则此属性不适用。 |否 |
| modifiedDatetimeStart    | 文件根据“上次修改时间”属性进行筛选。 <br>如果文件的上次修改时间在 `modifiedDatetimeStart` 到 `modifiedDatetimeEnd` 之间的范围内，则会选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。<br/>如果配置 `fileListPath`，则此属性不适用。 | 否                                            |
| maxConcurrentConnections | 可以同时连接到存储区存储的连接数。 仅在要限制与数据存储的并发连接时指定一个值。 | 否                                            |
| DistCp 设置 |  | |
| distcpSettings | 使用 HDFS DistCp 时将使用的属性组。 | 否 |
| resourceManagerEndpoint | YARN (Yet Another Resource Negotiator) 终结点 | 是（如果使用 DistCp） |
| tempScriptPath | 用于存储临时 DistCp 命令脚本的文件夹路径。 脚本文件由数据工厂生成，将在复制作业完成后删除。 | 是（如果使用 DistCp） |
| distcpOptions | 提供给 DistCp 命令的其他选项。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍了对文件夹路径和文件名使用通配符筛选器时产生的行为。

| folderPath | fileName             | recursive | 源文件夹结构和筛选器结果（用**粗体**表示的文件已检索） |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | （为空，使用默认值） | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | （为空，使用默认值） | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>文件列表示例

本部分介绍了在复制活动源中使用文件列表路径时产生的行为。 假设有以下源文件夹结构，并且要复制粗体类型的文件：

| 示例源结构                                      | FileListToCopy.txt 中的内容                             | Azure 数据工厂配置                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;元数据<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在数据集中：**<br>- 文件夹路径：`root/FolderA`<br><br>**在复制活动源中：**<br>- 文件列表路径：`root/Metadata/FileListToCopy.txt` <br><br>文件列表路径指向同一数据存储中的一个文本文件，该文件包含要复制的文件列表（每行一个文件，带有数据集中所配置路径的相对路径）。 |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>使用 DistCp 从 HDFS 复制数据

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) 是 Hadoop 本机命令行工具，用于在 Hadoop 群集中进行分布式复制。 在 Distcp 中运行某个命令时，该命令首先列出要复制的所有文件，然后在 Hadoop 群集中创建多个 Map 作业。 每个 Map 作业会将数据以二进制格式从源复制到接收器。

复制活动支持使用 DistCp 将文件复制到 Azure Blob 存储（包括[暂存复制](copy-activity-performance.md)）或 azure data lake store。 在这种情况下，DistCp 可以利用群集的功能，而不必在自承载集成运行时上运行。 使用 DistCp 可以提供更高的复制吞吐量，尤其是在群集非常强大的情况下。 根据数据工厂中的配置，复制活动会自动构造 DistCp 命令，将其提交到 Hadoop 群集并监视复制状态。

### <a name="prerequisites"></a>先决条件

若要使用 DistCp 将文件从 HDFS 复制到 Azure Blob 存储（包括暂存复制）或 Azure data lake store，请确保 Hadoop 群集满足以下要求：

* 启用了 MapReduce 和 YARN 服务。  
* YARN 版本为 2.5 或更高版本。  
* HDFS 服务器与目标数据存储集成： Azure Blob 存储或 Azure data lake store：  

    - 从 Hadoop 2.7 起，为 Azure Blob FileSystem 提供本机支持。 只需在 Hadoop 环境配置中指定 JAR 路径即可。
    - 从 Hadoop 3.0.0-alpha1 开始，包中附含 Azure Data Lake Store FileSystem。 如果你的 Hadoop 群集版本早于该版本，则需要手动将 Azure Data Lake Storage Gen2 相关 JAR 包（azure-datalake-store）从[此处](https://hadoop.apache.org/releases.html)导入到群集中，并在 Hadoop 环境配置中指定 JAR 文件路径。

* 在 HDFS 中准备临时文件夹。 此临时文件夹用于存储 DistCp shell 脚本，因此会占用 KB 级的空间。
* 确保 HDFS 链接服务中提供的用户帐户具有以下权限：
   * 在 YARN 中提交应用程序。
   * 在临时文件夹下创建一个子文件夹和读/写文件。

### <a name="configurations"></a>配置

如需与 DistCp 相关的配置和示例，请参阅[以 HDFS 作为源](#hdfs-as-source)部分。

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>对 HDFS 连接器使用 Kerberos 身份验证

可以通过两个选项来设置本地环境，以便对 HDFS 连接器使用 Kerberos 身份验证。 你可以选择更适合自己情况的选项。
* 选项 1：[在 Kerberos 领域中加入自承载集成运行时计算机](#kerberos-join-realm)
* 选项 2：[启用 Windows 域和 Kerberos 领域之间的相互信任](#kerberos-mutual-trust)

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>方法 1：在 Kerberos 领域中加入自承载集成运行时计算机

#### <a name="requirements"></a>要求

* 需将自承载集成运行时计算机加入 Kerberos 领域，而不能将其加入任何 Windows 域。

#### <a name="how-to-configure"></a>配置方式

**在自承载集成运行时计算机上：**

1.  运行 Ksetup 实用工具来配置 Kerberos 密钥分发中心 (KDC) 服务器和领域。

    由于 Kerberos 领域不同于 Windows 域，因此计算机必须配置为工作组的成员。 可运行以下命令来设置 Kerberos 领域和添加 KDC 服务器，以便实现此配置。 将 REALM.COM 替换为你自己的领域名。

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    运行上述命令后，重启计算机。

2.  使用 `Ksetup` 命令验证配置。 输出应如下所示：

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**在数据工厂中：**

* 将 Windows 身份验证与 Kerberos 主体名称及密码一起使用来配置 HDFS 连接器，以连接到 HDFS 数据源。 有关配置详细信息，请查看 [HDFS 链接服务属性](#linked-service-properties)部分。

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>选项 2：启用 Windows 域和 Kerberos 领域之间的相互信任

#### <a name="requirements"></a>要求

*   自承载集成运行时计算机必须加入 Windows 域。
*   需要用于更新域控制器设置的权限。

#### <a name="how-to-configure"></a>配置方式

> [!NOTE]
> 将以下教程中的 REALM.COM 和 AD.COM 替换为你自己的领域名和域控制器。

**在 KDC 服务器上：**

1. 编辑 krb5.conf 文件中的 KDC 配置，通过引用以下配置模板，让 KDC 信任 Windows 域。 默认情况下，配置位于 /etc/krb5.conf。

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

   在配置此文件后，重启 KDC 服务。

2. 使用以下命令准备 KDC 服务器中名为 krbtgt/REALM.COM\@AD.COM 的主体：

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. 在 *hadoop.security.auth_to_local* HDFS 服务配置文件中，添加 `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`。

**在域控制器上：**

1.  运行以下 `Ksetup` 命令以添加领域条目：

        C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
        C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  建立从 Windows 域到 Kerberos 领域的信任。 [password] 是主体 *krbtgt/REALM.COM\@AD.COM* 的密码。

        C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]

3.  选择在 Kerberos 中使用的加密算法。

    a. 选择“服务器管理器” > “组策略管理” > “域” > “组策略对象” > “默认或活动的域策略”，然后选择“编辑”。     

    b. 在“组策略管理编辑器”窗格中，选择“计算机配置” > “策略” > “Windows 设置” > “安全设置” > “本地策略” > “安全选项”，然后配置“网络安全:       配置 Kerberos 允许使用的加密类型”。

    c. 选择需要在连接到 KDC 服务器时使用的加密算法。 可以选择所有选项。

    ![“网络安全:配置 Kerberos 允许的加密类型”窗格的屏幕截图](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. 使用 `Ksetup` 命令可指定要在指定领域使用的加密算法。

        C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  创建域帐户和 Kerberos 主体之间的映射，以便在 Windows 域中使用 Kerberos 主体。

    a. 选择“管理工具” > “Active Directory 用户和计算机” 。

    b. 通过选择“视图” > “高级功能”，配置高级功能 。

    c. 在“高级功能”窗格中，右键单击要创建映射的帐户，然后在“名称映射”窗格中选择“Kerberos 名称”选项卡  。

    d. 从领域中添加主体。

       !["安全标识映射" 窗格](media/connector-hdfs/map-security-identity.png)

**在自承载集成运行时计算机上：**

* 运行以下 `Ksetup` 命令以添加领域条目。

        C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
        C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**在数据工厂中：**

* 将 Windows 身份验证与域帐户或 Kerberos 主体一起使用来配置 HDFS 连接器，以连接到 HDFS 数据源。 有关配置详细信息，请参阅 [HDFS 链接服务属性](#linked-service-properties)部分。

## <a name="lookup-activity-properties"></a>查找活动属性

有关查找活动属性的信息，请参阅 [Azure 数据工厂中的查找活动](control-flow-lookup-activity.md)。

## <a name="legacy-models"></a>旧模型

>[!NOTE]
>仍会按原样支持以下模型，以实现后向兼容性。 建议你使用前面讨论的新模型，因为 Azure 数据工厂创作 UI 已切换到生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| properties | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 FileShare |是 |
| folderPath | 文件夹的路径。 支持通配符筛选器。 允许的通配符为 `*`（匹配零个或零个以上的字符）和 `?`（匹配零个或单个字符）；如果实际文件名中包含通配符或此转义字符，请使用 `^` 进行转义。 <br/><br/>示例：“rootfolder/subfolder/”，请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |是 |
| fileName |  指定的“folderPath”下的文件的名称或通配符筛选器。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为 `*`（匹配零个或零个以上的字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件夹名内具有通配符或此转义字符，请使用 `^` 进行转义。 |否 |
| modifiedDatetimeStart | 文件根据“上次修改时间”属性进行筛选。 如果文件的上次修改时间在 `modifiedDatetimeStart` 到 `modifiedDatetimeEnd` 之间的范围内，则会选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要对大量文件应用文件筛选器时，启用此设置会影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 文件根据“上次修改时间”属性进行筛选。 如果文件的上次修改时间在 `modifiedDatetimeStart` 到 `modifiedDatetimeEnd` 之间的范围内，则会选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要对大量文件应用文件筛选器时，启用此设置会影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>若要分析具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat、ParquetFormat    。 请将格式中的“type”属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[ORC 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型包括：Gzip、Deflate、Bzip2 和 ZipDeflate。<br/>支持的级别为：“最佳”和“最快” 。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>若要复制具有指定名称的单个文件，请使用文件夹部分指定 folderPath 并使用文件名指定 fileName。<br>如需复制文件夹下的文件子集，请指定文件夹部分的 **folderPath** 和通配符筛选器部分的 **fileName**。

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
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

### <a name="legacy-copy-activity-source-model"></a>旧复制活动源模型

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 HdfsSource。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。<br/>允许的值为 *true*（默认值）和 *false*。 | 否 |
| distcpSettings | 使用 HDFS DistCp 时的属性组。 | 否 |
| resourceManagerEndpoint | YARN 资源管理器终结点 | 是（如果使用 DistCp） |
| tempScriptPath | 用于存储临时 DistCp 命令脚本的文件夹路径。 脚本文件由数据工厂生成，将在复制作业完成后删除。 | 是（如果使用 DistCp） |
| distcpOptions | 提供给 DistCp 命令的其他选项。 | 否 |
| maxConcurrentConnections | 可以同时连接到存储区存储的连接数。 仅在要限制与数据存储的并发连接时指定一个值。 | 否 |

**示例：复制活动中使用 DistCp 的 HDFS 源**

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

## <a name="next-steps"></a>后续步骤
有关可供 Azure 数据工厂中的复制活动用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
