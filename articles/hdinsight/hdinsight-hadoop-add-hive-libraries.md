---
title: 群集创建过程中的 Apache Hive 库-Azure HDInsight
description: 了解如何在群集创建过程中将 Apache Hive 库（jar 文件）添加到 HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471017"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>创建 HDInsight 群集时添加自定义 Apache Hive 库

了解如何在 HDInsight 上预加载 [Apache Hive](https://hive.apache.org/) 库。 本文档包含有关在群集创建过程中使用脚本操作预加载库的信息。 使用本文档中的步骤添加的库在 Hive 中全局可用-无需使用 "[添加 JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) " 来加载这些库。

## <a name="how-it-works"></a>工作原理

创建群集时，可以使用脚本操作在创建群集节点时修改群集节点。 本文档中的脚本接受一个参数，即库的位置。 此位置必须位于 Azure 存储帐户中，并且库必须作为 jar 文件存储。

在群集创建过程中，该脚本将枚举文件、将这些文件复制到头节点和工作节点上的 `/usr/lib/customhivelibs/` 目录，然后将它们添加到 `hive.aux.jars.path` 文件中的 `core-site.xml` 属性。 在基于 Linux 的群集中，它还会针对文件位置更新 `hive-env.sh` 文件。

使用本文中的脚本操作，可以在使用**WebHCat**和**HiveServer2**的 Hive 客户端时使用这些库。

## <a name="the-script"></a>脚本

**脚本位置**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>要求

* 这些脚本必须同时应用于“头节点”和“辅助角色节点”。

* 要安装的 jar 必须存储在**单个容器**中的 Azure Blob 存储中。

* 在创建期间，包含 jar 文件的库的存储帐户**必须**链接到 HDInsight 群集。 它必须是默认的存储帐户，或者是通过__存储帐户设置__添加的帐户。

* 必须指定容器的 WASB 路径作为脚本操作的参数。 例如，如果 jar 存储在名为**mystorage**的存储帐户**上名**为 lib 的容器中，则参数将 `wasbs://libs@mystorage.blob.core.windows.net/`。

  > [!NOTE]  
  > 本文档假定已创建存储帐户、blob 容器，并已将文件上传到该容器。
  >
  > 如果尚未创建存储帐户，可以通过 [Azure 门户](https://portal.azure.com)创建该帐户。 然后可以使用实用程序（如 [Azure 存储资源管理器](https://storageexplorer.com/)）在帐户中创建一个容器并将文件上传到该容器。

## <a name="create-a-cluster-using-the-script"></a>使用脚本创建群集。

1. 使用在[Linux 上设置 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)中的步骤开始预配群集，但不要完成预配。 也可以使用 Azure PowerShell 或 HDInsight .NET SDK 来使用此脚本创建群集。 有关使用这些方法的详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。 对于 "Azure 门户"，请从 "**配置 + 定价**" 选项卡中选择 " **+ 添加脚本操作**"。

1. 对于**存储**，如果包含 jar 文件库的存储帐户将不同于用于群集的帐户，请完成**额外的存储帐户**。

1. 对于**脚本操作**，请提供以下信息：

    |属性 |值 |
    |---|---|
    |脚本类型|- Custom|
    |名称|库 |
    |Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |节点类型|Head，辅助角色|
    |参数|输入包含 jar 的容器和存储帐户的 WASB 地址。 例如，`wasbs://libs@mystorage.blob.core.windows.net/` 。|

    > [!NOTE]
    > 对于 Apache Spark 2.1，请使用此 bash 脚本 URI： `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`。

1. 继续按[预配 Linux 上的 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)中所述预配群集。

群集创建完成后，可以使用通过此脚本从 Hive 添加的 jar，而无需使用 `ADD JAR` 语句。

## <a name="next-steps"></a>后续步骤

有关使用 Hive 的详细信息，请参阅[将 Apache Hive 与 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
