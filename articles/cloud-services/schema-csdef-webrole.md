---
title: Azure 云服务定义WebRole 架构 | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: e548841f334705aa71ada92c43ccde207a1f6318
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002307"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Azure 云服务定义 WebRole 架构
Azure Web 角色是针对 IIS 7 所支持的 Web 应用程序编程（例如 ASP.NET、PHP、Windows Communication Foundation 和 FastCGI）自定义的角色。

服务定义文件的默认扩展名为 .csdef。

## <a name="basic-service-definition-schema-for-a-web-role"></a>Web 角色的基本服务定义架构  
包含 Web 角色的服务定义文件的基本格式如下。

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>架构元素  
服务定义文件包含以下元素，本主题的后续部分做了详细说明：  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[设置](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[EndPoints](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[证书](#Certificates)

[证书](#Certificate)

[导入](#Imports)

[导入](#Import)

[运行时](#Runtime)

[环境](#Environment)

[变量](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[站点](#Sites)

[Site](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Bindings](#Bindings)

[Binding](#Binding)

[启动](#Startup)

[任务](#Task)

[Contents](#Contents)

[内容](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> WebRole  
`WebRole` 元素描述针对 IIS 7 和 ASP.NET 所支持的 Web 应用程序编程自定义的角色。 一个服务可以包含零个或多个 Web 角色。

下表介绍了 `WebRole` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 Web 角色的名称。 该角色的名称必须唯一。|  
|enableNativeCodeExecution|布尔值|可选。 默认值是 `true`默认启用本机代码执行和完全信任。 将此属性设置为 `false` 可禁用 Web 角色的本机代码执行，并改为使用 Azure 部分信任。|  
|vmsize|字符串|可选。 设置此值可更改分配给角色的虚拟机的大小。 默认值为 `Small`。 有关详细信息，请参阅[云服务的虚拟机大小](cloud-services-sizes-specs.md)。|  

##  <a name="ConfigurationSettings"></a> ConfigurationSettings  
`ConfigurationSettings` 元素描述 Web 角色的配置设置集合。 此元素是 `Setting` 元素的父级。

##  <a name="Setting"></a> Setting  
`Setting` 元素描述用于为角色实例指定配置设置的名称和值对。

下表介绍了 `Setting` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 配置设置的唯一名称。|  

角色的配置设置是在服务定义文件中声明并在服务配置文件中设置的名称-值对。

##  <a name="LocalResources"></a> LocalResources  
`LocalResources` 元素描述 Web 角色的本地存储资源集。 此元素是 `LocalStorage` 元素的父级。

##  <a name="LocalStorage"></a> LocalStorage  
`LocalStorage` 元素标识用于在运行时为服务提供文件系统空间的本地存储资源。 一个角色可以定义零个或多个本地存储资源。

> [!NOTE]
>  `LocalStorage` 元素可显示为 `WebRole` 元素的子级，提供与早期版本的 Azure SDK 的兼容性。

下表介绍了 `LocalStorage` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 本地存储的唯一名称。|  
|cleanOnRoleRecycle|布尔值|可选。 指示重启角色时是否应清理本地存储。 默认值为 `true`。|  
|sizeInMb|int|可选。 需要为本地存储分配的存储空间量，以 MB 为单位。 如果未指定，则分配的默认存储空间为 100 MB。 可分配的最小存储空间量为 1 MB。<br /><br /> 最大的本地资源大小取决于虚拟机大小。 有关详细信息，请参阅[云服务的虚拟机大小](cloud-services-sizes-specs.md)。|  
  
分配给本地存储资源的目录的名称对应于为名称属性提供的值。

##  <a name="Endpoints"></a> Endpoints  
`Endpoints` 元素描述角色的输入（外部）、内部和实例输入终结点的集合。 此元素是 `InputEndpoint`、`InternalEndpoint` 和 `InstanceInputEndpoint` 元素的父级。

输入和内部终结点是单独分配的。 一个服务可共有 25 个输入、内部和实例输入终结点，这些终结点可在一个服务中允许存在的 25 个角色间分配。 例如，如果有 5 个角色，则可以向每个角色分配 5 个输入终结点，或者向一个角色分配 25 个输入终结点，或者可以向 25 个角色中的每个角色分配 1 个输入终结点。

> [!NOTE]
>  部署的每个角色需要一个实例。 订阅的默认预配限制为 20 个内核，因此最多可有 20 个角色实例。 如果应用程序需要的实例数比默认预配提供的数目更多，请参阅[计费、订阅管理和配额支持](https://azure.microsoft.com/support/options/)，了解有关提高配额的详细信息。

##  <a name="InputEndpoint"></a> InputEndpoint  
`InputEndpoint` 元素描述 Web 角色的外部终结点。

可以定义由 HTTP、HTTPS、UDP 和 TCP 终结点组成的多个终结点。 可以指定为输入终结点选择的任意端口号，但为服务中每个角色指定的端口号必须唯一。 例如，如果指定 Web 角色要将端口 80 用于 HTTP，将端口 443 用于 HTTPS，则之后可以指定第二个 Web 角色将端口 8080 用于 HTTP，将端口 8043 用于 HTTPS。

下表介绍了 `InputEndpoint` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 外部终结点的唯一名称。|  
|协议|字符串|必需。 外部终结点的传输协议。 对于 Web 角色，可能的值为 `HTTP`、`HTTPS`、`UDP` 或 `TCP`。|  
|port|int|必需。 外部终结点的端口。 可以指定所选的任意端口号，但为服务中每个角色指定的端口号必须唯一。<br /><br /> 可能的值介于 1 和 65535（包含在内）之间（Azure SDK 1.7 或更高版本）。|  
|证书|字符串|HTTPS 终结点需要。 由 `Certificate` 元素定义的证书的名称。|  
|localPort|int|可选。 指定用于终结点上内部连接的端口。 `localPort` 属性将终结点上的外部端口映射到角色上的内部端口。 这在以下情况下非常有用：角色必须与端口上的内部组件而非为外部公开的组件进行通信。<br /><br /> 如果未指定，则 `localPort` 的值与 `port` 属性相同。 将 `localPort` 的值设置为“*”会自动分配可使用运行时 API 发现的未分配端口。<br /><br /> 可能的值介于 1 和 65535（包含在内）之间（Azure SDK 1.7 或更高版本）。<br /><br /> 仅当使用 Azure SDK 1.3 或更高版本时，才提供 `localPort` 属性。|  
|ignoreRoleInstanceStatus|布尔值|可选。 当此属性的值设置为 `true` 时，会忽略服务的状态，并且负载均衡器不会删除该终结点。 此值设置为 `true` 对调试服务的繁忙实例非常有用。 默认值为 `false`。 **注意：** 即使角色不处于就绪状态，终结点仍可接收流量。|  
|loadBalancerProbe|字符串|可选。 与输入终结点关联的负载均衡器探测的名称。 有关详细信息，请参阅 [LoadBalancerProbe 架构](schema-csdef-loadbalancerprobe.md)。|  

##  <a name="InternalEndpoint"></a> InternalEndpoint  
`InternalEndpoint` 元素描述 Web 角色的内部终结点。 只向服务中运行的其他角色实例提供内部终结点，而不向服务外部的客户端提供。 不包含 `Sites` 元素的 Web 角色只能有一个 HTTP、UDP 或 TCP 内部终结点。

下表介绍了 `InternalEndpoint` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 内部终结点的唯一名称。|  
|协议|字符串|必需。 内部终结点的传输协议。 可能的值为 `HTTP`、`TCP`、`UDP` 或 `ANY`。<br /><br /> `ANY` 的值指定允许任何协议、任何端口。|  
|port|int|可选。 用于终结点上内部负载均衡连接的端口。 负载均衡的终结点使用两个端口。 用于公共 IP 地址的端口，以及用于专用 IP 地址的端口。 通常这些端口的设置相同，但可以选择使用不同的端口。<br /><br /> 可能的值介于 1 和 65535（包含在内）之间（Azure SDK 1.7 或更高版本）。<br /><br /> 仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Port` 属性。|  

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
`InstanceInputEndpoint` 元素描述 Web 角色的实例输入终结点。 实例输入终结点使用负载均衡器的端口转发关联到特定的角色实例。 每个实例输入终结点映射到一系列可能端口中的特定端口。 此元素是 `AllocatePublicPortFrom` 元素的父级。

仅当使用 Azure SDK 1.7 或更高版本时，才提供 `InstanceInputEndpoint` 元素。

下表介绍了 `InstanceInputEndpoint` 元素的属性。
  
| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 终结点的唯一名称。|  
|localPort|int|必需。 指定需要所有角色实例均对其侦听才能接收负载均衡器转发的传入流量的内部端口。 可能的值介于 1 和 65535（含）之间。|  
|协议|字符串|必需。 内部终结点的传输协议。 可能的值为 `udp` 或 `tcp`。 将 `tcp` 用于基于 http/https 的流量。|  
  
##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
`AllocatePublicPortFrom` 元素描述可供外部客户用来访问每个实例输入终结点的公共端口范围。 公共 (VIP) 端口号在此范围中分配，并在租户部署和更新过程中分配给每个单独的角色实例终结点。 此元素是 `FixedPortRange` 元素的父级。

仅当使用 Azure SDK 1.7 或更高版本时，才提供 `AllocatePublicPortFrom` 元素。

##  <a name="FixedPort"></a> FixedPort  
`FixedPort` 元素指定内部终结点的端口，用于在该终结点上实现负载均衡的连接。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `FixedPort` 元素。

下表介绍了 `FixedPort` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|port|int|必需。 内部终结点的端口。 其效果与将 `FixedPortRange` min 和 max 设置为相同的端口一样。<br /><br /> 可能的值介于 1 和 65535（包含在内）之间（Azure SDK 1.7 或更高版本）。|  

##  <a name="FixedPortRange"></a> FixedPortRange  
`FixedPortRange` 元素指定分配给内部终结点或实例输入终结点的端口的范围，并设置用于在终结点上建立负载均衡连接的端口。

> [!NOTE]
>  `FixedPortRange` 元素的工作方式取决于它所在的元素。 当 `FixedPortRange` 元素位于 `InternalEndpoint` 元素中时，它会为运行角色的所有虚拟机打开负载均衡器上 min 和 max 属性范围内的所有端口。 当 `FixedPortRange` 元素位于 `InstanceInputEndpoint` 元素中时，它会在运行角色的每个虚拟机上只打开 min 和 max 属性范围内的一个端口。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `FixedPortRange` 元素。

下表介绍了 `FixedPortRange` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|min|int|必需。 范围内的最小端口。 可能的值介于 1 和 65535（包含在内）之间（Azure SDK 1.7 或更高版本）。|  
|max|字符串|必需。 范围内的最大端口。 可能的值介于 1 和 65535（包含在内）之间（Azure SDK 1.7 或更高版本）。|  

##  <a name="Certificates"></a> Certificates  
`Certificates` 元素描述 Web 角色的证书集合。 此元素是 `Certificate` 元素的父级。 一个角色可以包含任意数目的关联证书。 有关使用 certificates 元素的详细信息，请参阅[使用证书修改服务定义文件](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files)。

##  <a name="Certificate"></a> Certificate  
`Certificate` 元素描述与 Web 角色关联的证书。

下表介绍了 `Certificate` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 此证书的名称，用于与 HTTPS `InputEndpoint` 元素关联时进行引用。|  
|storeLocation|字符串|必需。 本地计算机上的证书存储位置，可在其中找到此证书。 可能的值为 `CurrentUser` 和 `LocalMachine`。|  
|storeName|字符串|必需。 本地计算机上此证书所在的证书存储的名称。 可能的值包括内置存储名称 `My`、`Root`、`CA`、`Trust`、`Disallowed`、`TrustedPeople`、`TrustedPublisher`、`AuthRoot`、`AddressBook`，或任何自定义存储名称。 如果指定了自定义存储名称，则会自动创建存储。|  
|permissionLevel|字符串|可选。 指定授予角色进程的访问权限。 如果只希望提升的进程访问私钥，则指定 `elevated` 权限。 `limitedOrElevated` 权限允许所有角色进程访问私钥。 可能的值为 `limitedOrElevated` 或 `elevated`。 默认值为 `limitedOrElevated`。|  

##  <a name="Imports"></a> Imports  
`Imports` 元素描述向来宾操作系统添加组件的 Web 角色的导入模块集合。 此元素是 `Import` 元素的父级。 此元素是可选的，一个角色只能有一个 imports 块。 

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Imports` 元素。

##  <a name="Import"></a> Import  
`Import` 元素指定要添加到来宾操作系统的模块。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Import` 元素。

下表介绍了 `Import` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|moduleName|字符串|必需。 要导入的模块的名称。 有效的导入模块为：<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-   Diagnostics<br /><br /> 借助 RemoteAccess 和 RemoteForwarder 模块，可配置远程桌面连接的角色实例。 有关详细信息，请参阅[启用远程桌面连接](cloud-services-role-enable-remote-desktop-new-portal.md)。<br /><br /> 借助 Diagnostics 模块，可收集角色实例的诊断数据。|  

##  <a name="Runtime"></a> Runtime  
`Runtime` 元素描述 Web 角色的环境变量设置集合，这些设置控制 Azure 主机进程的运行时环境。 此元素是 `Environment` 元素的父级。 此元素是可选的，一个角色只能有一个运行时块。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Runtime` 元素。

下表描述 `Runtime` 元素的属性：  

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|executionContext|字符串|可选。 指定在其中启动角色进程的上下文。 默认上下文为 `limited`。<br /><br /> -   `limited` – 不使用管理员特权启动进程。<br />-   `elevated` – 需要管理员权限才能启动进程。|  

##  <a name="Environment"></a> Environment  
`Environment` 元素描述 Web 角色的环境变量设置集合。 此元素是 `Variable` 元素的父级。 一个角色可以包含任意数目的环境变量集。

##  <a name="Variable"></a> Variable  
`Variable` 元素指定要在来宾操作系统中设置的环境变量。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Variable` 元素。

下表描述 `Variable` 元素的属性：  

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 要设置的环境变量的名称。|  
|值|字符串|可选。 要为环境变量设置的值。 必须包含 value 属性或 `RoleInstanceValue` 元素。|  

##  <a name="RoleInstanceValue"></a> RoleInstanceValue  
`RoleInstanceValue` 元素指定要从其中检索变量值的 xPath。

下表介绍了 `RoleInstanceValue` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|xpath|字符串|可选。 实例的部署设置的位置路径。 有关详细信息，请参阅[带有 XPath 的配置变量](cloud-services-role-config-xpath.md)。<br /><br /> 必须包含 value 属性或 `RoleInstanceValue` 元素。|  

##  <a name="EntryPoint"></a> EntryPoint  
`EntryPoint` 元素指定角色的入口点。 此元素是 `NetFxEntryPoint` 元素的父级。 借助这些元素，可以指定默认 WaWorkerHost.exe 以外的应用程序充当角色入口点。

仅当使用 Azure SDK 1.5 或更高版本时，才提供 `EntryPoint` 元素。

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
`NetFxEntryPoint` 元素指定要为角色运行的程序。

> [!NOTE]
>  仅当使用 Azure SDK 1.5 或更高版本时，才提供 `NetFxEntryPoint` 元素。

下表介绍了 `NetFxEntryPoint` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|assemblyName|字符串|必需。 包含入口点的程序集的路径和文件名称。 该路径相对于文件夹 \\%ROLEROOT%\Approot（请勿在 `commandLine` 中指定 \\%ROLEROOT%\Approot，它是假定的）。 **%ROLEROOT%** 是由 Azure 维护的环境变量，表示角色的根文件夹位置。 **\\%ROLEROOT%\Approot** 文件夹表示角色的应用程序文件夹。<br /><br /> 对于 HWC 角色，路径始终相对于 **\\%ROLEROOT%\Approot\bin** 文件夹。<br /><br /> 对于完整版 IIS 和 IIS Express 的 Web 角色，如果在相对于 **\\%ROLEROOT%\Approot** 文件夹的位置找不到程序集，则会搜索 **\\%ROLEROOT%\Approot\bin**。<br /><br /> 完整版 IIS 的这种回退行为并不是建议的最佳做法，在以后的版本中可能被删除。|  
|targetFrameworkVersion|字符串|必需。 在其上生成程序集的 .NET Framework 的版本。 例如，`targetFrameworkVersion="v4.0"`。|  

##  <a name="Sites"></a> Sites  
`Sites` 元素描述 Web 角色中托管的网站和 Web 应用程序的集合。 此元素是 `Site` 元素的父级。 如果不指定 `Sites` 元素，则会以传统 Web 角色的形式托管 Web 角色，并且 Web 角色中只能托管一个网站。 此元素是可选的，一个角色只能有一个 sites 块。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Sites` 元素。

##  <a name="Site"></a> Site  
`Site` 元素指定属于 Web 角色的一部分的网站或 Web 应用程序。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Site` 元素。

下表介绍了 `Site` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 网站或应用程序的名称。|  
|physicalDirectory|字符串|站点根的内容目录的位置。 可将该位置指定为绝对路径或相对于 .csdef 位置。|  

##  <a name="VirtualApplication"></a> VirtualApplication  
`VirtualApplication` 元素定义 Internet Information Services (IIS) 7 中的某个应用程序是通过 HTTP 等协议交付内容或提供服务的文件组。 在 IIS 7 中创建应用程序时，该应用程序的路径将成为站点 URL 的一部分。

仅当使用 Azure SDK 1.3 或更高版本时，才可以使用 `VirtualApplication` 元素。

下表介绍了 `VirtualApplication` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 指定用于标识虚拟应用程序的名称。|  
|physicalDirectory|字符串|必需。 指定包含虚拟应用程序的开发计算机上的路径。 在计算模拟器中，IIS 已配置为从此位置检索内容。 部署到 Azure 时，物理目录的内容会连同服务的余下部分一起打包。 将服务包部署到 Azure 后，会使用未打包内容的位置配置 IIS。|  

##  <a name="VirtualDirectory"></a> VirtualDirectory  
`VirtualDirectory` 元素指定在 IIS 中指定的目录名称（也称为路径），并映射到本地或远程服务器上的物理目录。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `VirtualDirectory` 元素。

下表介绍了 `VirtualDirectory` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 指定用于标识虚拟目录的名称。|  
|值|physicalDirectory|必需。 指定包含网站或虚拟目录内容的开发计算机上的路径。 在计算模拟器中，IIS 已配置为从此位置检索内容。 部署到 Azure 时，物理目录的内容会连同服务的余下部分一起打包。 将服务包部署到 Azure 后，会使用未打包内容的位置配置 IIS。|  

##  <a name="Bindings"></a> Bindings  
`Bindings` 元素描述网站的绑定集合。 它是 `Binding` 元素的父元素。 每个 `Site` 元素都需要此元素。 有关配置终结点的详细信息，请参阅[为角色实例启用通信](cloud-services-enable-communication-role-instances.md)。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Bindings` 元素。

##  <a name="Binding"></a> Binding  
`Binding` 元素指定请求与网站或 Web 应用程序通信时所需的配置信息。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Binding` 元素。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|名称|字符串|必需。 指定用于标识绑定的名称。|  
|endpointName|字符串|必需。 指定要绑定到的终结点名称。|  
|hostHeader|字符串|可选。 指定允许在单个 IP 地址/端口号组合中托管多个具有不同主机名的站点的主机名。|  

##  <a name="Startup"></a> Startup  
`Startup` 元素描述角色启动时运行的任务的集合。 此元素可以是 `Variable` 元素的父级。 有关使用角色启动任务的详细信息，请参阅[如何配置启动任务](cloud-services-startup-tasks.md)。 此元素是可选的，一个角色只能有一个启动块。

下表描述 `Startup` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|priority|int|仅供内部使用。|  

##  <a name="Task"></a> Task  
`Task` 元素指定在角色启动时发生的启动任务。 启动任务可用于执行准备角色以运行的任务，如安装软件组件或运行其他应用程序。 这些任务会按照它们在 `Startup` 元素块中出现的顺序执行。

仅当使用 Azure SDK 1.3 或更高版本时，才提供 `Task` 元素。

下表介绍了 `Task` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|commandLine|字符串|必需。 包含要运行的命令的脚本（如 CMD 文件）。 启动命令和批处理文件必须以 ANSI 格式保存。 在文件开头设置字节顺序标记的文件格式无法得到正确处理。|  
|executionContext|字符串|指定在其中运行脚本的上下文。<br /><br /> -   `limited` [默认] – 使用与托管进程的角色相同的特权运行。<br />-   `elevated` – 使用管理员特权运行。|  
|taskType|字符串|指定命令的执行行为。<br /><br /> -   `simple` [默认] – 系统等待任务退出，然后再启动其他所有任务。<br />-   `background` – 系统不会等待任务退出。<br />-   `foreground` – 与后台相似，但所有前台任务全部退出之前，不会重启角色。|  

##  <a name="Contents"></a> Contents  
`Contents` 元素描述 Web 角色的内容集合。 此元素是 `Content` 元素的父级。

仅当使用 Azure SDK 1.5 或更高版本时，才提供 `Contents` 元素。

##  <a name="Content"></a> Content  
`Content` 元素定义要复制到 Azure 虚拟机的内容的源位置，以及要复制到的目标路径。

仅当使用 Azure SDK 1.5 或更高版本时，才提供 `Content` 元素。

下表介绍了 `Content` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|目标|字符串|必需。 内容放置在 Azure 虚拟机上的位置。 此位置相对于文件夹 %ROLEROOT%\Approot。|  

此元素是 `SourceDirectory` 元素的父元素。

##  <a name="SourceDirectory"></a> SourceDirectory  
`SourceDirectory` 元素定义要从中复制内容的本地目录。 使用此元素指定要复制到 Azure 虚拟机的本地内容。

仅当使用 Azure SDK 1.5 或更高版本时，才提供 `SourceDirectory` 元素。

下表介绍了 `SourceDirectory` 元素的属性。

| 属性 | Type | Description |  
| --------- | ---- | ----------- |  
|路径|字符串|必需。 内容将复制到 Azure 虚拟机的本地目录的相对或绝对路径。 支持扩展目录路径中的环境变量。|  
  
## <a name="see-also"></a>另请参阅
[云服务（经典）定义架构](schema-csdef-file.md)
