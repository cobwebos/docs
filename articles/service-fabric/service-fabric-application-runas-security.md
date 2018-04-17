---
title: 在系统和本地安全帐户下运行 Azure Service Fabric 服务 | Microsoft Docs
description: 了解如何在系统和本地安全帐户下运行 Service Fabric 应用程序。  创建安全主体并应用运行方式策略以安全运行你的服务。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 471e5a79600d6a963a4fa5b6cec8d2cc16137489
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>以本地用户帐户或本地系统帐户运行服务
使用 Azure Service Fabric，可以保护群集中以不同用户帐户运行的应用程序。 默认情况下，Service Fabric 应用程序在运行 Fabric.exe 程序的帐户之下运行。 Service Fabric 还提供了在本地用户或系统帐户下运行应用程序的功能。 受支持的本地系统帐户类型为 **LocalUser**、**NetworkService**、**LocalService** 和 **LocalSystem**。  如果在 Windows 独立群集上运行 Service Fabric，可以使用 [Active Directory 域帐户](service-fabric-run-service-as-ad-user-or-group.md)或[组托管服务帐户](service-fabric-run-service-as-gmsa.md)运行服务。

在应用程序清单中，在 **Principals** 部分中定义运行服务或保护资源时所需的用户帐户。 还可以定义并创建用户组，以便统一管理一个或多个用户。 如果不同的服务入口点有多个用户，而且这些用户需要拥有可在组级别使用的常用权限，则这种做法特别有用。  然后，可以在 RunAs 策略中引用用户，该策略应用于应用程序中的特定服务或所有服务。 

默认情况下，RunAs 策略应用于主入口点。  如果需要[在系统帐户下运行特定的高权限设置操作](service-fabric-run-script-at-service-startup.md)，则还可以将 RunAs 策略应用于安装程序入口点，或者同时应用于主入口点和安装程序入口点。  

> [!NOTE] 
> 如果将 RunAs 策略应用到服务，且服务清单使用 HTTP 协议声明终结点资源，则必须指定 SecurityAccessPolicy。  有关详细信息，请参阅[为 HTTP 和 HTTPS 终结点分配安全访问策略](service-fabric-assign-policy-to-endpoint.md)。 
>

## <a name="run-a-service-as-a-local-user"></a>以本地用户身份运行服务
可以创建一个本地用户，用于帮助保护应用程序中的服务。 当在应用程序清单的主体部分中指定 **LocalUser** 帐户类型时，Service Fabric 在部署应用程序的计算机上创建本地用户帐户。 默认情况下，这些帐户的名称与应用程序清单中指定的名称不相同（例如，以下应用程序清单示例中的 *Customer3*）。 相反，它们是动态生成的并带有随机密码。

在 **ServiceManifestImport** 的 **RunAsPolicy** 部分中，从 **Principals** 部分中指定用来运行服务代码包的用户帐户。  以下示例展示了如何创建本地用户并将 RunAs 策略应用于主入口点：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>创建本地用户组
可以创建用户组并向组中添加一个或多个用户。 如果不同的服务入口点对应有多个用户，而且这些用户需要拥有特定的常见组级别权限，这种做法就特别有用。 下面的应用程序清单示例展示了一个名为 *LocalAdminGroup* 的具有管理员权限的本地组。 *Customer1* 和 *Customer2* 这两个用户已成为此本地组的成员。 在 **ServiceManifestImport** 部分中，应用了一个 RunAs 策略来以 *Customer2* 身份运行 *Stateful1Pkg* 代码包。  应用了另一个 RunAs 策略来以 *Customer1* 身份运行 *Web1Pkg* 代码包。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>将默认策略应用到所有服务代码包
**DefaultRunAsPolicy** 部分用于针对未定义特定 **RunAsPolicy** 的所有代码包指定默认用户帐户。 如果在应用程序所用的服务清单中指定的大多数代码包必须以同一用户运行，则应用程序可以只定义该用户帐户的默认 RunAs 策略。 以下示例指定如果代码包未指定 **RunAsPolicy**，则此代码包应该以 principals 部分中指定的 **MyDefaultAccount** 用户身份运行。  支持的帐户类型为 LocalUser、NetworkService、LocalSystem 和 LocalService。  如果使用本地用户或服务，则还需要指定帐户名称和密码。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>使用控制台重定向在本地调试代码包
有时，查看正在运行的服务的控制台输出对于调试很有帮助。 可以在服务清单中的入口点上设置控制台重定向策略，以便将输出写入到文件。 文件输出将写入到部署和运行应用程序的群集节点上名为 **log** 的应用程序文件夹中。 

> [!WARNING]
> 永远不要在生产中部署的应用程序中使用控制台重定向策略，因为这可能会影响应用程序故障转移。 *仅*将其用于本地开发和调试目的。  
> 
> 

下面的服务清单示例展示了如何使用 FileRetentionCount 值启用控制台重定向：

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
* [了解应用程序模型](service-fabric-application-model.md)
* [在服务清单中指定资源](service-fabric-service-manifest-resources.md)
* [部署应用程序](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
