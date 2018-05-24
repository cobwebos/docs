---
title: Azure Service Fabric CLI 入门
description: 了解如何使用 Azure Service Fabric CLI。 了解如何连接到群集以及如何管理应用程序。
services: service-fabric
author: Christina-Kang
manager: timlt
ms.service: service-fabric
ms.topic: conceptual
ms.date: 10/20/2017
ms.author: bikang
ms.openlocfilehash: 7b62631bd386a2feaa675b0ebd234768bec2f5e1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric CLI

Azure Service Fabric 命令行接口 (CLI) 是一个命令行实用程序，用于与 Service Fabric 实体交互并对其进行管理。 Service Fabric CLI 可以与 Windows 或 Linux 群集配合使用。 Service Fabric CLI 可以在任何支持 Python 的平台上运行。

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>先决条件

在安装之前，请确保环境中已安装 Python 和 pip。 有关详细信息，请参阅 [pip 快速入门文档](https://pip.pypa.io/en/latest/quickstart/)和官方的 [Python 安装文档](https://wiki.python.org/moin/BeginnersGuide/Download)。

CLI 支持 Python 2.7、3.5 和 3.6 版。 建议使用 Python 3.6 版，因为很快会终止对 Python 2.7 的支持。

### <a name="service-fabric-target-runtime"></a>Service Fabric 目标运行时

Service Fabric CLI 旨在支持 Service Fabric SDK 的最新运行时版本。 使用下表确定应安装哪个版本的 CLI：

| CLI 版本   | 支持的运行时版本 |
|---------------|---------------------------|
| 最新 (~=4)  | 最新 (~=6.1)            |
| 3.0.0         | 6.0                       |
| 1.1.0         | 5.6, 5.7                  |

为 `pip install` 命令添加 `==<version>` 后缀即可选择性地指定要安装的 CLI 的目标版本。 例如，版本 1.1.0 的语法为：

```
pip install -I sfctl==1.1.0
```

视需要将以下 `pip install` 命令替换为此前提到过的命令。

有关 Service Fabric CLI 版本的详细信息，请参阅 [GitHub 文档](https://github.com/Azure/service-fabric-cli/releases)。

## <a name="install-pip-python-and-the-service-fabric-cli"></a>安装 pip、Python 和 Service Fabric CLI

可以通过多种方式在平台上安装 pip 和 Python。 按照以下步骤可在主流操作系统上快速安装 Python 3 和 pip。

### <a name="windows"></a>Windows

对于 Windows 10、Windows Server 2016 和 Windows Server 2012 R2，使用标准官方安装说明进行操作。 默认情况下，Python 安装程序也安装 pip。

1. 转到官方的 [Python 下载页](https://www.python.org/downloads/)，下载最新版本的 Python 3.6。

2. 启动安装程序。

3. 在提示的底部，选择“将 Python 3.6 添加到路径”。

4. 选择“立即安装”，并完成安装。

现在可以打开新的命令窗口，获取 Python 和 pip 的版本。

```bat
python --version
pip --version
```

然后运行以下命令，安装 Service Fabric CLI：

```bat
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>适用于 Linux 的 Ubuntu 和 Windows 子系统

若要安装 Service Fabric CLI，请运行以下命令：

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

然后即可通过以下方式测试安装情况：

```bash
sfctl -h
```

如果收到“找不到命令”错误，例如：

`sfctl: command not found`

请确保可从 `$PATH` 访问 `~/.local/bin`：

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```

如果因文件夹权限不正确而导致适用于 Linux 的 Windows 子系统上的安装失败，则可能需要使用提升的权限再试：

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4（Service Fabric 预览版支持）

若要在 Redhat 上安装 Service Fabric CLI，请运行以下命令：

```bash
sudo yum install -y python34
sudo yum install python34-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

如需对安装进行测试，可参阅“适用于 Linux 的 Ubuntu 和 Windows 子系统”部分提到的步骤。

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

对于 MacOS，建议使用 [HomeBrew 包管理器](https://brew.sh)。 如果尚未安装 HomeBrew，请通过运行以下命令安装它：

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

然后从终端通过运行以下命令，安装 Python 3.6、pip 和 Service Fabric CLI：

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>CLI 语法

命令始终以 `sfctl` 为前缀。 有关可以使用的所有命令的一般信息，请使用 `sfctl -h`。 有关单个命令的帮助，请使用 `sfctl <command> -h`。

命令遵循可重复结构，将命令目标置于谓词或操作的前面。

```azurecli
sfctl <object> <action>
```

在此示例中，`<object>` 是 `<action>` 的目标。

## <a name="select-a-cluster"></a>选择群集

在执行任何操作之前，必须选择要连接到的群集。 例如，若要选择名为 `testcluster.com` 的群集并与之进行连接，请运行以下命令：

> [!WARNING]
> 请勿将不安全的 Service Fabric 群集用在生产环境中。

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

群集终结点必须以 `http` 或 `https` 为前缀。 它必须包括 HTTP 网关的端口。 此端口和地址与 Service Fabric Explorer URL 相同。

对于使用证书进行保护的群集，可以指定一个进行 PEM 编码的证书。 可以将证书指定为单个文件，或者指定为证书和密钥对。 如果它是并非 CA 签名的自签名证书，可以传递 `--no-verify` 选项以跳过 CA 验证。

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

有关详细信息，请参阅[连接到安全的 Azure Service Fabric 群集](service-fabric-connect-to-secure-cluster.md)。

## <a name="basic-operations"></a>基本操作

系统会在多个 Service Fabric CLI 会话中保留群集连接信息。 选择 Service Fabric 群集以后，即可在群集上运行任何 Service Fabric 命令。

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

下面是一些有助于解决常见问题的建议和提示。

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>将证书从 PFX 转换为 PEM 格式

Service Fabric CLI 支持 PEM（.pem 扩展名）文件形式的客户端证书。 如果使用 Windows 的 PFX 文件，则必须将这些证书转换为 PEM 格式。 若要将 PFX 文件转换为 PEM 文件，请使用以下命令：

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

同样，若要从 PEM 文件将转换为 PFX 文件，可以使用以下命令（此处未提供密码）：

```bash
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

有关详细信息，请参阅 [OpenSSL 文档](https://www.openssl.org/docs/)。

### <a name="connection-problems"></a>连接问题

某些操作可能会生成以下消息：

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

验证指定的群集终结点是否可用且正进行侦听。 另请验证是否可以在该主机和端口上使用 Service Fabric Explorer UI。 若要更新终结点，请使用 `sfctl cluster select`。

### <a name="detailed-logs"></a>详细日志

调试或报告某个问题时，详细日志通常很有用。 全局 `--debug` 标志可提高日志文件的详细程度。

### <a name="command-help-and-syntax"></a>命令帮助和语法

如需某个特定命令或一组命令的帮助，请使用 `-h` 标志。

```azurecli
sfctl application -h
```

以下是另一个示例：

```azurecli
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>更新 Service Fabric CLI 

若要更新 Service Fabric CLI，请运行以下命令（根据在原始安装期间所选的内容将 `pip` 替换为 `pip3`）：

```bash
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>后续步骤

* [使用 Azure Service Fabric CLI 部署应用程序](service-fabric-application-lifecycle-sfctl.md)
* [Linux 上的 Service Fabric 入门](service-fabric-get-started-linux.md)
