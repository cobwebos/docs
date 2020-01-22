---
title: 为 Apache Hadoop 创建 Java MapReduce - Azure HDInsight
description: 了解如何使用 Apache Maven 创建基于 Java 的 MapReduce 应用程序，并使用 Azure HDInsight 中的 Hadoop 运行它。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311948"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>为 HDInsight 上的 Apache Hadoop 开发 Java MapReduce 程序

了解如何使用 Apache Maven 创建基于 Java 的 MapReduce 应用程序，并使用 Azure HDInsight 中的 Apache Hadoop 运行它。

## <a name="prerequisites"></a>必备组件

* [Java 开发人员工具包（JDK）版本 8](https://aka.ms/azure-jdks)。

* 根据 Apache 要求正确[安装](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是 Java 项目的项目生成系统。

## <a name="configure-development-environment"></a>配置开发环境

用于本文的环境是运行 Windows 10 的计算机。 命令在命令提示符下执行，并使用记事本编辑了各种文件。 针对你的环境相应地进行修改。

在命令提示符下，输入以下命令以创建工作环境：

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>创建 Maven 项目

1. 输入以下命令，创建名为**wordcountjava**的 Maven 项目：

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    此命令使用 `artifactID` 参数（在本示例中为**wordcountjava** ）指定的名称创建目录。此目录包含以下项：

    * `pom.xml` - [项目对象模型 (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)，其中包含用于生成项目的信息和配置详细信息。
    * src\main\java\org\apache\hadoop\examples：包含应用程序代码。
    * src\test\java\org\apache\hadoop\examples：包含应用程序的测试。

1. 删除生成的示例代码。 通过输入以下命令来删除生成的测试和应用程序文件 `AppTest.java`，并 `App.java`：

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>更新项目对象模型

有关 pom .xml 文件的完整参考，请参阅 https://maven.apache.org/pom.html 。 通过输入以下命令打开 `pom.xml`：

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>添加依赖项

在 `pom.xml`的 `<dependencies>` 部分中添加以下文本：

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

这会定义具有特定版本（在 &lt;version\> 中列出）的库（在 &lt;artifactId\> 中列出）。 在编译时，会从默认 Maven 存储库下载这些依赖项。 可使用 [Maven 存储库搜索](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar)来查看详细信息。

`<scope>provided</scope>` 告诉 Maven 这些依赖关系不应与此应用程序一起打包，因为它们会在运行时由 HDInsight 群集提供。

> [!IMPORTANT]
> 使用的版本应与群集上存在的 Hadoop 版本匹配。 有关版本的详细信息，请参阅 [HDInsight 组件版本控制](../hdinsight-component-versioning.md)文档。

### <a name="build-configuration"></a>生成配置

Maven 插件允许自定义项目的构建阶段。 此节用于添加插件、资源和其他生成配置选项。

将以下代码添加到 `pom.xml` 文件中，然后保存并关闭该文件。 此文本必须位于文件中的 `<project>...</project>` 标记内，例如 `</dependencies>` 和 `</project>` 之间。

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

本部分配置 Apache Maven 编译器插件和 Apache Maven 阴影插件。 该编译器插件用于编译拓扑。 该阴影插件用于防止在由 Maven 构建的 JAR 程序包中复制许可证。 此插件用于防止 HDInsight 群集在运行时出现“重复的许可证文件”错误。 将 maven-shade-plugin 用于 `ApacheLicenseResourceTransformer` 实现可防止发生此错误。

maven-shade-plugin 还会生成 uber jar，其中包含应用程序所需的所有依赖项。

保存 `pom.xml` 文件。

## <a name="create-the-mapreduce-application"></a>创建 MapReduce 应用程序

1. 输入以下命令以创建并打开 `WordCount.java`的新文件。 在出现提示时选择 **"是"** 以创建新文件。

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. 然后，将以下 java 代码复制并粘贴到新文件中。 然后关闭该文件。

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

## <a name="build-and-package-the-application"></a>生成并打包应用程序

在 `wordcountjava` 目录中，使用以下命令来构建包含应用程序的 JAR 文件：

```cmd
mvn clean package
```

该指令会清除任何以前构建的项目，下载任何尚未安装的依赖项，并构建和打包应用程序。

命令完成之后，`wordcountjava/target` 目录包含一个名为 `wordcountjava-1.0-SNAPSHOT.jar` 的文件。

> [!NOTE]
> `wordcountjava-1.0-SNAPSHOT.jar` 文件是一种 uberjar，其中不仅包含 WordCount 作业，还包含该作业在运行时需要的依赖项。

## <a name="upload-the-jar-and-run-jobs-ssh"></a>上传 JAR 并运行作业 (SSH)

以下步骤使用 `scp` 将 JAR 复制到 Apache HBase on HDInsight 群集的主要头节点。 然后使用 `ssh` 命令连接到群集并直接在头节点上运行示例。

1. 将 jar 上传到群集。 将 `CLUSTERNAME` 替换为你的 HDInsight 群集名称，然后输入以下命令：

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. 连接到群集。 将 `CLUSTERNAME` 替换为你的 HDInsight 群集名称，然后输入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 在 SSH 会话中，使用以下命令运行 MapReduce 应用程序：

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    此命令启动 WordCount MapReduce 应用程序。 输入文件是 `/example/data/gutenberg/davinci.txt`，输出目录是 `/example/data/wordcountout`。 输入文件和输出均存储到群集的默认存储中。

1. 作业完成后，使用以下命令查看结果：

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    会收到包含单词和计数的列表，其值类似于以下文本：

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>后续步骤

在本文档中，学习了如何开发 Java MapReduce 作业。 请参阅以下文档，了解使用 HDInsight 的其他方式。

* [将 Apache Hive 和 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 配合使用](hdinsight-use-mapreduce.md)
* [Java 开发中心](https://azure.microsoft.com/develop/java/)
