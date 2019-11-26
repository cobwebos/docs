---
title: Apache Hive Azure HDInsight 的 Java 用户定义函数（UDF）
description: 了解如何创建可用于 Apache Hive 的基于 Java 的用户定义函数 (UDF)。 此 UDF 示例将表中的文本字符串转换为小写。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327212"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>将 Java UDF 与 HDInsight 中的 Apache Hive 配合使用

了解如何创建可用于 Apache Hive 的基于 Java 的用户定义函数 (UDF)。 此示例中的 Java UDF 将表中的文本字符串转换为全小写字符。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。
* [Java 开发人员工具包 (JDK) 版本 8](https://aka.ms/azure-jdks)
* 根据 Apache 要求正确[安装](https://maven.apache.org/download.cgi)的 [Apache Maven](https://maven.apache.org/install.html)。  Maven 是 Java 项目的项目生成系统。
* 群集主存储的 [URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 对于 Azure 存储，此值为 wasb://；对于Azure Data Lake Storage Gen2，此值为 abfs://；对于 Azure Data Lake Storage Gen1，此值为 adl://。 如果为 Azure 存储启用安全传输，则 URI 将为 `wasbs://`。  另请参阅[安全传输](../../storage/common/storage-require-secure-transfer.md)。

* 文本编辑器或 Java IDE

    > [!IMPORTANT]  
    > 如果在 Windows 客户端上创建 Python 文件，则必须使用将 LF 用作行尾的编辑器。 如果无法确定编辑器使用的是 LF 还是 CRLF，请参阅[故障排除](#troubleshooting)部分，了解删除 CR 字符的步骤。

## <a name="test-environment"></a>测试环境

本文使用的环境是一台运行 Windows 10 的计算机。  命令在命令提示符下执行，各种文件使用记事本进行编辑。 针对环境进行相应的修改。

在命令提示符下，输入以下命令以创建工作环境：

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>创建 Java UDF 示例

1. 通过输入以下命令创建一个新的 Maven 项目：

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    此命令创建一个名为 `exampleudf`的目录，其中包含 Maven 项目。

2. 创建项目后，通过输入以下命令删除作为项目的一部分创建的 `exampleudf/src/test` 目录：

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. 输入以下命令打开 `pom.xml`：

    ```cmd
    notepad pom.xml
    ```

    然后，将现有 `<dependencies>` 条目替换为以下 XML：

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    这些条目指定了 HDInsight 3.6 中包含的 Hadoop 和 Hive 版本。 可以在 [HDInsight 组件版本控制](../hdinsight-component-versioning.md)文档中找到 HDInsight 提供的 Hadoop 和 Hive 的版本信息。

    在文件末尾的 `<build>` 行之前添加 `</project>` 部分。 本部分应包含以下 XML：

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    这些条目定义如何生成项目。 具体而言，包括项目使用的 Java 版本以及如何生成 uberjar 以部署到群集。

    一旦进行了更改，请保存该文件。

4. 输入以下命令，以创建并打开新文件 `ExampleUDF.java`：

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    将以下 Java 代码复制并粘贴到新文件中。 然后关闭该文件。

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    该代码将实现接受一个字符串值，并返回该字符串的小写形式的 UDF。

## <a name="build-and-install-the-udf"></a>生成并安装 UDF

在以下命令中，请将 `sshuser` 替换为实际用户名（如果两者不同）。 将 `mycluster` 替换为实际群集名称。

1. 通过输入以下命令编译和打包 UDF：

    ```cmd
    mvn compile package
    ```

    此命令生成 UDF 并将其打包到 `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` 文件。

2. 使用 `scp` 命令通过输入以下命令将文件复制到 HDInsight 群集：

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. 输入以下命令，使用 SSH 连接到群集：

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. 从打开的 SSH 会话中，将 jar 文件复制到 HDInsight 存储。

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>在 Hive 中使用 UDF

1. 通过输入以下命令从 SSH 会话启动 Beeline 客户端：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    该命令假定使用默认的群集登录帐户 **admin**。

2. 当到达 `jdbc:hive2://localhost:10001/>` 提示符时，输入以下代码将 UDF 添加到 Hive，并将其作为函数公开。

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. 使用该 UDF 将从表中检索的值转换为小写字符串。

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    此查询从表中选择状态，将字符串转换为小写形式，然后将其与未修改的名称一起显示。 显示的输出类似于以下文本：

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>故障排除

运行 hive 作业时，可能会遇到类似于以下文本的错误：

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

此问题可能是由 Python 文件中的行尾结束符号导致的。 许多 Windows 编辑器默认为使用 CRLF 作为行尾结束符号，但 Linux 应用程序通常应使用 LF。

可以使用 PowerShell 语句删除 CR 字符，再将文件上载到 HDInsight：

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>后续步骤

有关使用 Hive 的其他方式，请参阅[将 Apache Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)。

有关 Hive 用户定义函数的详细信息，请参阅 apache.org 网站上的 Hive wiki 的 [Apache Hive 运算符和用户定义函数](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)部分。
