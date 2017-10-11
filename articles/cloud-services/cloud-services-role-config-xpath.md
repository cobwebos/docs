---
title: "云服务角色配置 XPath 速查表 | Microsoft Docs"
description: "可在云服务角色配置中用来将设置公开为环境变量的各种 XPath 设置。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: fd6efac829d3fd9e2840362b8d2ff423add566d9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>使用 XPath 将角色配置设置公共为环境变量
在云服务辅助角色或 Web 角色服务定义文件中，可以将运行时配置值公开为环境变量。 支持以下 XPath 值（分别对应于 API 值）。

[Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) 库中也提供了这些 XPath 值。 

## <a name="app-running-in-emulator"></a>应用在模拟器中运行
指示应用正在模拟器中运行。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| 代码 |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>部署 ID
检索实例的部署 ID。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| 代码 |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>角色 ID
检索实例的当前角色 ID。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| 代码 |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>更新域
检索实例的更新域。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| 代码 |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>容错域
检索实例的容错域。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| 代码 |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>角色名称
检索实例的角色名称。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| 代码 |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>配置设置
检索指定配置设置的值。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| 代码 |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>本地存储路径
检索实例的本地存储路径。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| 代码 |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## <a name="local-storage-size"></a>本地存储大小
检索实例的本地存储大小。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| 代码 |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>终结点协议
检索实例的终结点协议。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| 代码 |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## <a name="endpoint-ip"></a>终结点 IP
获取指定终结点的 IP 地址。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| 代码 |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## <a name="endpoint-port"></a>终结点端口
检索实例的终结点端口。

| 类型 | 示例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| 代码 |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## <a name="example"></a>示例
下面是使用名为 `TestIsEmulated`、设置为 [@emulated xpath value](#app-running-in-emulator) 的环境变量创建启动任务的辅助角色示例。 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>后续步骤
了解有关 [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) 文件的详细信息。

创建 [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) 包。

为角色启用[远程桌面](cloud-services-role-enable-remote-desktop.md)。

