---
title: Azure Service Fabric 探测
description: 如何使用应用程序和服务清单文件在 Azure Service Fabric 中为活动探测建模。
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137580"
---
# <a name="liveness-probe"></a>活动探测
从版本7.1 开始，Azure Service Fabric 支持[容器][containers-introduction-link]化应用程序的活动探测机制。 活动探测可帮助报告容器化应用程序的活动，如果该应用程序未快速响应，将重新启动该应用程序。
本文概述了如何使用清单文件来定义活动探测。

在继续阅读本文之前，请先熟悉[Service Fabric 应用程序模型][application-model-link]和[Service Fabric 承载模型][hosting-model-link]。

> [!NOTE]
> 只有 NAT 网络模式下的容器才支持活动探测。

## <a name="semantics"></a>语义
只能为每个容器指定一个活动探测，并且可以通过使用以下字段来控制其行为：

* `initialDelaySeconds`：容器启动后开始执行探测的初始延迟时间（秒）。 支持的值为**int**。默认值为0，最小值为0。

* `timeoutSeconds`：时间段（以秒为单位），在这段时间内，如果探测未成功完成，则将其视为失败。 支持的值为**int**。默认值为1，最小值为1。

* `periodSeconds`：指定探测频率的时间段（以秒为单位）。 支持的值为**int**。默认值为10，最小值为1。

* `failureThreshold`：达到此值时，容器将重新启动。 支持的值为**int**。默认值为3，最小值为1。

* `successThreshold`：失败，若要将探测视为成功，必须为此值成功运行。 支持的值为**int**。默认值为1，最小值为1。

在任何时刻，最多可以有一个对一个容器的探测。 如果探测未在**timeoutSeconds**中设置的时间内完成，请等待并统计**failureThreshold**的时间。 

此外，Service Fabric 将在**DeployedServicePackage**上引发以下探测[运行状况报告][health-introduction-link]：

* `OK`：在**successThreshold**中设置的值的探测成功。

* `Error`：在容器重启之前，探测**failureCount** ==  **failureThreshold**。

* `Warning`: 
    * 探测失败并**failureCount** < **failureThreshold**。 此运行状况报告持续到**failureCount**达到**failureThreshold**或**successThreshold**中设置的值。
    * 如果成功，则会出现警告，但会连续成功更新。

## <a name="specifying-a-liveness-probe"></a>指定活动探测

可以在**ServiceManifestImport**下的 applicationmanifest.xml 文件中指定探测。

探测可用于以下任一操作：

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>HTTP 探测器

对于 HTTP 探测，Service Fabric 会将 HTTP 请求发送到指定的端口和路径。 如果返回代码大于或等于200且小于400，则指示成功。

下面的示例演示如何指定 HTTP 探测：

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

HTTP 探测具有可以设置的其他属性：

* `path`：要在 HTTP 请求中使用的路径。

* `port`：用于探测的端口。 此属性是必需的。 范围为1到65535。

* `scheme`：用于连接到代码包的方案。 如果将此属性设置为 HTTPS，则会跳过证书验证。 默认设置为 HTTP。

* `httpHeader`：要在请求中设置的标头。 可以指定多个标头。

* `host`：要连接到的主机 IP 地址。

### <a name="tcp-probe"></a>TCP 探测器

对于 TCP 探测，Service Fabric 将尝试使用指定的端口打开容器上的套接字。 如果它可以建立连接，探测将被视为成功。 下面的示例演示如何指定使用 TCP 套接字的探测：

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

此探测会将**exec**命令发出到容器，并等待命令完成。

> [!NOTE]
> **Exec**命令采用逗号分隔的字符串。 以下示例中的命令将适用于 Linux 容器。
> 如果你正在尝试探测 Windows 容器，请使用**cmd**。

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
有关相关信息，请参阅以下文章：
* [Service Fabric 和容器][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

