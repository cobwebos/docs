---
title: Azure 服务结构探测器
description: 如何使用应用程序和服务清单文件在 Azure 服务结构中建模实时探测。
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431210"
---
# <a name="liveness-probe"></a>活度探头
从 7.1 服务结构开始支持[容器化][containers-introduction-link]应用的活度探测机制。 Liveness 探测有助于宣布容器化应用程序的真实性，当它们不及时响应时，将导致重新启动。
本文概述了如何通过清单文件定义动态探测。

在继续本文之前，我们建议熟悉[服务结构应用程序模型][application-model-link][和服务结构托管模型][hosting-model-link]。

> [!NOTE]
> 仅在 NAT 网络模式下的容器中支持活动探测。

## <a name="semantics"></a>语义
每个容器只能指定一个活动探测，并且可以使用以下字段控制其行为：

* `initialDelaySeconds`：容器启动后，在数秒内开始执行探测的初始延迟。 支持的值为 int。默认值为 0。 最小值为 0。

* `timeoutSeconds`：在探测器未成功完成之后，我们将探测器视为失败。 支持的值为 int。默认值为 1。 最小值为 1。

* `periodSeconds`：以秒为单位，指定我们探测的频率。 支持的值为 int。默认值为 10。 最小值为 1。

* `failureThreshold`：一旦我们达到故障阈值，容器将重新启动。 支持的值为 int。默认值为 3。 最小值为 1。

* `successThreshold`：在失败时，要将探测器视为成功，必须成功执行成功阈值。 支持的值为 int。默认值为 1。 最小值为 1。

一次最多会有 1 个探头到容器。 如果探头没有在超时中完成**秒，** 我们会一直等待并将其计入**故障阈值**。 

此外，ServiceFabric 将在部署的服务包上引发以下探测[运行状况报告][health-introduction-link]：

* `Ok`：如果探测器**成功，那么**我们将运行状况报告为"确定"。

* `Error`：如果探测失败Count =**失败阈值**，则在重新启动容器之前，我们会报告错误。

* `Warning`: 
    1. 如果探测失败，并且故障计数<**故障阈值**，我们会报告警告。 此健康报告一直持续到失败计数达到**失败阈值**或**成功阈值**。
    2. 关于成功失败后，我们仍然报告警告，但更新的连续成功。

## <a name="specifying-liveness-probe"></a>指定活动性探测

您可以在"服务清单导入"下的应用程序清单.xml 中指定探测：

探头可以之一：

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>HTTP 探头

对于 HTTP 探测，服务交换矩阵将向指定的端口和路径发送 HTTP 请求。 返回代码大于或等于 200 且小于 400 表示成功。

下面是如何指定 HttpGet 探测器的示例：

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

HttpGet 探测器具有您可以设置的其他属性：

* `path`：HTTP 请求上访问的路径。

* `port`：要进入探头的端口。 范围为 1 到 65535。 Mandatory。

* `scheme`：用于连接到代码包的方案。 如果设置为 HTTPS，将跳过证书验证。 默认值为 HTTP

* `httpHeader`：要在请求中设置的标头。 您可以指定其中的多个。

* `host`：要连接到的主机 IP。

## <a name="tcp-probe"></a>TCP 探头

对于 TCP 探测，Service Fabric 将尝试打开容器上的套接字，该套接字带有指定的端口。 如果可以建立连接，则探测器将被视为成功。 下面是如何指定使用 TCP 套接字的探测的示例：

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

## <a name="exec-probe"></a>执行探测

此探测将发出执行器到容器中，并等待命令完成。

> [!NOTE]
> Exec 命令采用逗号分隔字符串。 示例中的以下命令适用于 Linux 容器。
> 如果您正在尝试窗口容器，请使用<Command>cmd</Command>

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
有关相关信息，请参阅以下文章。
* [服务结构和容器。][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

