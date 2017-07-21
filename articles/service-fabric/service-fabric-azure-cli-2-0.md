---
title: "Service Fabric 和 Azure CLI 2.0 入门"
description: "如何使用 Azure CLI 2.0 版中的 Service Fabric 命令模块，包括如何连接到群集以及如何管理应用程序"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017

---
# Service Fabric 和 Azure CLI 2.0
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

新的 Azure CLI 2.0 现在包括管理 Service Fabric 群集所需的命令。 本文档包括 Azure CLI 入门步骤。

## 安装 Azure CLI 2.0
<a id="install-azure-cli-20" class="xliff"></a>

Azure CLI 现在包括进行 Service Fabric 群集的交互和管理所需的命令。 可以按[标准安装流程](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)获取最新的 Azure CLI。

有关详细信息，请查看 [Azure CLI 2.0 文档](https://docs.microsoft.com/en-us/cli/azure/overview)。

## CLI 语法
<a id="cli-syntax" class="xliff"></a>

在 Azure CLI 中，所有 Azure Service Fabric 命令都带 `az sf` 前缀。 若要详细了解可用命令，可运行 `az sf -h` 以获取常规信息。 也可运行 `az sf <command> -h`，获取有关单个命令的详细帮助。

CLI 中的 Azure Service Fabric 命令遵循命名模式

```azurecli
az sf <object> <action>
```

在这里，`<object>` 是 `<action>` 的目标。

## 选择群集
<a id="selecting-a-cluster" class="xliff"></a>

在执行任何操作之前，必须选择要连接到的群集。 请查看以下代码片段示例，了解如何连接到不安全的群集。

> [!WARNING]
> 请勿将不安全的 Service Fabric 群集用于生产环境

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

群集终结点必须以 `http` 或 `https` 为前缀，且应包括 HTTP 网关的端口。 此端口和地址与 Service Fabric Explorer URL 相同。

对于使用证书进行保护的群集，系统支持不加密的 `pem` 或 `crt` 和 `key` 文件。

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

如需详细信息，请参阅[有关如何连接到安全群集的详细文档](service-fabric-connect-to-secure-cluster.md)。

> [!NOTE]
> select 命令不会在返回之前执行任何请求。 若要验证是否已正确指定某个群集，请运行 `az sf cluster health` 之类的命令，然后检查该命令是否没有返回错误。

## 执行基本操作
<a id="performing-basic-operations" class="xliff"></a>

系统会在不同的 Azure CLI 会话中保留群集连接信息。 选择 Service Fabric 群集以后，即可运行任何 Service Fabric 命令。

例如，若要获取 Service Fabric 群集运行状况，请运行以下命令

```azurecli
az sf cluster health
```

该命令生成以下输出（假定在 Azure CLI 配置中指定了 JSON 输出）

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

## 提示和常见问题解答
<a id="tips-and-faq" class="xliff"></a>

在 Azure CLI 中使用 Service Fabric 命令时，如果遇到问题，则可参考以下信息

### 将证书从 PFX 转换为 PEM
<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

Azure CLI 支持 PEM（扩展名为 `.pem`）文件形式的客户端证书。 如果使用 Windows 中的 PFX 文件，则需将这些证书转换为 PEM 格式。 要将 PFX 文件转换为 PEM 文件，请使用以下命令：

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

有关详细信息，请参阅 [OpenSSL 文档](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html)。

### 连接问题
<a id="connection-issues" class="xliff"></a>

执行操作时，可能会遇到以下错误：

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

在这种情况下，请仔细检查指定的群集终结点是否可以访问，以及是否处于侦听状态。 另请验证是否可以在该主机和端口上访问 Service Fabric Explorer UI。 使用 `az sf cluster select` 更新终结点。

### 获取详细的日志
<a id="getting-detailed-logs" class="xliff"></a>

调试或报告问题时可以包括详细的日志，这很有用。 Azure CLI 包括一个全局的 `--debug` 标志，可提高日志的详细程度。

### 命令帮助和语法
<a id="command-help-and-syntax" class="xliff"></a>

Service Fabric 命令与 Azure CLI 命令遵循相同的约定。 指定 `-h` 标志可获取特定的命令或命令组的帮助。 例如：

```azurecli
az sf application -h
```

或

```azurecli
az sf application create -h
```

