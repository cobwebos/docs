---
title: 服务结构中的初始化代码包
description: 描述服务结构中的初始化器代码包。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430625"
---
# <a name="initializer-codepackages"></a>初始化器代码包

从版本 7.1 开始，Service Fabric 支持[容器][containers-introduction-link]和[来宾可执行][guest-executables-introduction-link]应用程序的**初始化器代码包**。 初始化器代码包提供了在其他代码包开始执行之前在服务包作用域执行初始化的机会。 它们与服务包的关系类似于代码包的[SetupEntryPoint。][setup-entry-point-link]

在继续本文之前，我们建议熟悉[服务结构应用程序模型][application-model-link][和服务结构托管模型][hosting-model-link]。

> [!NOTE]
> 使用[可靠服务][reliable-services-link]编程模型编写的服务当前不支持初始化码包。
 
## <a name="semantics"></a>语义

初始化器代码包预计将运行到**成功完成（退出代码 0）。** 故障的初始化程序代码包将重新启动，直到它成功完成。 在服务包中的其他代码包开始执行**之前，允许**并按指定**sequentially**顺序执行多个初始化器代码包，并按**指定顺序**执行。

## <a name="specifying-initializer-codepackages"></a>指定初始化器代码包
通过在服务清单中将**初始化器**属性设置为**true，** 可以将代码包标记为初始化程序。 当有多个初始化器代码包时，可以通过**ExecOrder**属性指定其执行顺序。 **ExecOrder**必须是非负整数，并且仅适用于初始化代码包。 首先执行具有**执行 Order**较低值的初始化代码包。 如果未为初始化器代码包指定**ExecOrder，** 则假定默认值 0。 未指定具有相同**ExecOrder**值的初始化代码包的相对执行顺序。

以下服务清单代码段描述了三个代码包，其中两个标记为初始化程序。 激活此服务包时，首先执行*InitCodePackage0，* 因为它具有最低值**ExecOrder**。 成功完成*InitCode 包0*后，将执行*InitCode 包1。* 最后，在成功完成*InitCode 包 1*后，将执行*工作负载代码包*。

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>使用初始化器代码包的完整示例

让我们看一个使用初始化器代码包的完整示例。

> [!IMPORTANT]
> 下面的示例假定熟悉使用 Service Fabric 和 Docker 创建[Windows 容器应用程序][containers-getting-started-link]。
>
> 此示例引用mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器并非在所有主机 OS 版本间都兼容。 若要了解详细信息，请参阅 [Windows 容器版本兼容性](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)。

以下服务清单.xml 基于前面介绍的服务清单代码段。 *InitCode包0* *，InitCode包1*和*工作负载代码包*是表示容器的代码包。 激活后，首先执行*InitCode 包0。* 它将消息记录到文件并退出。 接下来，执行*InitCodePackage1，* 该程序还会将消息记录到文件和退出。 最后，*工作负载代码包*开始执行。 它还将消息记录到文件，输出文件的内容以**进行停滞**，然后永久 ping。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

以下应用程序清单.xml 描述了基于上述讨论的 ServiceManifest.xml 的应用程序。 请注意，它为所有容器指定相同的**卷**装载，即**C：_WorkspaceOnHost**安装在所有三个容器上的**C：_WorkspaceOn容器**中。 最终结果是，所有容器都按激活顺序写入同一日志文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
成功激活服务包后 **，C：_WorkspaceOnHost_log.txt**的内容应如下。

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>后续步骤

有关相关信息，请参阅以下文章。

* [服务结构和容器。][containers-introduction-link]
* [服务结构和来宾可执行文件。][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

