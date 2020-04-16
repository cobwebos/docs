---
title: 在服务结构中运行完成语义
description: 描述服务结构中的 Runto 完成语义。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431288"
---
# <a name="runtocompletion"></a>运行完成

从版本 7.1 开始，Service Fabric 支持[容器][containers-introduction-link]和[来宾可执行][guest-executables-introduction-link]应用程序的**RunTo 完成**语义。 这些语义支持始终运行应用程序和服务的应用程序和服务完成任务和退出。

在继续本文之前，我们建议熟悉[服务结构应用程序模型][application-model-link][和服务结构托管模型][hosting-model-link]。

> [!NOTE]
> 使用[可靠服务][reliable-services-link]编程模型编写的服务当前不支持 RunTo 完成语义。
 
## <a name="runtocompletion-semantics-and-specification"></a>运行完成语义和规范
[在导入服务清单时，][application-and-service-manifests-link]可以将 Runto 完成语义指定为**执行策略**。 指定的策略由包含服务清单的所有代码包继承。 以下应用程序清单.xml 代码段提供了一个示例。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**执行策略**允许以下两个属性：
* **类型****：RunTo 完成**目前是此属性的唯一允许值。
* **重新启动：** 此属性指定在失败时应用于包含服务包的代码包的重新启动策略。 使用**非零退出代码**退出的代码包被视为失败。 此属性的允许值为 **"打开失败****"，从不**以 **"未失败**"为默认值。

当重新启动策略设置为**OnFailure**时，如果任何代码包失败 **（非零退出代码），** 则重新启动它，在重复失败之间回退。 如果任何代码包失败，重新启动策略设置为 **"从不**"，则部署服务包的部署状态将标记为 **"失败**"，但允许其他代码包继续执行。 如果包含服务包的所有代码包都运行到成功完成 **（退出代码 0），** 则部署服务包的部署状态将标记为**RanTo 完成**。 

## <a name="complete-example-using-runtocompletion-semantics"></a>使用 Runto 完成语义完成示例

让我们看一个使用 RunToComplete 语义的完整示例。

> [!IMPORTANT]
> 下面的示例假定熟悉使用 Service Fabric 和 Docker 创建[Windows 容器应用程序][containers-getting-started-link]。
>
> 此示例引用mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器并非在所有主机 OS 版本间都兼容。 若要了解详细信息，请参阅 [Windows 容器版本兼容性](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)。

以下 ServiceManifest.xml 描述了由两个代码包组成的服务包，它们表示容器。 *RunTo 完成代码包1*只需记录一条消息以**进行停滞**和退出。 *RunToTo完成代码包2* ping回环地址一段时间，然后退出代码**为0，1**或**2。** **1**

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

以下应用程序清单.xml 描述了基于上述讨论的 ServiceManifest.xml 的应用程序。 它为*WindowsRunto 完成服务包*指定**运行完成****执行策略**，并具有 **"OnFailure"** 的重新启动策略。 激活*WindowsRunTo 完成服务包*后，其组成代码包将启动。 *运行完成代码包1*应在第一次激活时成功退出。 但是 *，Runto完成代码包2*可能会失败 **（非零退出代码），** 在这种情况下，它将重新启动，因为重新启动策略是**OnFail。**

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
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>查询已部署服务包的部署状态
可以使用[获取服务交换服务包][deployed-service-package-link]从 PowerShell 查询已部署服务包的部署状态，也可以使用[FabricClient][fabric-client-link] API[获取服务包Listasync（字符串、Uri、字符串）][deployed-service-package-fabricclient-link]从 C# 查询

## <a name="considerations-when-using-runtocompletion-semantics"></a>使用 Runto 完成语义时的注意事项

当前运行完成支持应记下以下几点。
* 这些语义仅支持[容器][containers-introduction-link]和[来宾可执行][guest-executables-introduction-link]应用程序。
* 不允许使用 RunTo 完成语义的应用程序升级方案。 如有必要，用户应删除并重新创建此类应用程序。
* 故障转移事件可能导致代码包在成功完成后在同一节点或群集的其他节点上重新执行。 故障转移事件的示例包括节点上的节点重新启动和服务结构运行时升级。

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

