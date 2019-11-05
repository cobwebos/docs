---
title: Azure 连接的计算机代理 CLI 接口
description: Azure 连接的计算机代理 CLI 的参考文档
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513193"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure 连接的计算机代理 CLI 接口

`Azcmagent` （Azure 连接的计算机代理）工具用于配置与 Azure 的非 azure 计算机连接并对其进行故障排除。

代理本身是在 Linux 上称为 `himdsd` 的后台程序进程，以及 Windows 上名为 `himds` 的 Windows 服务。

在正常使用中，`azcmagent connect` 用于建立此计算机与 Azure 之间的连接，并且 `azcmagent disconnect` 确定不再需要该连接。 其他命令用于疑难解答或其他特殊情况。

## <a name="options"></a>选项

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>另请参阅

* [azcmagent connect-将](#azcmagent-connect)此计算机连接到 Azure
* [azcmagent 断开](#azcmagent-disconnect)-从 Azure 断开连接此计算机
* [重新连接 azcmagent](#azcmagent-reconnect) -将此计算机重新连接到 Azure
* [azcmagent show](#azcmagent-show) -获取计算机元数据和代理状态。 这主要用于排查问题。
* [azcmagent 版本](#azcmagent-version)-显示混合管理代理版本

## <a name="azcmagent-connect"></a>azcmagent 连接

将此计算机连接到 Azure

### <a name="synopsis"></a>概要

在 Azure 中创建表示此计算机的资源。

这会使用提供的身份验证选项在 Azure 中创建资源，资源管理器表示此计算机。 资源位于请求的订阅和资源组中，计算机上的数据存储在 location 参数指定的 Azure 区域中。
如果未重写，则默认资源名称是此计算机的主机名。

然后，将下载和存储此计算机的系统分配标识的证书。 此步骤完成后， **Azure 连接的计算机元数据**服务和来宾配置代理开始与 azure 云同步。

身份验证选项：

* 访问令牌 `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* 服务主体 ID 和机密 `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* 设备登录（交互） `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>azcmagent 断开连接

从 Azure 断开连接此计算机

### <a name="synopsis"></a>概要

删除代表此服务器的 Azure 中的资源。

此命令使用提供的身份验证选项来删除代表此计算机的 Azure 资源管理器资源。 此后， **Azure 连接的计算机元数据服务**和来宾配置代理将断开连接。 此命令不会停止或删除服务：删除包，以便执行此操作。

此命令需要比 "Azure 连接的计算机载入" 角色更高的特权。

计算机断开连接后，如果想要在 Azure 中为其创建新资源，请使用 `azcmagent connect`，而不是 `azcmagent reconnect`。

身份验证选项：

* 访问令牌 `azcmagent disconnect --access-token <>`
* 服务主体 ID 和机密 `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 交互式设备登录 `azcmagent disconnect --tenant-id <>`

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

## <a name="azcmagent-reconnect"></a>重新连接 azcmagent

将此计算机重新连接到 Azure

### <a name="synopsis"></a>概要

将具有无效凭据的计算机重新连接到 Azure。

如果计算机已有 Azure 中的资源，但无法对其进行身份验证，则可以使用此命令进行重新连接。 如果计算机关闭的时间足以使其证书过期（至少45天），则可能会发生这种情况。

如果计算机与 `azcmagent disconnect`断开连接，请改用 `azcmagent connect`。

此命令使用提供的身份验证选项来检索与表示此计算机的 Azure 资源管理器资源相对应的新凭据。

此命令需要比**Azure 连接的计算机加入**角色更高的特权。

身份验证选项

* 访问令牌 `azcmagent reconnect --access-token <>`
* 服务主体 ID 和机密 `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 交互式设备登录 `azcmagent reconnect --tenant-id <>`

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

## <a name="azcmagent-show"></a>azcmagent show

获取计算机元数据和代理状态。 这主要用于排查问题。

### <a name="synopsis"></a>概要

获取计算机元数据和代理状态。 这主要用于排查问题。


### <a name="syntax"></a>语法

```
azcmagent show [flags]
```

### <a name="options"></a>选项

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent 版本

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
