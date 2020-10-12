---
title: Azure Service Fabric 探测
description: 如何使用应用程序和服务清单文件在 Azure Service Fabric 中对运行情况探测进行建模。
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82137580"
---
# <a name="liveness-probe"></a>运行情况探测
从版本 7.1 开始，Azure Service Fabric 支持针对[容器化][containers-introduction-link]应用程序的运行情况探测机制。 运行情况探测有助于报告容器化应用程序的运行情况，如果应用程序没有快速响应，它将重启。
本文概述了如何通过使用清单文件定义运行情况探测。

继续阅读本文之前，请熟悉 [Service Fabric 应用程序模型][application-model-link]和 [Service Fabric 托管模型][hosting-model-link]。

> [!NOTE]
> 只有 NAT 网络模式下的容器支持运行情况探测。

## <a name="semantics"></a>语义
只能为每个容器指定 1 个运行情况探测，可以通过以下字段控制其行为：

* `initialDelaySeconds`：容器启动后开始执行探测的初始延迟秒数。 支持的值为整数。默认值为 0，最小值为 0。

* `timeoutSeconds`：一个时间段（秒），在此时间之后，如果探测未成功完成，则会将其视为失败。 支持的值为整数。默认值为 1，最小值为 1。

* `periodSeconds`：用于指定探测频率的时间段（秒）。 支持的值为整数。默认值为 10，最小值为 1。

* `failureThreshold`：当达到此值时，容器将重启。 支持的值为整数。默认值为 3，最小值为 1。

* `successThreshold`：失败后，若要将探测视为成功，则必须成功运行此值。 支持的值为整数。默认值为 1，最小值为 1。

在任何时刻，最多只能有对一个容器的一个探测。 如果探测未在“timeoutSeconds”中设置的时间内完成，请等待并计算到达“failureThreshold”的时间 。 

此外，Service Fabric 还会引发有关“DeployedServicePackage”的以下探测[运行状况报告][health-introduction-link]：

* `OK`：对于“successThreshold”中设置的值，探测成功。

* `Error`：容器重启之前，探测“failureCount” ==  “failureThreshold” 。

* `Warning`： 
    * 探测失败，“failureCount” < “failureThreshold” 。 此运行状况报告会一直进行下去，直至“failureCount”达到在“failureThreshold”或“successThreshold”中设置的值  。
    * 如果失败后成功，警告仍然存在，但会包含更新的续成功。

## <a name="specifying-a-liveness-probe"></a>指定运行情况探测

可以在 ApplicationManifest.xml 文件中的“ServiceManifestImport”下指定探测。

探测可用于以下任一情况：

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>HTTP 探测器

对于 HTTP 探测，Service Fabric 会向指定的端口和路径发送 HTTP 请求。 返回代码大于或等于 200 且小于 400 表示成功。

下面是一个演示如何指定 HTTP 探测的示例：

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HTTP 探测有其他可设置的属性：

* `path`：要在 HTTP 请求中使用的路径。

* `port`：用于探测的端口。 此属性是必需的。 范围为 1 到 65535。

* `scheme`：用于连接到代码包的方案。 如果将此属性设置为 HTTPS，则会跳过证书验证。 默认设置为 HTTP。

* `httpHeader`：要在请求中设置的标头。 可以指定多个标头。

* `host`：要连接到的主机 IP 地址。

### <a name="tcp-probe"></a>TCP 探测器

对于 TCP 探测，Service Fabric 会尝试使用指定的端口在容器上打开一个套接字。 如果它可以建立连接，则探测会被视为成功。 下面的示例展示了如何指定使用 TCP 套接字的探测：

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Exec 探测

此探测将向容器发出一个“exec”命令，并等待命令完成。

> [!NOTE]
> “Exec”命令接受以逗号分隔的字符串。 以下示例中的命令适用于 Linux 容器。
> 如果要探测 Windows 容器，请使用“cmd”。

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解相关信息：
* [Service Fabric 和容器][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

