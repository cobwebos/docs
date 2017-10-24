---
title: "为 Hadoop 创建 Java MapReduce - Azure HDInsight | Microsoft Docs"
description: "了解如何使用 Apache Maven 创建基于 Java 的 MapReduce 应用程序，并使用 Azure HDInsight 中的 Hadoop 运行它。"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: larryfr
ms.openlocfilehash: 11d63f22204eb2acb530378f53ac72f16a35a4f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>为 HDInsight 上的 Hadoop 开发 Java MapReduce 程序

了解如何使用 Apache Maven 创建基于 Java 的 MapReduce 应用程序，并使用 Azure HDInsight 中的 Hadoop 运行它。

> [!NOTE]
> 此示例最近在 HDInsight 3.6 上进行了测试。

## <a name="prerequisites"></a>先决条件

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 或更高版本（或类似程序，如 OpenJDK）。
    
    > [!NOTE]
    > HDInsight 版本 3.4 及更早版本使用 Java 7。 HDInsight 3.5 及更高版本使用 Java 8。

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>配置开发环境

可以在安装 Java 和 JDK 时设置以下环境变量。 不过，应该检查它们是否存在并且包含系统的正确值。

* `JAVA_HOME` - 应该指向已安装 Java 运行时环境 (JRE) 的目录。 例如，在 OS X、Unix 或 Linux 系统上，它的值应该类似于 `/usr/lib/jvm/java-7-oracle`。 在 Windows 中，它的值类似于 `c:\Program Files (x86)\Java\jre1.7`

* `PATH` - 应该包含以下路径：
  
  * `JAVA_HOME`（或等效的路径）

  * `JAVA_HOME\bin`（或等效的路径）

  * 安装 Maven 的目录

## <a name="create-a-maven-project"></a>创建 Maven 项目

1. 在开发环境中，通过中断会话或命令行将目录更改为要存储此项目的位置。

2. 使用随同 Maven 一起安装的 `mvn` 命令，为项目生成基架。

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > 如果使用 PowerShell，必须将 `-D` 参数用双引号引起来。
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    此命令将使用 `artifactID` 参数指定的名称（此示例中为 **wordcountjava**）创建目录。此目录包含以下项：

   * `pom.xml` - [项目对象模型 (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)，其中包含用于生成项目的信息和配置详细信息。

   * `src` - 包含应用程序的目录。

3. 删除 `src/test/java/org/apache/hadoop/examples/apptest.java` 文件， 因为此示例用不到该文件。

## <a name="add-dependencies"></a>添加依赖项

1. 编辑 `pom.xml` 文件，并在 `<dependencies>` 部分中添加以下文本：
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
   ```

    这会定义具有特定版本（在 &lt;version\> 中列出）的库（在 &lt;artifactId\> 中列出）。 在编译时，会从默认 Maven 存储库下载这些依赖项。 可使用 [Maven 存储库搜索](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar)来查看详细信息。
   
    `<scope>provided</scope>` 告诉 Maven 这些依赖关系不应与此应用程序一起打包，因为它们会在运行时由 HDInsight 群集提供。

    > [!IMPORTANT]
    > 使用的版本应与群集上存在的 Hadoop 版本匹配。 有关版本的详细信息，请参阅 [HDInsight 组件版本控制](hdinsight-component-versioning.md)文档。

2. 将以下内容添加到 `pom.xml` 文件中。 此文本必须位于文件中的 `<project>...</project>` 标记内；例如 `</dependencies>` 和 `</project>` 之间。

   ```xml
    <build>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
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
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.1</version>
            <configuration>
            <source>1.8</source>
            <target>1.8</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    第一个插件配置 [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/)，用于生成 uberjar（有时称为 fatjar），其中包含应用程序所需的依赖项。 它还可以防止在 jar 包中复制许可证，复制许可证在某些系统中可能会导致问题。

    第二个插件配置目标 Java 版本。

    > [!NOTE]
    > HDInsight 3.4 及更早版本使用 Java 7。 HDInsight 3.5 及更高版本使用 Java 8。

3. 保存 `pom.xml` 文件。

## <a name="create-the-mapreduce-application"></a>创建 MapReduce 应用程序

1. 转到 `wordcountjava/src/main/java/org/apache/hadoop/examples` 目录并将 `App.java` 文件重命名为 `WordCount.java`。

2. 在文本编辑器中打开 `WordCount.java` 文件，并将其内容替换为以下文本：
   
    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```
   
    请注意，包名为 `org.apache.hadoop.examples`，类名为 `WordCount`。 提交 MapReduce 作业时，会使用这些名称。

3. 保存文件。

## <a name="build-the-application"></a>构建应用程序

1. 更改为 `wordcountjava` 目录（如果尚未到达此目录）。

2. 使用以下命令生成包含该应用程序的 JAR 文件：

   ```
   mvn clean package
   ```

    该指令会清除任何以前构建的项目，下载任何尚未安装的依赖项，并构建和打包应用程序。

3. 命令完成之后，`wordcountjava/target` 目录包含一个名为 `wordcountjava-1.0-SNAPSHOT.jar` 的文件。
   
   > [!NOTE]
   > `wordcountjava-1.0-SNAPSHOT.jar` 文件是一种 uberjar，其中不仅包含 WordCount 作业，还包含该作业在运行时需要的依赖项。

## <a id="upload"></a>上传该 jar

使用以下命令将该 jar 文件上传到 HDInsight 头节点：

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

此命令会将两个文件从本地系统复制到头节点。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

## <a name="run"></a>在 Hadoop 上运行 MapReduce 作业

1. 使用 SSH 连接到 HDInsight。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 在 SSH 会话中，使用以下命令运行 MapReduce 应用程序：
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    此命令启动 WordCount MapReduce 应用程序。 输入文件是 `/example/data/gutenberg/davinci.txt`，输出目录是 `/example/data/wordcountout`。 输入文件和输出均存储到群集的默认存储中。

3. 作业完成后，使用以下命令查看结果：
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    会收到包含单词和计数的列表，其值类似于以下文本：
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>后续步骤

在本文档中，学习了如何开发 Java MapReduce 作业。 请参阅以下文档，了解使用 HDInsight 的其他方式。

* [将 Hive 与 HDInsight 配合使用][hdinsight-use-hive]
* [将 Pig 与 HDInsight 配合使用][hdinsight-use-pig]
* [将 MapReduce 与 HDInsight 配合使用](hdinsight-use-mapreduce.md)

有关详细信息，另请参阅 [Java 开发人员中心](https://azure.microsoft.com/develop/java/)。

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

