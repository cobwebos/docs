---
title: "Azure Service Fabric Docker Compose 预览版"
description: "Azure Service Fabric 接受 Docker Compose 格式，因此可以更轻松地安排使用 Service Fabric 的现有容器。 这种支持目前处于预览状态。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/27/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: f4899748ee191a64156c0e2fae87c195ae4dbc8c
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# <a name="docker-compose-application-support-in-azure-service-fabric-preview"></a>Azure Service Fabric 中的 Docker Compose 应用程序支持（预览版）

Docker 使用 [docker-compose.yml](https://docs.docker.com/compose) 文件定义多容器应用程序。 为了让客户轻松地熟练使用 Docker 来安排 Azure Service Fabric 中的现有容器应用程序，我们在平台中增加了对 Docker Compose 的本机预览支持。 Service Fabric 可接受 `docker-compose.yml` 文件的版本 3 和更高版本。 

由于这种支持处于预览状态，因此仅支持一部分 Compose 指令。 例如，不支持应用程序升级。 但是，始终可以删除并部署应用程序，而不是对其进行升级。

若要使用此预览版，需要通过 Azure 门户使用预览 SDK（版本 255.255.x.x）创建群集。 

> [!NOTE]
> 此功能处于预览状态，且不受支持。

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>在 Service Fabric 上部署一个 Docker Compose 文件

以下命令创建一个 Service Fabric 应用程序（在上一示例中名为 `fabric:/TestContainerApp`），可以像对任何其他 Service Fabric 应用程序一样对该应用程序进行监视和托管。 指定的应用程序名称可用于运行状况查询。

### <a name="use-powershell"></a>使用 PowerShell

通过在 PowerShell 中运行以下命令，根据 docker-compose.yml 文件创建 Service Fabric Compose 应用程序：

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` 和 `RegistryPassword` 指容器注册表用户名和密码。 创建完应用程序后，可以使用以下命令检查其状态：

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

若要通过 PowerShell 删除 Compose 应用程序，请使用以下命令：

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

### <a name="use-azure-cli-20"></a>使用 Azure CLI 2.0

或者，可以使用以下 Azure CLI 命令：

```azurecli
az sf compose create --application-id fabric:/TestContainerApp --compose-file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```

创建应用程序后，可以使用以下命令检查其状态：

```azurecli
az sf compose status --application-id TestContainerApp [ --timeout ]
```

若要删除 Compose 应用程序，请使用以下命令：

```azurecli
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

## <a name="supported-compose-directives"></a>支持的 Compose 指令

此预览版支持 Compose 版本 3 格式中的一部分配置选项，包括以下基元：

* 服务 > 部署 > 副本
* 服务 > 部署 > 放置 > 约束
* 服务 > 部署 > 资源 > 限制
    * -cpu-shares
    * -memory
    * -memory-swap
* 服务 > 命令
* 服务 > 环境
* 服务 > 端口
* 服务 > 映像
* 服务 > 隔离（仅适用于 Windows）
* 服务 > 日志记录 > 驱动程序
* 服务 > 日志记录 > 驱动程序 > 选项
* 卷和部署 > 卷

将群集设置为强制实施 [Service Fabric 资源调控](service-fabric-resource-governance.md)中所述的资源限制。 所有其他 Docker Compose 指令均不受此预览版支持。

## <a name="servicednsname-computation"></a>ServiceDnsName 计算

如果 Compose 文件中指定的服务名称是完全限定的域名（也就是说，它包含一个句点 [.]），则由 Service Fabric 注册的 DNS 名称为 `<ServiceName>`（包含句点）。 如果不是，则应用程序名称中的每个路径段都会成为服务 DNS 名称中的域标签，其中第一个路径段成为顶级域标签。

例如，如果指定的应用程序名称为 `fabric:/SampleApp/MyComposeApp`，则 `<ServiceName>.MyComposeApp.SampleApp` 将是注册的 DNS 名称。

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Compose（实例定义）和 Service Fabric 应用程序模型（类型定义）之间的差异

docker-compose.yml 文件描述一组包括属性和配置在内的可部署容器。
例如，该文件可以包含环境变量和端口。 还可以在 docker-compose.yml 文件中指定放置约束、资源限制和 DNS 名称等部署参数。

[Service Fabric 应用程序模型](service-fabric-application-model.md)使用服务类型和应用程序类型，在此模型中，可以有相同类型的多个应用程序实例。 例如，每个客户一个应用程序实例。 此基于类型的模型支持向运行时注册相同应用程序类型的多个版本。

例如，客户 A 可以有一个使用 1.0 类型的 AppTypeA 实例化的应用程序，而客户 B 可以有另一个使用相同类型和版本实例化的应用程序。 在应用程序清单中定义应用程序类型，在创建应用程序时指定应用程序名称和部署参数。

虽然此模型具有较大灵活性，但我们还是计划支持一种更简单的基于实例的部署模型（其类型隐含在清单文件中）。 在此模型中，每个应用程序都会获取自己独立的清单。 我们将通过增加对 docker-compose.yml 的支持，实现对此模型（采用基于实例的部署格式）的预览支持。

## <a name="next-steps"></a>后续步骤

* 了解 [Service Fabric 应用程序模型](service-fabric-application-model.md)。

## <a name="related-articles"></a>相关文章

* [Service Fabric 和 Azure CLI 2.0 入门](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI 入门](service-fabric-azure-cli.md)

