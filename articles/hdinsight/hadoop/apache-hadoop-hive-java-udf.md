---
title: "将 Java 用户定义函数 (UDF) 与 HDInsight 中的 Hive 配合使用 — Azure | Microsoft Docs"
description: "了解如何创建可用于 Hive 的基于 Java 的用户定义的函数 (UDF)。 此 UDF 示例将表中的文本字符串转换为小写。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: cc6b63f40dd4323ecdf0dfb75c7c8036cbc7b0b7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>在 HDInsight 中通过 Hive 使用 Java UDF

了解如何创建可用于 Hive 的基于 Java 的用户定义的函数 (UDF)。 此示例中的 Java UDF 将表中的文本字符串转换为全小写字符。

## <a name="requirements"></a>要求

* HDInsight 群集 

    > [!IMPORTANT]
    > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

    本文档中的大多数步骤适用于基于 Windows 和 Linux 的群集。 但是，用于将已编译的 UDF 上传到群集并运行的步骤特定于基于 Linux 的群集。 提供可用于基于 Windows 的群集的信息的链接。

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 或更高版本（或类似程序，如 OpenJDK）

* [Apache Maven](http://maven.apache.org/)

* 文本编辑器或 Java IDE

    > [!IMPORTANT]
    > 如果在 Windows 客户端上创建 Python 文件，则必须使用将 LF 用作行尾的编辑器。 如果无法确定编辑器使用的是 LF 还是 CRLF，请参阅[故障排除](#troubleshooting)部分，了解删除 CR 字符的步骤。

## <a name="create-an-example-java-udf"></a>创建 Java UDF 示例 

1. 从命令行中，使用以下命令创建新 Maven 项目：

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > 如果使用 PowerShell，必须将参数用引号引起来。 例如，`mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`。

    此命令创建一个名为 **exampleudf** 的目录，其中包含 Maven 项目。

2. 创建该项目后，删除作为项目的一部分创建的 **exampleudf/src/test** 目录。

3. 打开 **exampleudf/pom.xml**，将现有 `<dependencies>` 条目替换为以下 XML：

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

    这些条目指定了 HDInsight 3.5 中包含的 Hadoop 和 Hive 版本。 可以在 [HDInsight 组件版本控制](../hdinsight-component-versioning.md)文档中找到 HDInsight 提供的 Hadoop 和 Hive 的版本信息。

    在文件末尾的 `</project>` 行之前添加 `<build>` 部分。 本部分应包含以下 XML：

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.5  -->
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
                <version>2.3</version>
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

4. 将 **exampleudf/src/main/java/com/microsoft/examples/App.java** 重命名为 **ExampleUDF.java**，并在编辑器中打开该文件。

5. 将 **ExampleUDF.java** 文件的内容替换为以下代码，并保存该文件。

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

1. 使用以下命令编译和打包 UDF：

    ```bash
    mvn compile package
    ```

    此命令生成 UDF 并将其打包到 `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` 文件。

2. 使用 `scp` 命令将文件复制到 HDInsight 群集。

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    将 `myuser` 替换为群集的 SSH 用户帐户。 将 `mycluster` 替换为群集名称。 如果使用密码保护 SSH 帐户，系统会提示输入该密码。 如果使用了证书，则可能需要使用 `-i` 参数指定私钥文件。

3. 使用 SSH 连接到群集。

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    有关详细信息，请参阅 [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

4. 从 SSH 会话中，将 jar 文件复制到 HDInsight 存储。

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>在 Hive 中使用 UDF

1. 使用以下命令在 SSH 会话中启动 Beeline 客户端。

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    该命令假定使用默认的群集登录帐户 **admin**。

2. 当到达 `jdbc:hive2://localhost:10001/>` 提示符时，输入以下代码将 UDF 添加到 Hive，并将其作为函数公开。

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > 此示例假定 Azure 存储为群集的默认存储。 如果群集使用 Data Lake Store，请将 `wasb:///` 值更改为 `adl:///`。

3. 使用该 UDF 将从表中检索的值转换为小写字符串。

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    此查询将从表中选择设备平台（Android、Windows、iOS 等），将字符串转换为小写字符串，并显示它们。 显示的输出类似于以下文本：

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>后续步骤

有关使用 Hive 的其他方式，请参阅[将 HDInsight 与 Hive 配合使用](hdinsight-use-hive.md)。

有关 Hive 用户定义函数的详细信息，请参阅 apache.org 网站上的 Hive wiki 的 [Hive 运算符和用户定义函数](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)部分。
