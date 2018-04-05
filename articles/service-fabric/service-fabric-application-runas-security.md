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
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 62917a1d342158ec2114a9204ee1ca9e447284fa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>以本地用户帐户或本地系统帐户运行服务
使用 Azure Service Fabric，可以保护群集中以不同用户帐户运行的应用程序。 默认情况下，Service Fabric 应用程序在运行 Fabric.exe 程序的帐户之下运行。 借助 Service Fabric，还可以通过在应用程序清单中指定 RunAs 策略，以使用本地用户帐户或本地系统帐户运行应用程序。 受支持的本地系统帐户类型为 **LocalUser**、**NetworkService**、**LocalService** 和 **LocalSystem**。

还可以定义和创建用户组，以便将一个或多个要统一管理的用户添加到每个组。 如果不同的服务入口点有多个用户，而且这些用户需要拥有可在组级别使用的某些常用权限，则这种做法特别有用。

> [!NOTE] 
> 如果将 RunAs 策略应用到服务，且服务清单使用 HTTP 协议声明终结点资源，则必须指定 SecurityAccessPolicy。  有关详细信息，请参阅[为 HTTP 和 HTTPS 终结点分配安全访问策略](service-fabric-assign-policy-to-endpoint.md)。 
>

## <a name="create-local-user-groups"></a>创建本地用户组
可以定义和创建允许将一个或多个用户添加到组的用户组。 如果不同的服务入口点对应有多个用户，而且这些用户需要拥有特定的常见组级别权限，这种做法就特别有用。 下面的示例演示一个名为 **LocalAdminGroup** 的具有管理员权限的本地组。 在此应用程序清单示例中，Customer1 和 Customer2 这两个用户已成为此本地组的成员：

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
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
```

## <a name="create-local-users"></a>创建本地用户
可以创建一个本地用户，用于帮助保护应用程序中的服务。 当在应用程序清单的主体部分中指定 **LocalUser** 帐户类型时，Service Fabric 在部署应用程序的计算机上创建本地用户帐户。 默认情况下，这些帐户的名称不与应用程序清单中指定的名称相同（例如，以下应用程序清单示例中的 Customer3）。 相反，它们是动态生成的并带有随机密码。

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

如果应用程序要求所有计算机上的用户帐户和密码都相同（例如，启用 NTLM 身份验证），群集清单必须将 NTLMAuthenticationEnabled 设置为 true。 群集清单还必须指定 NTLMAuthenticationPasswordSecret，用于对所有计算机生成相同的密码。

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>将策略分配到服务代码包
**ServiceManifestImport** 的 **RunAsPolicy** 部分指定 principals 部分中应该用来运行代码包的帐户。 它还将服务清单中的代码包关联到 principals 节中的用户帐户。 可以为设置入口点或主入口点指定此策略，或者指定 `All` 以将其应用到两者。 以下示例演示了应用的不同策略：

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

如果未指定 EntryPointType，则默认值设置为 `EntryPointType=”Main”`。 如果想要以系统帐户运行某些高特权设置操作，则指定 **SetupEntryPoint** 将特别有用。 有关详细信息，请参阅[以本地用户或系统帐户身份运行服务启动脚本](service-fabric-run-script-at-service-startup.md)。 实际服务代码可使用较低特权的帐户来运行。

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>将默认策略应用到所有服务代码包
**DefaultRunAsPolicy** 部分用于针对未定义特定 **RunAsPolicy** 的所有代码包指定默认用户帐户。 如果在应用程序所用的服务清单中指定的大多数代码包必须以同一用户运行，则应用程序可以只定义该用户帐户的默认 RunAs 策略。 以下示例指定如果代码包未指定 **RunAsPolicy**，则此代码包应该以 principals 部分指定的 **MyDefaultAccount** 运行。

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
* [了解应用程序模型](service-fabric-application-model.md)
* [在服务清单中指定资源](service-fabric-service-manifest-resources.md)
* [部署应用程序](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
