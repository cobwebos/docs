---
title: Service Fabric 中的 Initializer CodePackage
description: 介绍了 Service Fabric 中的 Initializer CodePackage。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258971"
---
# <a name="initializer-codepackages"></a>Initializer CodePackage

从 7.1 版开始，Service Fabric 支持适用于[容器][containers-introduction-link]应用程序和[来宾可执行文件][guest-executables-introduction-link]应用程序的 **Initializer CodePackage**。 Initializer CodePackage 提供了在其他 Codepackage 开始执行之前在 ServicePackage 范围内执行初始化操作的机会。 它们与 ServicePackage 的关系类似于 [SetupEntryPoint][setup-entry-point-link] 相对于 CodePackage 的关系。

继续阅读本文之前，建议先熟悉 [Service Fabric 应用程序模型][application-model-link]和 [Service Fabric 托管模型][hosting-model-link]。

> [!NOTE]
> 使用 [Reliable Services][reliable-services-link] 编程模型编写的服务目前不支持 Initializer CodePackage。
 
## <a name="semantics"></a>语义

正常情况下，Initializer CodePackage 在运行后会成功完成（退出代码为 0）。 失败的 Initializer CodePackage 会重启，直至成功完成。 在 ServicePackage 中的其他 CodePackage 开始执行之前，允许多个 Initializer CodePackage 顺序执行（按指定的顺序），直至成功完成。  

## <a name="specifying-initializer-codepackages"></a>指定 Initializer CodePackage
可以将 CodePackage 标记为 Initializer，方法是在 ServiceManifest 中将 **Initializer** 属性设置为 **true**。 如果有多个 Initializer CodePackage，则可通过 **ExecOrder** 属性指定其执行顺序。 **ExecOrder** 必须为非负整数，仅对 Initializer CodePackage 有效。 **ExecOrder** 值较小的 Initializer CodePackage 先执行。 如果没有为 Initializer CodePackage 指定 **ExecOrder**，则会采用默认值 0。 未指定 **ExecOrder** 值相同的 Initializer CodePackage 的相对执行顺序。

以下 ServiceManifest 代码片段描述了三个 CodePackage，其中的两个标记为 Initializer。 激活此 ServicePackage 时，*InitCodePackage0* 先执行，因为它的 **ExecOrder** 值最小。 InitCodePackage0 成功完成（退出代码为 0）后，系统会执行 InitCodePackage1。 最后，在成功完成 InitCodePackage1 后，会执行 WorkloadCodePackage。

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
## <a name="complete-example-using-initializer-codepackages"></a>使用 Initializer CodePackage 的完整示例

让我们看看一个使用 Initializer CodePackage 的完整示例。

> [!IMPORTANT]
> 以下示例假定你熟悉如何创建[使用 Service Fabric 和 Docker 的 Windows 容器应用程序][containers-getting-started-link]。
>
> 此示例引用了 mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器并非在所有主机 OS 版本间都兼容。 若要了解详细信息，请参阅 [Windows 容器版本兼容性](/virtualization/windowscontainers/deploy-containers/version-compatibility)。

以下 ServiceManifest.xml 在前述 ServiceManifest 代码片段基础上构建。 InitCodePackage0、InitCodePackage1 和 WorkloadCodePackage 是表示容器的 CodePackage  。 激活后，*InitCodePackage0* 先执行。 它将一条消息记录到一个文件中，然后退出。 接下来执行 *InitCodePackage1*，它也将一条消息记录到一个文件中，然后退出。 最后，*WorkloadCodePackage* 开始执行。 它也将一条消息记录到一个文件中，将文件内容输出到 **stdout**，然后一直 ping 下去。

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

以下 ApplicationManifest.xml 描述了一个基于上述 ServiceManifest.xml 的应用程序。 请注意，它为所有容器指定同一个卷装载。也就是说，在所有三个容器中，**C:\WorkspaceOnHost** 都装载在 **C:\WorkspaceOnContainer** 中。 最终效果是，所有容器都按其激活顺序将内容写入同一日志文件。

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
成功激活 ServicePackage 后，**C:\WorkspaceOnHost\log.txt** 的内容应如下所示。

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解相关信息。

* [Service Fabric 和容器][containers-introduction-link]。
* [Service Fabric 和来宾可执行文件][guest-executables-introduction-link]。

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
