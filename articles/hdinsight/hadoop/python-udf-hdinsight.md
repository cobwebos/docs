---
title: "通过 Apache Hive 和 Pig 使用 Python UDF - Azure HDInsight | Microsoft Docs"
description: "了解如何在 HDInsight（Azure 上的 Hadoop 技术堆栈）中通过 Hive 和 Pig 使用 Python 用户定义的函数 (UDF)。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/05/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 002072c8eac37ffb1548b44627ec08e941c96a1d
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>在 HDInsight 中通过 Hive 和 Pig 使用 Python 用户定义的函数 (UDF)

了解如何在 Azure HDInsight 上的 Hadoop 中通过 Apache Hive 和 Pig 使用 Python 用户定义的函数 (UDF)。

## <a name="python"></a>Python on HDInsight

Python2.7 默认安装在 HDInsight 3.0 和更高版本上。 可以结合此 Python 版本使用 Apache Hive 进行流式处理。 流式处理使用 STDOUT 和 STDIN 在 Hive 与 UDF 之间传递数据。

HDInsight 还包含 Jython，后者是用 Java 编写的 Python 实现。 Jython 直接在 Java 虚拟机上运行，不使用流式处理。 将 Python 与 Pig 配合使用时，我们建议将 Jython 用作 Python 解释器。

> [!WARNING]
> 本文档中的步骤基于以下假设： 
>
> * 在本地开发环境中创建 Python 脚本。
> * 通过本地 Bash 会话使用 `scp` 命令或使用提供的 PowerShell 脚本将脚本上传到 HDInsight。
>
> 如果使用 [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) 预览版来操作 HDInsight，则必须：
>
> * 在 Cloud Shell 环境内部创建脚本。
> * 使用 `scp` 将文件从 Cloud Shell 上传到 HDInsight。
> * 在 Cloud Shell 中使用 `ssh` 连接到 HDInsight 并运行示例。

## <a name="hivepython"></a>Hive UDF

可通过 HiveQL `TRANSFORM` 语句将 Python 用作 Hive 中的 UDF。 例如，以下 HiveQL 调用群集的默认 Azure 存储帐户中存储的 `hiveudf.py` 文件。

**基于 Linux 的 HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**基于 Windows 的 HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> 在基于 Windows 的 HDInsight 群集上，`USING` 子句必须指定 python.exe 的完整路径。

下面是本示例执行的操作：

1. 文件开头的 `add file` 语句将 `hiveudf.py` 文件添加到分布式缓存，使群集中的所有节点都可访问该文件。
2. `SELECT TRANSFORM ... USING` 语句从 `hivesampletable` 中选择数据。 它还将 clientid、devicemake 和 devicemodel 值传递到 `hiveudf.py` 脚本。
3. `AS` 子句描述从 `hiveudf.py` 返回的字段。

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>创建 hiveudf.py 文件


在开发环境中，创建名为 `hiveudf.py` 的文本文件。 将以下代码用作该文件的内容：

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

此脚本可执行以下操作：

1. 从 STDIN 读取一行数据。
2. 可以使用 `string.strip(line, "\n ")` 删除尾随的换行符。
3. 执行流式处理时，一个行就包含了所有值，每两个值之间有一个制表符。 因此，`string.split(line, "\t")` 可用于在每个制表符处拆分输入，并只返回字段。
4. 在处理完成后，必须将输出以单行形式写入到 STDOUT，并在每两个字段之间提供一个制表符。 例如，`print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`。
5. `while` 循环会一直重复到无法读取 `line`。

脚本输出是 `devicemake` 和 `devicemodel` 的输入值的连接，并且是连接值的哈希。

