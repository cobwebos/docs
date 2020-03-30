---
title: Azure 连接的计算机代理 CLI 接口
description: Azure 连接计算机代理 CLI 的参考文档
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513193"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure 连接的计算机代理 CLI 接口

`Azcmagent` （Azure 连接的计算机代理）工具用于配置和排除与 Azure 连接的非 azure 计算机。

代理本身是在 Linux 上调用`himdsd`的守护进程，以及 Windows 上`himds`调用的 Windows 服务。

在正常使用中，`azcmagent connect`用于在此计算机和 Azure 之间建立连接，`azcmagent disconnect`如果您决定不再需要该连接。 其他命令用于故障排除或其他特殊情况。

## <a name="options"></a>选项

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>另请参阅

* [阿兹cmagent连接](#azcmagent-connect)- 将此计算机连接到 Azure
* [阿兹cmagent断开连接](#azcmagent-disconnect)- 断开此计算机与 Azure 的连接
* [阿兹cmagent重新连接](#azcmagent-reconnect)- 将此计算机重新连接到 Azure
* [阿兹cmagent 显示](#azcmagent-show)- 获取计算机元数据和代理状态。 这主要可用于故障排除。
* [阿兹cmagent版本](#azcmagent-version)- 显示混合管理代理版本

## <a name="azcmagent-connect"></a>阿兹cmagent连接

将此计算机连接到 Azure

### <a name="synopsis"></a>概要

在 Azure 中创建表示此计算机的资源。

这将使用提供的身份验证选项在 Azure 资源管理器中创建表示此计算机的资源。 资源位于请求的订阅和资源组中，有关计算机的数据存储在位置参数指定的 Azure 区域中。
默认资源名称是此计算机的主机名（如果不是重写）。

然后，将下载与此计算机的系统分配标识对应的证书，并将其存储在本地。 完成此步骤后 **，Azure 连接的计算机元数据**服务和来宾配置代理开始与 Azure 云同步。

身份验证选项：

* 访问令牌`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* 服务主体 ID 和机密`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* 设备登录（交互式）`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>语法

```none
azcmagent connect [flags]
```

### <a name="options"></a>选项

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>阿兹cmagent断开

断开此计算机与 Azure 的连接

### <a name="synopsis"></a>概要

删除 Azure 中表示此服务器的资源。

此命令使用提供的身份验证选项来删除表示此计算机的 Azure 资源管理器资源。 在此之后，将断开**Azure 连接的计算机元数据服务和**来宾配置代理。 此命令不会停止或删除服务：删除包以便执行此操作。

此命令需要比"Azure 连接的计算机载入"角色更高的权限。

断开计算机后，如果要在`azcmagent connect`Azure`azcmagent reconnect`中为其创建新资源，请使用 。

身份验证选项：

* 访问令牌`azcmagent disconnect --access-token <>`
* 服务主体 ID 和机密`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 交互式设备登录`azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>语法

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>选项

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>阿兹cmagent重新连接

将此计算机重新连接到 Azure

### <a name="synopsis"></a>概要

将凭据无效的计算机重新连接到 Azure。

如果计算机已在 Azure 中具有资源，但无法对其进行身份验证，则可以使用此命令重新连接它。 如果计算机关闭足够长的时间使其证书过期（至少 45 天），则可以这样做。

如果计算机与`azcmagent disconnect`断开连接，则改`azcmagent connect`用。

此命令使用提供的身份验证选项来检索对应于表示此计算机的 Azure 资源管理器资源的新凭据。

此命令需要比**Azure 连接的计算机载入**角色更高的权限。

身份验证选项

* 访问令牌`azcmagent reconnect --access-token <>`
* 服务主体 ID 和机密`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 交互式设备登录`azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>语法

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>选项

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>阿兹cmagent显示

获取计算机元数据和代理状态。 这主要可用于故障排除。

### <a name="synopsis"></a>概要

获取计算机元数据和代理状态。 这主要可用于故障排除。


### <a name="syntax"></a>语法

```
azcmagent show [flags]
```

### <a name="options"></a>选项

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>阿兹cmagent版本

显示混合管理代理版本

### <a name="synopsis"></a>概要

显示混合管理代理版本

### <a name="syntax"></a>语法

```none
azcmagent version [flags]
```

### <a name="options"></a>选项

```none
  -h, --help   help for version
```
