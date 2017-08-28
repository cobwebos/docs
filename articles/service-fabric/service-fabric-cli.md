---
title: "Azure Service Fabric CLI (sfctl) 入门"
description: "了解如何使用 Azure Service Fabric CLI。 了解如何连接到群集以及如何管理应用程序。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 5ce9adf6c82e3a5521883c5de1e0689d5bf0d94e
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---
# <a name="azure-service-fabric-command-line"></a>Azure Service Fabric 命令行

Azure Service Fabric CLI (sfctl) 是命令行实用程序，用于与 Azure Service Fabric 实体交互并对其进行管理。 Sfctl 可以与 Windows 或 Linux 群集配合使用。 Sfctl 可以在任何支持 python 的平台上运行。

## <a name="prerequisites"></a>先决条件

在安装之前，请确保环境中已安装 python 和 pip。 有关详细信息，请参阅 [pip 快速入门文档](https://pip.pypa.io/en/latest/quickstart/)和正式的 [python 安装文档](https://wiki.python.org/moin/BeginnersGuide/Download)。

虽然 python 2.7 和 3.6 均受支持，但建议使用 python 3.6。

## <a name="install"></a>安装

Azure Service Fabric CLI (sfctl) 以 python 包的形式打包。 若要安装最新版，请运行：

```bash
pip install sfctl
```

安装以后，请运行 `sfctl -h` 以获取可用命令的相关信息。

## <a name="cli-syntax"></a>CLI 语法

命令始终以 `sfctl` 为前缀。 有关可以使用的所有命令的一般信息，请使用 `sfctl -h`。 有关单个命令的帮助，请使用 `sfctl <command> -h`。

命令遵循可重复结构，将命令目标置于谓词或操作的前面：

```azurecli
sfctl <object> <action>
```

在此示例中，`<object>` 是 `<action>` 的目标。

## <a name="select-a-cluster"></a>选择群集

在执行任何操作之前，必须选择要连接到的群集。 例如，运行以下命令即可选择名为 `testcluster.com` 的群集并与之进行连接。

> [!WARNING]
> 请勿将不安全的 Service Fabric 群集用在生产环境中。

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

群集终结点必须以 `http` 或 `https` 为前缀。 它必须包括 HTTP 网关的端口。 此端口和地址与 Service Fabric Explorer URL 相同。

对于使用证书进行保护的群集，可以指定一个进行 PEM 编码的证书。 可以将证书指定为单个文件，或者指定为证书和密钥对。

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

有关详细信息，请参阅[连接到安全的 Azure Service Fabric 群集](service-fabric-connect-to-secure-cluster.md)。

## <a name="basic-operations"></a>基本操作

系统会在多个 sfctl 会话中保留群集连接信息。 选择 Service Fabric 群集以后，即可在群集上运行任何 Service Fabric 命令。

例如，若要获取 Service Fabric 群集运行状况，请使用以下命令：

```azurecli
sfctl cluster health
```

此命令生成以下输出：

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

一些有助于解决常见问题的建议和提示。

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>将证书从 PFX 转换为 PEM 格式

Service Fabric CLI 支持 PEM（.pem 扩展名）文件形式的客户端证书。 如果使用 Windows 的 PFX 文件，则必须将这些证书转换为 PEM 格式。 若要将 PFX 文件转换为 PEM 文件，请使用以下命令：

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

有关详细信息，请参阅 [OpenSSL 文档](https://www.openssl.org/docs/)。

### <a name="connection-issues"></a>连接问题

某些操作可能会生成以下消息：

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

验证指定的群集终结点是否可用且正进行侦听。 另请验证是否可以在该主机和端口上使用 Service Fabric Explorer UI。 若要更新终结点，请使用 `sfctl cluster select`。

### <a name="detailed-logs"></a>详细日志

调试或报告某个问题时，详细日志通常很有用。 有一个全局的 `--debug` 标志，可提高日志文件的详细程度。

### <a name="command-help-and-syntax"></a>命令帮助和语法

如需某个特定命令或一组命令的帮助，请使用 `-h` 标志：

```azurecli
sfctl application -h
```

另一个示例：

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>后续步骤

* [使用 Azure Service Fabric CLI 部署应用程序](service-fabric-application-lifecycle-sfctl.md)
* [Linux 上的 Service Fabric 入门](service-fabric-get-started-linux.md)