有关如何在 HDInsight 群集上运行此示例的信息，请参阅[运行示例](#running)。

## <a name="pigpython"></a>Pig UDF

在整个 `GENERATE` 语句中，Python 脚本可用作 Pig 中的 UDF。 可以使用 Jython 或 C Python 运行脚本。

* Jython 在 JVM 上运行，并且原本就能从 Pig 调用。
* C Python 是外部进程，因此，JVM 上的 Pig 中的数据将发送到 Python 进程中运行的脚本。 Python 脚本的输出将发回到 Pig 中。

若要指定 Python 解释器，请在引用 Python 脚本时使用 `register`。 以下示例将脚本作为 `myfuncs` 注册到 Pig：

* **使用 Jython**：`register '/path/to/pigudf.py' using jython as myfuncs;`
* **使用 C Python**：`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> 使用 Jython 时，pig_jython 文件的路径可以是本地路径或 WASB:// 路径。 但是，使用 C Python 时，必须引用用于提交 Pig 作业的节点的本地文件系统上的文件。

通过注册后，此示例的 Pig Latin 对于两个脚本是相同的：

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

下面是本示例执行的操作：

1. 第一行代码将示例数据文件 `sample.log` 加载到 `LOGS` 中。 它还将每个记录定义为 `chararray`。
2. 第二行代码筛选出所有 null 值，并将操作结果存储在 `LOG` 中。
3. 接下来，它将循环访问 `LOG` 中的记录，并使用 `GENERATE` 来调用作为 `myfuncs` 加载的 Python/Jython 脚本中包含的 `create_structure` 方法。 `LINE` 用于将当前记录传递给函数。
4. 最后，使用 `DUMP` 命令将输出转储到 STDOUT。 在操作完成后，此命令会显示结果。

### <a name="create-the-pigudfpy-file"></a>创建 pigudf.py 文件

在开发环境中，创建名为 `pigudf.py` 的文本文件。 将以下代码用作该文件的内容：

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

在 Pig Latin 示例中，已将 `LINE` 输入定义为 chararray，因为此输入没有一致的架构。 Python 脚本将数据转换成用于输出的一致架构。

1. `@outputSchema` 语句定义返回到 Pig 的数据的格式。 在本例中，该格式为**数据袋**，这是一种 Pig 数据类型。 该数据袋包含以下字段，所有这些字段都是 chararray（字符串）：

   * date - 创建日志条目的日期
   * time - 创建日志条目的时间
   * classname - 为其创建该条目的类名
   * level - 日志级别
   * detail - 日志条目的详细信息

2. 接下来，`def create_structure(input)` 将定义一个函数，以便 Pig 将行项传递到其中。

3. 示例数据 `sample.log` 基本上符合日期、时间、类名、级别和详细信息架构。 但是，它包含以 `*java.lang.Exception*` 开头的几个行。 必须修改这些行，使之与架构匹配。 `if` 语句会检查这些行，然后调整输入数据以将 `*java.lang.Exception*` 字符串移到末尾，使数据与预期的输出架构相一致。

4. 接下来，使用 `split` 命令在前四个空格字符处拆分数据。 输出会分配到 `date`、`time`、`classname`、`level` 和 `detail` 中。

5. 最后，将值返回到 Pig。

数据返回到 Pig 时，其架构与 `@outputSchema` 语句中的定义一致。

## <a name="running"></a>上传并运行示例

> [!IMPORTANT]
> **SSH** 步骤仅适用于基于 Linux 的 HDInsight 群集。 **PowerShell** 步骤适用于基于 Linux 或 Windows 的 HDInsight 群集，但需要 Windows 客户端。

### <a name="ssh"></a>SSH

有关使用 SSH 的详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 使用 `scp` 将文件复制到 HDInsight 群集。 例如，以下命令会将文件复制到名为 **mycluster** 的群集。

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. 使用 SSH 连接到群集。

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    有关详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)文档。

3. 从 SSH 会话将前面上传的 python 文件添加到群集的 WASB 存储中。

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

在上传文件后，使用以下步骤来运行 Hive 和 Pig 作业。

#### <a name="use-the-hive-udf"></a>使用 Hive UDF

1. 要连接到 Hive，请使用以下命令：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    此命令启动 Beeline 客户端。

2. 在 `0: jdbc:hive2://headnodehost:10001/>` 提示符下输入以下查询：

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. 在输入最后一行后，该作业应该启动。 作业完成后，其返回的输出类似于以下示例：

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. 要退出 Beeline，请使用以下命令：

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>使用 Pig UDF

1. 要连接到 Pig，请使用以下命令：

    ```bash
    pig
    ```

2. 在 `grunt>` 提示符下输入以下语句：

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. 在输入以下行后，作业应会启动。 作业完成后，其返回的输出类似于以下数据：

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. 使用 `quit` 退出 Grunt shell，并在本地文件系统上使用以下命令编辑 pigudf.py 文件：

    ```bash
    nano pigudf.py
    ```

5. 进入编辑器后，通过删除行开头的 `#` 字符来取消注释以下行：

    ```bash
    #from pig_util import outputSchema
    ```

    此行会修改 Python 脚本以使用 C Python 而不是 Jython。 更改后，请使用 Ctrl+X 退出编辑器。 选择 Y，然后选择 Enter 保存更改。

6. 使用 `pig` 命令再次启动 shell。 在 `grunt>` 提示符下，使用以下命令运行带有 Jython 解释器的 Python 脚本。

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    完成此作业后，看到的输出应该与之前使用 Jython 运行脚本后的输出相同。

### <a name="powershell-upload-the-files"></a>PowerShell：上传文件

可以使用 PowerShell 将文件上传到 HDInsight 服务器。 使用以下脚本上传 Python 文件：

> [!IMPORTANT] 
> 本部分中的步骤使用 Azure PowerShell。 有关如何使用 Azure PowerShell 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> 将 `C:\path\to` 值更改为开发环境中的文件路径。

此脚本将检索 HDInsight 群集的信息，然后提取默认存储帐户的名称和密钥，并将文件上传到容器的根目录。

> [!NOTE]
> 有关上传文件的详细信息，请参阅[在 HDInsight 中上传 Hadoop 作业的数据](../hdinsight-upload-data.md)文档。

#### <a name="powershell-use-the-hive-udf"></a>PowerShell：使用 Hive UDF

也可以使用 PowerShell 远程运行 Hive 查询。 使用以下 PowerShell 脚本来运行使用 **hiveudf.py** 脚本的 Hive 查询：

> [!IMPORTANT]
> 在运行之前，该脚本会提示输入 HDInsight 群集的 HTTPs/管理员帐户信息。

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

**Hive** 作业的输出应该如以下示例所示：

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

也可以使用 PowerShell 运行 Pig Latin 作业。 若要运行使用 **pigudf.py** 脚本的 Pig Latin 作业，请使用以下 PowerShell 脚本：

> [!NOTE]
> 使用 PowerShell 远程提交作业时，无法使用 C Python 作为解释器。

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

**Pig** 作业的输出应类似于以下数据：

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>故障排除

### <a name="errors-when-running-jobs"></a>运行作业时出现错误

运行 Hive 作业时，可能会遇到类似于以下文本的错误：

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

此问题可能是由 Python 文件中的行尾结束符号导致的。 许多 Windows 编辑器默认为使用 CRLF 作为行尾结束符号，但 Linux 应用程序通常应使用 LF。

可以使用 PowerShell 语句删除 CR 字符，然后再将文件上传到 HDInsight：

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell 脚本

用于运行示例的两个示例 PowerShell 脚本都包含一个带注释的行，该行显示作业的错误输出。 如果未看到作业的预期输出，请取消注释以下行，并查看错误信息中是否指明了问题。

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

错误信息 (STDERR) 和作业的结果 (STDOUT) 也记录到 HDInsight 存储。

| 对于此作业... | 在 Blob 容器中查看这些文件 |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>后续步骤

如果需要加载默认情况下未提供的 Python 模块，请参阅[如何将模块部署到 Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)。

若要了解使用 Pig、Hive 的其他方式以及如何使用 MapReduce，请参阅以下文档：

* [将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hdinsight-use-mapreduce.md)
