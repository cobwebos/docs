---
title: 初始值设定项 Codepackage Service Fabric
description: 介绍 Service Fabric 中的初始值设定项 Codepackage。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430625"
---
# <a name="initializer-codepackages"></a>Initializer CodePackage

从7.1 版开始，Service Fabric 支持[容器][containers-introduction-link]和[来宾可执行][guest-executables-introduction-link]应用程序的**codepackage 初始值设定项**。 初始值设定项 Codepackage 提供了在其他 Codepackage 开始执行之前，在 ServicePackage 范围执行初始化的机会。 它们与 ServicePackage 的关系类似于 CodePackage 的[SetupEntryPoint][setup-entry-point-link] 。

在继续阅读本文之前，建议熟悉[Service Fabric 应用程序模型][application-model-link]和[Service Fabric 托管模型][hosting-model-link]。

> [!NOTE]
> 使用[Reliable Services][reliable-services-link]编程模型编写的服务目前不支持初始值设定项 codepackage。
 
## <a name="semantics"></a>语义

应运行初始值设定项 CodePackage 才能**成功完成（退出代码0）**。 在成功完成之前，将重新启动失败的初始化表达式 CodePackage。 允许使用多个初始值设定项**codepackage，并**按**指定顺序****按**顺序执行多个初始值设定项。

## <a name="specifying-initializer-codepackages"></a>指定初始值设定项 Codepackage
可以通过在 Servicemanifest.xml 中将**初始值设定项**属性设置为**True** ，将 CodePackage 标记为初始值设定项。 如果有多个初始值设定项 Codepackage，则可以通过**ExecOrder**属性指定其执行顺序。 **ExecOrder**必须为非负整数，且仅对初始值设定项 codepackage 有效。 首先执行具有较小值**ExecOrder**的初始值设定项 codepackage。 如果没有为初始值设定项 CodePackage 指定**ExecOrder** ，则假定默认值为0。 未指定具有相同值的 Codepackage 初始值设定**项的相对**执行顺序。

以下 Servicemanifest.xml 代码片段介绍了三个标记为初始值设定项的 Codepackage。 如果激活了此 ServicePackage，则先执行*InitCodePackage0* ，因为它的最小值为**ExecOrder**。 成功完成（退出代码为0） *InitCodePackage0*，将执行*InitCodePackage1* 。 最后，在*InitCodePackage1*成功完成后，将执行*WorkloadCodePackage* 。

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
## <a name="complete-example-using-initializer-codepackages"></a>使用初始值设定项 Codepackage 的完整示例

让我们看一个使用初始值设定项 Codepackage 的完整示例。

> [!IMPORTANT]
> 以下示例假设熟悉如何[使用 Service Fabric 和 Docker 创建 Windows 容器应用程序][containers-getting-started-link]。
>
> 此示例引用 mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器并非在所有主机 OS 版本间都兼容。 若要了解详细信息，请参阅 [Windows 容器版本兼容性](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)。

下面的 Servicemanifest.xml 基于前面介绍的 Servicemanifest.xml 代码段构建。 *InitCodePackage0*、 *InitCodePackage1*和*WorkloadCodePackage*都是表示容器的 codepackage。 激活时，先执行*InitCodePackage0* 。 它将消息记录到文件中并退出。 接下来，执行*InitCodePackage1* ，它还将消息记录到文件中并退出。 最后， *WorkloadCodePackage*开始执行。 它还将消息记录到一个文件，将该文件的内容输出到**stdout** ，然后将其永久 ping。

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

以下 Applicationmanifest.xml 介绍了基于上述 Servicemanifest.xml 的应用程序。 请注意，它为所有容器指定相同的**卷**装入，即**C:\WorkspaceOnHost**在所有三个容器上装载到**C:\WorkspaceOnContainer** 。 最终效果是所有容器都按照它们的激活顺序写入同一日志文件。

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
成功激活 ServicePackage 后， **C:\WorkspaceOnHost\log.txt**的内容应如下所示。

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>后续步骤

请参阅以下文章了解相关信息。

* [Service Fabric 和容器。][containers-introduction-link]
* [Service Fabric 和来宾可执行文件。][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

