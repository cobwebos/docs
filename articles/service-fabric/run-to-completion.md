---
title: Service Fabric 中的 RunToCompletion 语义
description: 介绍 Service Fabric 中的 RunToCompletion 语义。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261206"
---
# <a name="runtocompletion"></a>RunToCompletion

从 7.1 版开始，Service Fabric 支持适用于[容器][containers-introduction-link]应用程序和[来宾可执行文件][guest-executables-introduction-link]应用程序的 RunToCompletion 语义。 这些语义使应用程序和服务一完成任务就退出，不需始终运行应用程序和服务。

继续阅读本文之前，建议先熟悉 [Service Fabric 应用程序模型][application-model-link]和 [Service Fabric 托管模型][hosting-model-link]。

> [!NOTE]
> 使用 [Reliable Services][reliable-services-link] 编程模型编写的服务目前不支持 RunToCompletion 语义。
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion 语义和规范
[导入 ServiceManifest][application-and-service-manifests-link]时，可以将 RunToCompletion 语义指定为 ExecutionPolicy。 指定的策略由构成该 ServiceManifest 的所有 CodePackage 继承。 以下 ApplicationManifest.xml 代码片段提供了一个示例。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
ExecutionPolicy 允许以下两个属性：
* **类型：** RunToCompletion 当前是此属性的唯一允许值。
* **Restart:** 此属性指定在失败时应用于构成该 ServicePackage 的 Codepackage 的重启策略。 以“非零退出代码”退出的 CodePackage 被视为已失败。 此属性的允许值为 **OnFailure** 和 **Never**，默认值为 **OnFailure**。

当重启策略设置为 **OnFailure** 时，如果有任何 CodePackage 失败 **（退出代码为非零值）** ，则会重启，在反复失败的情况下会进行回退。 当重启策略设置为 **Never** 时，如果有任何 CodePackage 失败，则 DeployedServicePackage 的部署状态会被标记为“失败”，但其他 CodePackage 可以继续执行。 如果构成该 ServicePackage 的所有 CodePackage 都运行至成功完成 **（退出代码为 0）** ，则 DeployedServicePackage 的部署状态会被标记为“RanToCompletion”。 

## <a name="complete-example-using-runtocompletion-semantics"></a>使用了 RunToCompletion 语义的完整示例

让我们看一个使用了 RunToCompletion 语义的完整示例。

> [!IMPORTANT]
> 以下示例假定你熟悉如何创建[使用 Service Fabric 和 Docker 的 Windows 容器应用程序][containers-getting-started-link]。
>
> 此示例引用了 mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器并非在所有主机 OS 版本间都兼容。 若要了解详细信息，请参阅 [Windows 容器版本兼容性](/virtualization/windowscontainers/deploy-containers/version-compatibility)。

以下 ServiceManifest.xml 描述了一个 ServicePackage，其中包含表示容器的两个 Codepackage。 RunToCompletionCodePackage1 直接将消息记录到 stdout 并退出。 RunToCompletionCodePackage2 将在一段时间内对环回地址执行 ping 操作，然后以退出代码 **0**、**1** 或 **2** 退出。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

下面的 ApplicationManifest.xml 描述了一个基于上述 ServiceManifest.xml 的应用程序。 它为 WindowsRunToCompletionServicePackage 指定了 **RunToCompletion** **ExecutionPolicy**，使用的重启策略为 **OnFailure**。 当激活 WindowsRunToCompletionServicePackage 时，构成它的那些 CodePackage 会启动。 RunToCompletionCodePackage1 在首次激活时应该会成功退出。 但是，RunToCompletionCodePackage2 可能会失败 **（退出代码为非零值）** 。在这种情况下，它会重启，因为重启策略为 **OnFailure**。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>查询 DeployedServicePackage 的部署状态
可以通过 PowerShell 使用 [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] 或通过 C# 使用 [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync(String, Uri, String)][deployed-service-package-fabricclient-link] 来查询 DeployedServicePackage 的部署状态

## <a name="considerations-when-using-runtocompletion-semantics"></a>使用 RunToCompletion 语义时的注意事项

对于当前的 RunToCompletion 支持，应注意以下几点。
* 只有[容器][containers-introduction-link]应用程序和[来宾可执行文件][guest-executables-introduction-link]应用程序支持这些语义。
* 不允许对包含 RunToCompletion 语义的应用程序执行升级方案。 必要时，用户应删除并重新创建此类应用程序。
* 故障转移事件可能会导致 Codepackage 在成功完成后在同一节点上或群集的其他节点上重新执行。 故障转移事件的示例包括：节点重启和节点上的 Service Fabric 运行时升级。

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
