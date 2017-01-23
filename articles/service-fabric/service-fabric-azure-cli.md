---
title: "使用 CLI 与 Service Fabric 群集交互 | Microsoft 文档"
description: "如何使用 Azure CLI 与 Service Fabric 群集交互"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: c3ec8ff3-3b78-420c-a7ea-0c5e443fb10e
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2016
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: caf6dd414bd8f8180c90835dd9744dcd98f7709c


---
# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>使用 Azure CLI 与 Service Fabric 群集交互
可以使用 Linux 上的 Azure CLI 从 Linux 计算机与 Service Fabric 群集进行交互。

第一步是从 git 代表获取最新版本的 CLI，并且使用以下命令在你的路径中进行设置：

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

对于每个命令，它都支持，你可以键入命令的名称来获取该命令的帮助。 该命令支持自动完成功能。 例如，以下命令提供所有应用程序命令的帮助。 

```sh
 azure servicefabric application 
```

你可以进一步筛选特定命令的帮助，如下例所示：

```sh
 azure servicefabric application  create
```

若要在 CLI 中启用自动完成功能，请运行以下命令：

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

以下命令连接到群集，并在群集中显示节点：

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

若要使用命名的参数并查找它们是什么，你可以在命令后键入 --help。 例如：

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

当从一台**不属于群集**的计算机连接到多台计算机群集时，使用以下命令：

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

根据需要，将 PublicIPorFQDN 标记替换为实际 IP 或 FQDN。 当从一台**属于群集**的计算机连接到多台计算机群集时，使用以下命令：

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

可以使用 PowerShell 或 CLI 与通过 Azure 门户创建的 Linux Service Fabric 群集进行交互。 

> [!WARNING]
> 这些群集不安全，因此，可以通过在群集清单中添加公共 IP 地址打开你的单机。

## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>使用 Azure CLI 连接到 Service Fabric 群集
下面的 Azure CLI 命令介绍如何连接到安全的群集。 证书详细信息必须与群集节点上的证书匹配。

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```

如果你的证书具有证书颁发机构 (CA)，则需要添加 --ca-cert-path 参数，如下例所示： 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
如果有多个 CA，则使用逗号作为分隔符。

如果证书中的公用名与连接终结点不匹配，则可以使用参数 `--strict-ssl` 跳过验证，如下面的命令中所示： 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```

如果想要跳过 CA 验证，可以添加 --reject-unauthorized 参数，如下面的命令中所示： 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```

在连接后，你应能够运行其他 CLI 命令以与群集进行交互。 

## <a name="deploying-your-service-fabric-application"></a>部署 Service Fabric 应用程序
执行以下命令以复制、注册和启动 Service Fabric 应用程序：

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>升级应用程序
该进程类似于 [Windows 中的进程](service-fabric-application-upgrade-tutorial-powershell.md)。

从项目根目录生成、复制、注册并创建你的应用程序。 如果应用程序实例名为 fabric:/MySFApp，并且类型为 MySFApp，则命令如下所示：

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

对应用程序进行更改，并重新生成已修改的服务。  使用服务（和代码或配置或数据，具体视需要而定）的更新版本来更新已修改的服务清单文件 (ServiceManifest.xml)。 此外，使用应用程序的更新版本号和已修改的服务来修改应用程序的清单 (ApplicationManifest.xml)。  现在，使用以下命令复制并注册更新的应用程序：

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

现在，可以使用以下命令启动应用程序升级：

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

现在，可以使用 SFX 监视应用程序升级。 在几分钟后，应用程序将得到更新。  你还可以尝试出现错误的更新应用程序，检查 Service Fabric 中的自动回滚功能。

## <a name="converting-from-pfx-to-pem-and-vice-versa"></a>从 PFX 转换到 PEM，反之亦然

可能需要在本地计算机（搭载 Windows 或 Linux）中安装证书以访问不同环境中的安全群集。 例如，从 Windows 计算机访问受保护的 Linux 群集时，需要将证书从 PFX 转换到 PEM；反之，需要从 PEM 转换到 PFX。 

要将 PEM 文件转换为 PFX 文件，请使用以下命令：

```bash
openssl pkcs12 -export -out certificate.pfx -inkey mycert.pem -in mycert.pem -certfile mycert.pem
```

要将 PFX 文件转换为 PEM 文件，请使用以下命令：

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

有关详细信息，请参阅 [OpenSSL 文档](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html)。

## <a name="troubleshooting"></a>故障排除
### <a name="copying-of-the-application-package-does-not-succeed"></a>复制应用程序包不成功
检查是否已安装 `openssh`。 默认情况下，Ubuntu Desktop 不安装它。 使用以下命令安装它：

```
 sudo apt-get install openssh-server openssh-client**
```

如果问题仍然存在，请尝试通过使用以下命令更改 **sshd_config** 文件来对 ssh 禁用 PAM：

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

如果问题仍然存在，请尝试通过执行以下命令增加 ssh 会话数：

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
尚不支持使用密钥（而是支持使用密码）进行 ssh 身份验证（因为该平台使用 ssh 来复制包），因此改为使用密码身份验证。



## <a name="next-steps"></a>后续步骤
设置开发环境并将 Service Fabric 应用程序部署到 Linux 群集。




<!--HONumber=Dec16_HO2-->


