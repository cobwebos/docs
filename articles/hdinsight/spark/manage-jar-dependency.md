---
title: 管理 JAR 依赖项 - Azure HDInsight
description: 本文讨论了管理 HDInsight 应用程序的 Java 存档 （JAR） 依赖项的最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135728"
---
# <a name="jar-dependency-management-best-practices"></a>JAR 依赖项管理最佳实践

安装在 HDInsight 群集上的组件依赖于第三方库。 通常，这些内置组件会引用诸如 Guava 等通用模块的特定版本。 当您提交具有其依赖项的应用程序时，可能会导致同一模块的不同版本之间的冲突。 如果首先在类路径中引用的组件版本，则内置组件可能会由于版本不兼容而引发异常。 但是，如果内置组件首先将其依赖项注入到类路径，则应用程序可能会引发类似`NoSuchMethod`的错误。

为了避免版本冲突，请考虑对应用程序依赖项进行对等进行对等的还原。

## <a name="what-does-package-shading-mean"></a>包装上带下什么含义？
对下定义提供了一种包含和重命名依赖项的方法。 它重新定位类并重写受影响的字节码和资源，以创建依赖项的私有副本。

## <a name="how-to-shade-a-package"></a>如何为包裹着色？

### <a name="use-uber-jar"></a>使用优步
Uber-jar 是一个包含应用程序 jar 及其依赖项的单个 jar 文件。 默认情况下，Uber-jar 中的依赖项没有上用。 在某些情况下，如果其他组件或应用程序引用这些库的不同版本，则可能会引入版本冲突。 为了避免这种情况，您可以构建一个 Uber-Jar 文件，该文件包含一些（或全部）依赖项。

### <a name="shade-package-using-maven"></a>使用 Maven 的阴影包
Maven 可以构建以 Java 和 Scala 编写的应用程序。 Maven-shade-插件可以帮助您轻松创建遮阳超级插件。

下面的示例显示了一个文件`pom.xml`，该文件已更新，用于使用 maven-shade-插件对包进行着色。  XML 部分`<relocation>…</relocation>`通过移动相应的`com.google.guava`JAR`com.google.shaded.guava`文件条目并重写受影响的字节码，将类从包移动到包中。

更改`pom.xml`后，可以执行`mvn package`以构建具有扫描的 uber-jar。

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

### <a name="shade-package-using-sbt"></a>使用 SBT 的阴影包
SBT 也是 Scala 和 Java 的构建工具。 SBT 没有像 maven-shade 插件那样的阴影插件。 您可以将文件修改`build.sbt`为阴影包。 

例如，要着色`com.google.guava`，可以向`build.sbt`文件添加以下命令：

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

然后，您可以运行`sbt clean`并`sbt assembly`生成已扫描的 jar 文件。 

## <a name="next-steps"></a>后续步骤

* [使用 HDInsight 智能 J 工具](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [在 IntelliJ 中为 Spark 创建 Scala Maven 应用程序](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
