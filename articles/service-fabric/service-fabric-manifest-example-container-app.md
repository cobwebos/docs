---
title: Azure Service Fabric 容器应用程序清单示例 | Microsoft Docs
description: 了解如何为多容器 Service Fabric 应用程序配置应用程序和服务清单设置。
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 622e6f7552d91cdb9ccf3668c302496c68a5920f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719237"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>多容器应用程序和服务清单示例
下面是多容器 Service Fabric 应用程序的应用程序和服务清单示例。 这些示例的用途是展示有哪些设置可用以及如何使用它们。 这些应用程序和服务清单基于 [Windows Server 2016 容器示例](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows)清单。

展示了以下功能：

|清单|功能|
|---|---|
|[应用程序清单](#application-manifest)| [替代环境变量](service-fabric-get-started-containers.md#configure-and-set-environment-variables)、[配置容器端口到主机映射](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)、[配置容器注册表身份验证](service-fabric-get-started-containers.md#configure-container-registry-authentication)、[资源调控](service-fabric-resource-governance.md)、[设置隔离模式](service-fabric-get-started-containers.md#configure-isolation-mode)、[指定 OS 内部版本特定的容器映像](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService 服务清单](#frontendservice-service-manifest)| [设置环境变量](service-fabric-get-started-containers.md#configure-and-set-environment-variables)、[配置终结点](service-fabric-get-started-containers.md#configure-communication)、将命令传递给容器、[将证书导入到容器中](service-fabric-securing-containers.md)| 
|[BackEndService 服务清单](#backendservice-service-manifest)|[设置环境变量](service-fabric-get-started-containers.md#configure-and-set-environment-variables)、[配置终结点](service-fabric-get-started-containers.md#configure-communication)、[配置卷驱动程序](service-fabric-containers-volume-logging-drivers.md)| 

有关特定 XML 元素的详细信息，请参阅[应用程序清单元素](#application-manifest-elements)、[FrontEndService 服务清单元素](#frontendservice-service-manifest-elements)和 [BackEndService 服务清单元素](#backendservice-service-manifest-elements)。

## <a name="application-manifest"></a>应用程序清单

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService 服务清单

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService 服务清单

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Policies 元素
描述要在导入的服务清单中应用的策略（终结点绑定、包共享、运行方式和安全访问权限）。 有关详细信息，请参阅 [Policies 元素](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy 元素
定义在整个服务包级别应用的资源调控策略。 有关详细信息，请参阅 [ServicePackageResourceGovernancePolicy 元素](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy 元素
指定代码包的资源限制。 有关详细信息，请参阅 [ResourceGovernancePolicy 元素](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies 元素
指定用于激活容器主机的策略。 有关详细信息，请参阅 [ContainerHostPolicies 元素](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials 元素
要从中提取映像的容器映像存储库的凭据。 有关详细信息，请参阅 [RepositoryCredentials 元素](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>PortBinding 元素
指定要绑定到公开的容器端口的终结点资源。 有关详细信息，请参阅 [PortBinding 元素](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Volume 元素
指定要绑定到容器的卷。 有关详细信息，请参阅 [Volume 元素](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption 元素
要传递给驱动程序的驱动程序选项。 有关详细信息，请参阅 [DriverOption 元素](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides 元素
Windows Server 容器在不同 OS 版本中可能不兼容。  可以为每个容器指定多个 OS 映像，并使用 OS 的内部版本来标记它们。 通过在 Windows 命令提示符下运行“winver”获取 OS 的内部版本。 如果基础 OS 为内部版本 16299（Windows Server 版本 1709），Service Fabric 会选取带有 Os="16299" 标记的容器映像。 可以认为未标记的容器映像适合所有版本的 OS，并且会替代在服务清单中指定的映像。 有关详细信息，请参阅 [ImageOverrides 元素](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Image 元素
容器映像，对应于要启动的 OS 内部版本号。 如果 Os 属性未指定，则认为容器映像适合所有版本的 OS，并且会替代在服务清单中指定的映像。 有关详细信息，请参阅 [Image 元素](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides 元素
 有关详细信息，请参阅 [EnvironmentOverrides 元素](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>EnvironmentVariable 元素
环境变量。 有关详细信息，请参阅 [EnvironmentVariable 元素](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef 元素
指定要向容器环境公开的 X509 证书的相关信息。 该证书必须安装在所有群集节点的 LocalMachine 存储中。
应用程序启动时，运行时读取证书并生成 PFX 文件和密码（在 Windows 上）或 PEM 文件（在 Linux 上）。
可在容器内使用 Certificates_ServicePackageName_CodePackageName_CertName_PFX 和 Certificates_ServicePackageName_CodePackageName_CertName_Password 环境变量访问此 PFX 文件和密码。 可在容器内使用 Certificates_ServicePackageName_CodePackageName_CertName_PEM 和 Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey 环境变量访问此 PEM 文件。 有关详细信息，请参阅 [CertificateRef 元素](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices 元素
声明每当一个应用程序依据此应用程序类型进行实例化时自动创建的服务实例。 有关详细信息，请参阅 [DefaultServices 元素](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Service 元素
声明在实例化应用程序时要自动创建的服务。 有关详细信息，请参阅 [Service 元素](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>StatelessService 元素
定义无状态服务。 有关详细信息，请参阅 [StatelessService 元素](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService 服务清单元素
### <a name="servicemanifest-element"></a>ServiceManifest 元素
以声明方式描述服务类型和版本。 它列出组成一个服务包以支持一个或多个服务类型的独立可升级的代码、配置和数据包。 此外，还指定资源、诊断设置和服务元数据，例如服务类型、运行状况属性和负载均衡指标。 有关详细信息，请参阅 [ServiceManifest 元素](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes 元素
定义此清单中的 CodePackage 支持哪些服务类型。 当一种服务针对这些服务类型之一进行实例化时，可激活此清单中声明的所有代码包，方法是运行这些代码包的入口点。 在清单级别而不是代码包级别声明服务类型。 有关详细信息，请参阅 [ServiceTypes 元素](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType 元素
描述无状态服务类型。 有关详细信息，请参阅 [StatelessServiceType 元素](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage 元素
描述支持定义的服务类型的代码包。 当一种服务针对这些服务类型之一进行实例化时，可激活此清单中声明的所有代码包，方法是运行这些代码包的入口点。 生成的进程应在运行时注册所支持的服务类型。 当存在多个代码包时，每当系统查找任何一种声明的服务类型时，它们都会被激活。 有关详细信息，请参阅 [CodePackage 元素](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint 元素
EntryPoint 指定的可执行文件通常是长时间运行的服务主机。 提供单独的设置入口点可避免长时间使用高特权运行服务主机。 由 EntryPoint 指定的可执行文件在 SetupEntryPoint 成功退出后运行。 如果总是终止或出现故障，则将监视并重启所产生的过程（再次从 SetupEntryPoint 开始）。 有关详细信息，请参阅 [EntryPoint 元素](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost 元素
 有关详细信息，请参阅 [ContainerHost 元素](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName 元素
https://hub.docker.com 或 Azure 容器注册表上的存储库和映像。 有关详细信息，请参阅 [ImageName 元素](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables 元素
将环境变量传递给容器或 exe。  有关详细信息，请参阅 [EnvironmentVariables 元素](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable 元素
环境变量。 有关详细信息，请参阅 [EnvironmentVariable 元素](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage 元素
声明一个由 Name 属性命名的文件夹，该文件夹中包含 Settings.xml 文件。 此文件包含进程用户定义的键值对设置，进程可在运行时读回这些设置。 升级期间，如果仅更改了 ConfigPackage 版本，则不重启正在运行的进程。 相反，回调会向进程通知配置设置已更改，以便可以重新动态加载这些设置。 有关详细信息，请参阅 [ConfigPackage 元素](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage 元素
声明一个由 Name 属性命名的文件夹，该文件夹中包含静态数据文件。 升级服务清单中所列的任何数据包时，Service Fabric 会回收主机和支持包中指定的所有 EXE 和 DLLHOST。 有关详细信息，请参阅 [DataPackage 元素](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resources 元素
描述此服务使用的资源，可以在不修改已编译代码的情况下声明，并可以在部署服务时更改。 通过应用程序清单的 Principals 和 Policies 节控制对这些资源的访问。 有关详细信息，请参阅 [Resources 元素](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Endpoints 元素
定义服务的终结点。 有关详细信息，请参阅 [Endpoints 元素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint 元素
有关详细信息，请参阅 [Endpoint 元素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>BackEndService 服务清单元素
### <a name="servicemanifest-element"></a>ServiceManifest 元素
以声明方式描述服务类型和版本。 它列出组成一个服务包以支持一个或多个服务类型的独立可升级的代码、配置和数据包。 此外，还指定资源、诊断设置和服务元数据，例如服务类型、运行状况属性和负载均衡指标。 有关详细信息，请参阅 [ServiceManifest 元素](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes 元素
定义此清单中的 CodePackage 支持哪些服务类型。 当一种服务针对这些服务类型之一进行实例化时，可激活此清单中声明的所有代码包，方法是运行这些代码包的入口点。 在清单级别而不是代码包级别声明服务类型。 有关详细信息，请参阅 [ServiceTypes 元素](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType 元素
描述无状态服务类型。 有关详细信息，请参阅 [StatelessServiceType 元素](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage 元素
描述支持定义的服务类型的代码包。 当一种服务针对这些服务类型之一进行实例化时，可激活此清单中声明的所有代码包，方法是运行这些代码包的入口点。 生成的进程应在运行时注册所支持的服务类型。 当存在多个代码包时，每当系统查找任何一种声明的服务类型时，它们都会被激活。 有关详细信息，请参阅 [CodePackage 元素](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint 元素
EntryPoint 指定的可执行文件通常是长时间运行的服务主机。 提供单独的设置入口点可避免长时间使用高特权运行服务主机。 由 EntryPoint 指定的可执行文件在 SetupEntryPoint 成功退出后运行。 如果总是终止或出现故障，则将监视并重启所产生的过程（再次从 SetupEntryPoint 开始）。 有关详细信息，请参阅 [EntryPoint 元素](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost 元素
有关详细信息，请参阅 [ContainerHost 元素](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName 元素
https://hub.docker.com 或 Azure 容器注册表上的存储库和映像。 有关详细信息，请参阅 [ImageName 元素](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Commands 元素
将以逗号分隔的命令列表传递给容器。 有关详细信息，请参阅 [Commands 元素](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables 元素
将环境变量传递给容器或 exe。  有关详细信息，请参阅 [EnvironmentVariables 元素](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable 元素
环境变量。 有关详细信息，请参阅 [EnvironmentVariable 元素](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage 元素
声明一个由 Name 属性命名的文件夹，该文件夹中包含 Settings.xml 文件。 此文件包含进程用户定义的键值对设置，进程可在运行时读回这些设置。 升级期间，如果仅更改了 ConfigPackage 版本，则不重启正在运行的进程。 相反，回调会向进程通知配置设置已更改，以便可以重新动态加载这些设置。 有关详细信息，请参阅 [ConfigPackage 元素](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resources 元素
描述此服务使用的资源，可以在不修改已编译代码的情况下声明，并可以在部署服务时更改。 通过应用程序清单的 Principals 和 Policies 节控制对这些资源的访问。 有关详细信息，请参阅 [Resources 元素](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Endpoints 元素
定义服务的终结点。 有关详细信息，请参阅 [Endpoints 元素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Endpoint 元素
 有关详细信息，请参阅 [Endpoint 元素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

