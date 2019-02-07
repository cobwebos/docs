---
title: Azure Service Fabric Reliable Services 应用程序清单示例 | Microsoft Docs
description: 了解如何为 Reliable Services Service Fabric 应用程序配置应用程序和服务清单设置。
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: ryanwi
ms.openlocfilehash: 9cb41bfde38d9b47f5db994c0ca39c64b453ef1d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171450"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Reliable Services 应用程序和服务清单示例
下面是包含 ASP.NET Core Web 前端和有状态后端的 Service Fabric 应用程序的应用程序和服务清单示例。 这些示例的用途是展示有哪些设置可用以及如何使用它们。 这些应用程序和服务清单基于 [Service Fabric .NET 快速入门](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)清单。

展示了以下功能：
|清单|功能|
|---|---|
|[应用程序清单](#application-manifest)| [资源监管](service-fabric-resource-governance.md)、[以本地管理员帐户运行服务](service-fabric-application-runas-security.md)、[将默认策略应用到所有服务代码包](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)、[创建用户和组主体](service-fabric-application-runas-security.md)、在服务实例之间共享数据包、[重写服务终结点](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|[FrontEndService 服务清单](#frontendservice-service-manifest)| [服务启动时运行脚本](service-fabric-run-script-at-service-startup.md)、[定义 HTTPS 终结点](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|[BackEndService 服务清单](#backendservice-service-manifest)| [声明配置包](service-fabric-application-and-service-manifests.md)、[声明数据包](service-fabric-application-and-service-manifests.md)、[配置终结点](service-fabric-service-manifest-resources.md)| 

有关特定 XML 元素的详细信息，请参阅[应用程序清单元素](#application-manifest-elements)、[VotingWeb 服务清单元素](#votingweb-service-manifest-elements)和 [VotingData 服务清单元素](#votingdata-service-manifest-elements)。

## <a name="application-manifest"></a>应用程序清单

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>VotingWeb 服务清单

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>VotingData 服务清单

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>应用程序清单元素
### <a name="applicationmanifest-element"></a>ApplicationManifest 元素
以声明方式描述应用程序类型和版本。 引用构成服务的一个或多个服务清单来撰写应用程序类型。 可以使用参数化应用程序设置重写构成服务的配置设置。 默认服务、服务模板、主体、策略、诊断设置和证书也可以在应用程序级别声明。 有关详细信息，请参阅 [ApplicationManifest 元素](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Parameters 元素
声明在此应用程序清单中使用的参数。 当应用程序已实例化并可用于重写应用程序或服务配置设置时，可以提供这些参数的值。 有关详细信息，请参阅 [Parameters 元素](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameter 元素
要在此清单中使用的应用程序参数。 可在应用程序实例化期间更改参数值；如果未提供值，则使用默认值。 有关详细信息，请参阅 [Parameter 元素](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport 元素
导入服务开发人员创建的服务清单。 必须导入应用程序中每个构成服务的服务清单。 可为服务清单声明配置重写和策略。 有关详细信息，请参阅 [ServiceManifestImport 元素](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef 元素
按引用导入服务清单。 当前，服务清单文件 (ServiceManifest.xml) 必须存在于生成包中。 有关详细信息，请参阅 [ServiceManifestRef 元素](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides 元素
指定服务清单资源中声明的终结点的资源重写。 有关详细信息，请参阅 [ResourceOverrides 元素](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Endpoints 元素
要重写的终结点。 有关详细信息，请参阅 [Endpoints 元素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint 元素
服务清单中声明的要重写的终结点。 有关详细信息，请参阅 [Endpoint 元素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Policies 元素
描述要在导入的服务清单中应用的策略（终结点绑定、包共享、运行方式和安全访问权限）。 有关详细信息，请参阅 [Policies 元素](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy 元素
定义在整个服务包级别应用的资源调控策略。 有关详细信息，请参阅 [ServicePackageResourceGovernancePolicy 元素](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy 元素
指定代码包的资源限制。 有关详细信息，请参阅 [ResourceGovernancePolicy 元素](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy 元素
指示是否应跨同一服务类型的服务实例共享某个代码、配置或数据包。 有关详细信息，请参阅 [PackageSharingPolicy 元素](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy 元素
向服务清单中定义的资源（例如终结点）上的主体授予访问权限。 通常，它非常有助于控制和限制服务对不同资源的访问，以尽量降低安全风险。 从市场中由不同开发人员开发的服务集合构建应用程序时，此属性尤其重要。 有关详细信息，请参阅 [SecurityAccessPolicy 元素](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy 元素
指定运行服务代码包时所用的本地用户或本地系统帐户。 在 Azure Active Directory 是可用的 Windows Server 部署上支持域帐户。 默认情况下，应用程序在运行 Fabric.exe 程序的帐户之下运行。 应用程序也可以以其他帐户运行，必须在 Principals 节中声明。 如果向服务应用 RunAs 策略务，而服务清单声明具有 HTTP 协议的终结点资源，则还必须指定 SecurityAccessPolicy，以确保分配给这些终结点的端口都已针对用来运行服务的 RunAs 用户帐户正确列入访问控制列表中。 对于 HTTPS 终结点，还必须定义 EndpointBindingPolicy，用于指示要返回给客户端的证书名称。 有关详细信息，请参阅 [RunAsPolicy 元素](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices 元素
声明每当一个应用程序依据此应用程序类型进行实例化时自动创建的服务实例。 有关详细信息，请参阅 [DefaultServices 元素](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Service 元素
声明在实例化应用程序时要自动创建的服务。 有关详细信息，请参阅 [Service 元素](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>StatefulService 元素
定义有状态服务。 有关详细信息，请参阅 [StatefulService 元素](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService 元素
定义无状态服务。 有关详细信息，请参阅 [StatelessService 元素](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Principals 元素
描述此应用程序运行服务和安全资源所需的安全主体（用户、组）。 主体在 policies 节中引用。 有关详细信息，请参阅 [Principals 元素](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Groups 元素
将组集声明为可在策略中引用的安全主体。 如果不同的服务入口点对应有多个用户，而且这些用户需要拥有特定的常见组级别权限，则组就很有用。 有关详细信息，请参阅 [Groups 元素](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Group 元素
将组声明为可在策略中引用的安全主体。 有关详细信息，请参阅 [Group 元素](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Membership 元素
 有关详细信息，请参阅 [Membership 元素](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup 元素
 有关详细信息，请参阅 [SystemGroup 元素](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Users 元素
将一组用户声明为可在策略中引用的安全主体。 有关详细信息，请参阅 [Users 元素](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>User 元素
将用户声明为可在策略中引用的安全主体。 有关详细信息，请参阅 [User 元素](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf 元素
用户可以添加到任何现有的成员身份组中，因此它可以继承所有属性和该成员身份组的安全设置。 成员身份组可用来保护需要按不同的服务或相同的服务（在不同计算机上）访问的外部资源。 有关详细信息，请参阅 [MemberOf 元素](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup 元素
要将用户添加到的系统组。  必须在 Groups 节中定义该系统组。 有关详细信息，请参阅 [SystemGroup 元素](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Group 元素
要将用户添加到的组。  必须在 Groups 节中定义该组。 有关详细信息，请参阅 [Group 元素](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Policies 元素
描述要在应用程序级别应用的策略（日志集合、默认运行方式帐户、运行状况和安全访问）。 有关详细信息，请参阅 [Policies 元素](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy 元素
针对在 ServiceManifestImport 节中未定义特定 RunAsPolicy 的所有服务代码包指定默认用户帐户。 有关详细信息，请参阅 [DefaultRunAsPolicy 元素](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>VotingWeb 服务清单元素
### <a name="servicemanifest-element"></a>ServiceManifest 元素
以声明方式描述服务类型和版本。 它列出组成一个服务包以支持一个或多个服务类型的独立可升级的代码、配置和数据包。 此外，还指定资源、诊断设置和服务元数据，例如服务类型、运行状况属性和负载均衡指标。 有关详细信息，请参阅 [ServiceManifest 元素](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes 元素
定义此清单中的 CodePackage 支持哪些服务类型。 当一种服务针对这些服务类型之一进行实例化时，可激活此清单中声明的所有代码包，方法是运行这些代码包的入口点。 在清单级别而不是代码包级别声明服务类型。 有关详细信息，请参阅 [ServiceTypes 元素](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType 元素
描述无状态服务类型。 有关详细信息，请参阅 [StatelessServiceType 元素](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage 元素
描述支持定义的服务类型的代码包。 当一种服务针对这些服务类型之一进行实例化时，可激活此清单中声明的所有代码包，方法是运行这些代码包的入口点。 生成的进程应在运行时注册所支持的服务类型。 当存在多个代码包时，每当系统查找任何一种声明的服务类型时，它们都会被激活。 有关详细信息，请参阅 [CodePackage 元素](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint 元素
一个特权入口点，默认情况下，以与 Service Fabric（通常是 NETWORKSERVICE 帐户）相同的凭据先于任何其他入口点运行。 EntryPoint 指定的可执行文件通常是长时间运行的服务主机。 提供单独的设置入口点可避免长时间使用高特权运行服务主机。 有关详细信息，请参阅 [SetupEntryPoint 元素](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost 元素
 有关详细信息，请参阅 [ExeHost 元素](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Program 元素
可执行文件名称。  例如，“MySetup.bat”或“MyServiceHost.exe”。 有关详细信息，请参阅 [Program 元素](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Arguments 元素
 有关详细信息，请参阅 [Arguments 元素](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder 元素
部署应用程序时所在群集节点上的代码包中的进程工作目录。 可以指定三个值：Work（默认值）、CodePackage 或 CodeBase。 CodeBase 指定将工作目录设置为在代码包中定义 EXE 时所在的目录。 CodePackage 将工作目录设置为代码包的根目录，不管在代码包目录中定义 EXE 时的位置如何。 Work 将工作目录设置为在节点上创建的唯一文件夹。  整个应用程序实例的此文件夹是同一个文件夹。 默认情况下，应用程序中所有进程的工作目录设置为应用程序工作文件夹。 这是允许进程写入数据的位置。 建议不要在代码包或代码库中写入数据，因为这些文件夹可能是在不同的应用程序实例之间共享的，可能会被删除。 有关详细信息，请参阅 [WorkingFolder 元素](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection 元素

> [!WARNING]
> 请勿在生产应用程序中使用控制台重定向，只能将其用于本地开发和调试。 请在部署和运行应用程序时所在的群集节点上，将控制台输出从启动脚本重定向到名为“log”的应用程序文件夹中的一个输出文件。 有关详细信息，请参阅 [ConsoleRedirection 元素](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint 元素
EntryPoint 指定的可执行文件通常是长时间运行的服务主机。 提供单独的设置入口点可避免长时间使用高特权运行服务主机。 由 EntryPoint 指定的可执行文件在 SetupEntryPoint 成功退出后运行。 如果总是终止或出现故障，则将监视并重启所产生的过程（再次从 SetupEntryPoint 开始）。 有关详细信息，请参阅 [EntryPoint 元素](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost 元素
 有关详细信息，请参阅 [ExeHost 元素](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage 元素
声明一个按 Name 特性命名的文件夹，该文件夹位于包含 Settings.xml 文件的 PackageRoot 中。 此文件包含进程用户定义的键值对设置，进程可在运行时读回这些设置。 升级期间，如果仅更改了 ConfigPackage 版本，则不重启正在运行的进程。 相反，回调会向进程通知配置设置已更改，以便可以重新动态加载这些设置。 有关详细信息，请参阅 [ConfigPackage 元素](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resources 元素
描述此服务使用的资源，可以在不修改已编译代码的情况下声明，并可以在部署服务时更改。 通过应用程序清单的 Principals 和 Policies 节控制对这些资源的访问。 有关详细信息，请参阅 [Resources 元素](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Endpoints 元素
定义服务的终结点。 有关详细信息，请参阅 [Endpoints 元素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint 元素
服务清单中声明的要重写的终结点。 有关详细信息，请参阅 [Endpoint 元素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>VotingData 服务清单元素
### <a name="servicemanifest-element"></a>ServiceManifest 元素
以声明方式描述服务类型和版本。 它列出组成一个服务包以支持一个或多个服务类型的独立可升级的代码、配置和数据包。 此外，还指定资源、诊断设置和服务元数据，例如服务类型、运行状况属性和负载均衡指标。 有关详细信息，请参阅 [ServiceManifest 元素](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes 元素
定义此清单中的 CodePackage 支持哪些服务类型。 当一种服务针对这些服务类型之一进行实例化时，可激活此清单中声明的所有代码包，方法是运行这些代码包的入口点。 在清单级别而不是代码包级别声明服务类型。 有关详细信息，请参阅 [ServiceTypes 元素](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType 元素
描述有状态服务类型。 有关详细信息，请参阅 [StatefulServiceType 元素](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage 元素
描述支持定义的服务类型的代码包。 当一种服务针对这些服务类型之一进行实例化时，可激活此清单中声明的所有代码包，方法是运行这些代码包的入口点。 生成的进程应在运行时注册所支持的服务类型。 当存在多个代码包时，每当系统查找任何一种声明的服务类型时，它们都会被激活。 有关详细信息，请参阅 [CodePackage 元素](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint 元素
EntryPoint 指定的可执行文件通常是长时间运行的服务主机。 提供单独的设置入口点可避免长时间使用高特权运行服务主机。 由 EntryPoint 指定的可执行文件在 SetupEntryPoint 成功退出后运行。 如果总是终止或出现故障，则将监视并重启所产生的过程（再次从 SetupEntryPoint 开始）。 有关详细信息，请参阅 [EntryPoint 元素](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost 元素
 有关详细信息，请参阅 [ExeHost 元素](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Program 元素
可执行文件名称。  例如，“MySetup.bat”或“MyServiceHost.exe”。 有关详细信息，请参阅 [Program 元素](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder 元素
部署应用程序时所在群集节点上的代码包中的进程工作目录。 可以指定三个值：Work（默认值）、CodePackage 或 CodeBase。 CodeBase 指定将工作目录设置为在代码包中定义 EXE 时所在的目录。 CodePackage 将工作目录设置为代码包的根目录，不管在代码包目录中定义 EXE 时的位置如何。 Work 将工作目录设置为在节点上创建的唯一文件夹。  整个应用程序实例的此文件夹是同一个文件夹。 默认情况下，应用程序中所有进程的工作目录设置为应用程序工作文件夹。 这是允许进程写入数据的位置。 建议不要在代码包或代码库中写入数据，因为这些文件夹可能是在不同的应用程序实例之间共享的，可能会被删除。 有关详细信息，请参阅 [WorkingFolder 元素](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage 元素
声明一个按 Name 特性命名的文件夹，该文件夹位于包含 Settings.xml 文件的 PackageRoot 中。 此文件包含进程用户定义的键值对设置，进程可在运行时读回这些设置。 升级期间，如果仅更改了 ConfigPackage 版本，则不重启正在运行的进程。 相反，回调会向进程通知配置设置已更改，以便可以重新动态加载这些设置。 有关详细信息，请参阅 [ConfigPackage 元素](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage 元素
声明一个按 Name 特性命名的文件夹，该文件夹位于将要由进程在运行时使用的静态数据文件所在的 PackageRoot 中。 升级服务清单中所列的任何数据包时，Service Fabric 会回收主机和支持包中指定的所有 EXE 和 DLLHOST。 有关详细信息，请参阅 [DataPackage 元素](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resources 元素
描述此服务使用的资源，可以在不修改已编译代码的情况下声明，并可以在部署服务时更改。 通过应用程序清单的 Principals 和 Policies 节控制对这些资源的访问。 有关详细信息，请参阅 [Resources 元素](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Endpoints 元素
定义服务的终结点。 有关详细信息，请参阅 [Endpoints 元素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint 元素
服务清单中声明的要重写的终结点。 有关详细信息，请参阅 [Endpoint 元素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

