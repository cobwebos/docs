---
title: "Azure Service Fabric 和 Azure CLI 2.0 入门"
description: "了解如何在 Azure CLI 2.0 版中使用 Azure Service Fabric 命令模块。 了解如何连接到群集以及如何管理应用程序。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Azure Service Fabric 和 Azure CLI 2.0

Azure 命令行工具 (Azure CLI) 2.0 版包括有助于管理 Azure Service Fabric 群集的命令。 了解 Azure CLI 和 Service Fabric 的入门方式。

## <a name="install-azure-cli-20"></a>安装 Azure CLI 2.0

可以使用 Azure CLI 2.0 命令与 Service Fabric 群集交互并对其进行管理。 若要获取最新版的 Azure CLI，请按 [Azure CLI 2.0 标准安装过程](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)操作。

有关详细信息，请参阅 [Azure CLI 2.0 概述](https://docs.microsoft.com/en-us/cli/azure/overview)。

## <a name="azure-cli-syntax"></a>Azure CLI 语法

在 Azure CLI 中，所有 Service Fabric 命令都带 `az sf` 前缀。 有关可以使用的命令的一般信息，请使用 `az sf -h`。 有关单个命令的帮助，请使用 `az sf <command> -h`。

Azure CLI 中的 Service Fabric 命令遵循以下命名模式：

```azurecli
az sf <object> <action>
```

`<object>` 是 `<action>` 的目标。

## <a name="select-a-cluster"></a>选择群集

在执行任何操作之前，必须选择要连接到的群集。 有关示例，请参阅以下代码。 该代码连接到不安全的群集。

> [!WARNING]
> 请勿将不安全的 Service Fabric 群集用在生产环境中。

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

群集终结点必须以 `http` 或 `https` 为前缀。 它必须包括 HTTP 网关的端口。 此端口和地址与 Service Fabric Explorer URL 相同。

对于使用证书进行保护的群集，可以使用未加密的 .pem 文件，也可以使用 .crt 和 .key 文件。 例如：

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

有关详细信息，请参阅[连接到安全的 Azure Service Fabric 群集](service-fabric-connect-to-secure-cluster.md)。

> [!NOTE]
> `select` 命令在返回之前不处理任何请求。 若要验证是否已正确指定群集，请使用 `az sf cluster health` 之类的命令。 验证该命令是否不返回任何错误。

## <a name="basic-operations"></a>基本操作

系统会在多个 Azure CLI 会话中保留群集连接信息。 选择 Service Fabric 群集以后，即可在群集上运行任何 Service Fabric 命令。

例如，若要获取 Service Fabric 群集运行状况，请使用以下命令：

```azurecli
az sf cluster health
```

该命令生成以下输出（假定在 Azure CLI 配置中指定了 JSON 输出）：

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>提示和故障排除

在 Azure CLI 中使用 Service Fabric 命令时，如果遇到问题，可参阅以下信息。

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>将证书从 PFX 转换为 PEM 格式

Azure CLI 支持 PEM（.pem 扩展名）文件形式的客户端证书。 如果使用 Windows 的 PFX 文件，则必须将这些证书转换为 PEM 格式。 若要将 PFX 文件转换为 PEM 文件，请使用以下命令：

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

有关详细信息，请参阅 [OpenSSL 文档](https://www.openssl.org/docs/)。

### <a name="connection-issues"></a>连接问题

某些操作可能会生成以下消息：

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

验证指定的群集终结点是否可用且正进行侦听。 另请验证是否可以在该主机和端口上使用 Service Fabric Explorer UI。 若要更新终结点，请使用 `az sf cluster select`。

### <a name="detailed-logs"></a>详细日志

调试或报告某个问题时，详细日志通常很有用。 Azure CLI 提供一个全局的 `--debug` 标志，可提高日志文件的详细程度。

### <a name="command-help-and-syntax"></a>命令帮助和语法

Service Fabric 命令与 Azure CLI 命令遵循相同的约定。 如需某个特定命令或一组命令的帮助，请使用 `-h` 标志：

```azurecli
az sf application -h
```

以下是另一示例：

```azurecli
az sf application create -h
```

