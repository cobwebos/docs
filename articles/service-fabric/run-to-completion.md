---
title: Service Fabric 中的 RunToCompletion 语义
description: 介绍 Service Fabric 中的 RunToCompletion 语义。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431288"
---
# <a name="runtocompletion"></a>RunToCompletion

从7.1 版开始，Service Fabric 支持[容器][containers-introduction-link]和[来宾可执行][guest-executables-introduction-link]应用程序的**RunToCompletion**语义。 这些语义启用了完成任务并退出的应用程序和服务，而不是始终运行应用程序和服务。

在继续阅读本文之前，建议熟悉[Service Fabric 应用程序模型][application-model-link]和[Service Fabric 托管模型][hosting-model-link]。

> [!NOTE]
> RunToCompletion 语义当前不支持使用[Reliable Services][reliable-services-link]编程模型编写的服务。
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion 语义和规范
[导入 servicemanifest.xml][application-and-service-manifests-link]时，可以将 RunToCompletion 语义指定为**set-executionpolicy** 。 指定的策略由包含 Servicemanifest.xml 的所有 Codepackage 继承。 以下 Applicationmanifest.xml 代码片段提供了一个示例。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**Set-executionpolicy**允许以下两个属性：
* **类型：** **RunToCompletion**当前是此属性的唯一允许值。
* **重新启动：** 此属性指定在失败时应用于由 ServicePackage 组成的 Codepackage 的重新启动策略。 使用**非零退出代码**的 CodePackage 退出被视为已失败。 此属性的允许值为**OnFailure** ，**且** **OnFailure**为默认值。

如果将重启策略设置为**OnFailure**，则如果任何 CodePackage 失败 **（非零退出代码）**，则会重新启动，并在重复失败之间来回切换。 如果将重启策略设置为 "**从不**"，则在任何 CodePackage 失败的情况下，DeployedServicePackage 的部署状态将标记为 "**失败**"，但允许继续执行其他 codepackage。 如果包含 ServicePackage 运行的所有 Codepackage 成功完成 **（退出代码0）**，则 DeployedServicePackage 的部署状态将标记为**RanToCompletion**。 

## <a name="complete-example-using-runtocompletion-semantics"></a>使用 RunToCompletion 语义的完整示例

让我们看一个使用 RunToCompletion 语义的完整示例。

> [!IMPORTANT]
> 以下示例假设熟悉如何[使用 Service Fabric 和 Docker 创建 Windows 容器应用程序][containers-getting-started-link]。
>
> 此示例引用 mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器并非在所有主机 OS 版本间都兼容。 若要了解详细信息，请参阅 [Windows 容器版本兼容性](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)。

以下 Servicemanifest.xml 描述了一个 ServicePackage，其中包含表示容器的两个 Codepackage。 *RunToCompletionCodePackage1*只是将消息记录到**stdout** ，然后退出。 *RunToCompletionCodePackage2* ping 环回地址一段时间后，退出代码为**0**、 **1**或**2**。

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

以下 Applicationmanifest.xml 介绍了基于上述 Servicemanifest.xml 的应用程序。 它为*WindowsRunToCompletionServicePackage*指定**RunToCompletion** **set-executionpolicy** ，重新启动策略为**OnFailure**。 *WindowsRunToCompletionServicePackage*激活时，将启动其组成的 codepackage。 第一次激活时， *RunToCompletionCodePackage1*应成功退出。 但是， *RunToCompletionCodePackage2*可能会失败 **（非零退出代码）**，在这种情况下，将重新启动，因为重新启动策略为**OnFailure**。

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
可以使用[ServiceFabricDeployedServicePackage][deployed-service-package-link]在 PowerShell 中查询 DeployedServicePackage 的部署状态，也可以从 c # 使用[FabricClient][fabric-client-link] API [fabricclient.queryclient.getdeployedservicepackagelistasync （string，Uri，String）][deployed-service-package-fabricclient-link]查询

## <a name="considerations-when-using-runtocompletion-semantics"></a>使用 RunToCompletion 语义时的注意事项

对于当前的 RunToCompletion 支持，应注意以下几点。
* 仅[容器][containers-introduction-link]和[来宾可执行][guest-executables-introduction-link]应用程序支持这些语义。
* 不允许使用具有 RunToCompletion 语义的应用程序的升级方案。 如果需要，用户应删除并重新创建此类应用程序。
* 故障转移事件会导致 Codepackage 在成功完成后、同一节点或群集的其他节点上重新执行。 故障转移事件的示例包括节点重启和 Service Fabric 运行时升级。

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

