---
title: 管理 JAR 依赖项 - Azure HDInsight
description: 本文介绍有关管理 HDInsight 应用程序的 Java 存档 (JAR) 依赖项的最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: e3807f54de672642bfc6454cf38babbf189c210a
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086069"
---
# <a name="jar-dependency-management-best-practices"></a>JAR 依赖项管理最佳做法

安装在 HDInsight 群集上的组件依赖于第三方库。 通常，这些内置组件引用特定版本的通用模块（例如 Guava）。 连同依赖项一起提交某个应用程序时，可能会导致同一模块的不同版本之间发生冲突。 如果先在类路径中引用某个组件版本，内置组件可能会因版本不兼容而引发异常。 但是，如果内置组件先在类路径注入其依赖项，则应用程序可能会引发 `NoSuchMethod` 之类的错误。

为了避免版本冲突，请考虑分装应用程序依赖项。

## <a name="what-does-package-shading-mean"></a>包分装是什么意思？
“分装”是包含和重命名依赖项的一种方式。 它将类重新定位，并重写受影响的字节码和资源，以创建依赖项的专用副本。

## <a name="how-to-shade-a-package"></a>如何将包分装？

### <a name="use-uber-jar"></a>使用 uber-jar
uber-jar 是单个 jar 文件，其中包含应用程序 jar 及其依赖项。 uber-jar 中的依赖项默认不会分装。 在某些情况下，如果其他组件或应用程序引用这些库的不同版本，则可能会导致版本冲突。 为了避免这种情况，可以生成一个 uber-jar 文件并在其中包含一部分（或全部）已分装的依赖项。

### <a name="shade-package-using-maven"></a>使用 Maven 将包分装
Maven 可以生成以 Java 和 Scala 编写的应用程序。 Maven-shade-plugin 可帮助你轻松创建分装的 uber-jar。

以下示例演示一个 `pom.xml` 文件，该文件已更新为使用 maven-shade-plugin 将包分装。  XML 节 `<relocation>…</relocation>` 通过移动相应的 JAR 文件条目并重写受影响的字节码，将类从包 `com.google.guava` 移到包 `com.google.shaded.guava` 中。

更改 `pom.xml`后，可以执行 `mvn package` 来生成分装的 uber-jar。

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>使用 SBT 将包分装
SBT 也是适用于 Scala 和 Java 的生成工具。 SBT 没有类似于 maven-shade-plugin 的分装插件。 可以修改 `build.sbt` 文件将包分装。 

例如，若要分装 `com.google.guava`，可将以下命令添加到 `build.sbt` 文件：

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

然后，可以运行 `sbt clean` 和 `sbt assembly` 来生成分装的 jar 文件。 

## <a name="next-steps"></a>后续步骤

* [使用 HDInsight IntelliJ 工具](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [在 IntelliJ 中为 Spark 创建 Scala Maven 应用程序](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
