---
title: 管理 JAR 依赖项-Azure HDInsight
description: 本文介绍管理 HDInsight 应用程序的 Java 存档（JAR）依赖项的最佳实践。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135728"
---
# <a name="jar-dependency-management-best-practices"></a>JAR 依赖项管理最佳做法

安装在 HDInsight 群集上的组件依赖于第三方库。 通常，这些内置组件会引用 Guava 等常见模块的特定版本。 提交具有依赖项的应用程序时，可能会导致同一模块的不同版本之间发生冲突。 如果首先在类路径中引用的组件版本，则由于版本不兼容，内置组件可能会引发异常。 但是，如果内置组件首先向类路径注入依赖项，则应用程序可能会引发 `NoSuchMethod`之类的错误。

若要避免版本冲突，请考虑为应用程序依赖关系着色。

## <a name="what-does-package-shading-mean"></a>包底纹的含义是什么？
"底纹" 提供了一种方法来包含和重命名依赖项。 它重新定位类，并重写受影响的字节码和资源，创建依赖项的私有副本。

## <a name="how-to-shade-a-package"></a>如何为包着色？

### <a name="use-uber-jar"></a>使用 uber
Uber 是一个包含应用程序 jar 及其依赖项的 jar 文件。 Uber 中的依赖项默认情况下为未着色。 在某些情况下，如果其他组件或应用程序引用不同版本的库，这可能会导致版本冲突。 为避免出现这种情况，可以生成一个 Uber 文件，其中包含部分（或全部）依赖项的着色。

### <a name="shade-package-using-maven"></a>使用 Maven 为包着色
Maven 可以生成以 Java 和 Scala 编写的应用程序。 Maven-插件有助于轻松创建着色 uber。

下面的示例演示一个文件 `pom.xml` 该文件已更新为使用 maven 来为包着色。  XML 部分 `<relocation>…</relocation>` 通过移动相应的 JAR 文件条目并重新编写受影响的字节码，将类从包 `com.google.guava` 移到包 `com.google.shaded.guava` 中。

更改 `pom.xml`后，可以执行 `mvn package` 以生成着色的 uber。

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

### <a name="shade-package-using-sbt"></a>使用 SBT 为包着色
SBT 也是用于 Scala 和 Java 的生成工具。 SBT 没有阴影插件（如 maven）。 您可以修改 `build.sbt` 文件以为包加阴影。 

例如，若要 `com.google.guava`底纹，可以将以下命令添加到 `build.sbt` 文件：

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

然后，可以运行 `sbt clean` 和 `sbt assembly` 来生成着色后的 jar 文件。 

## <a name="next-steps"></a>后续步骤

* [使用 HDInsight IntelliJ 工具](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [在 IntelliJ 中创建适用于 Spark 的 Scala Maven 应用程序](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
