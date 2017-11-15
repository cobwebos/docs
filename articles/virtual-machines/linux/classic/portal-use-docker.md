---
title: "使用适用于 Linux 的 Docker VM 扩展 | Microsoft Docs"
description: "介绍 Docker 和 Azure 虚拟机扩展，以及如何在经典部署模式下使用 Azure CLI 创建用作 Docker 主机的 Azure 虚拟机。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.openlocfilehash: a5c1822b7304c0360da866ddb504483f5a53432f
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>将 Docker VM 扩展与 Azure 经典门户结合使用
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[Docker](https://www.docker.com/) 是最常用的虚拟化技术之一，它使用 [Linux 容器](http://en.wikipedia.org/wiki/LXC)而不是虚拟机作为在共享资源上隔离数据和执行计算的方法。 可以使用由 [Azure Linux 代理]管理的 Docker VM 扩展，创建可在 Azure 上为应用程序托管任意数量容器的 Docker VM。

> [!NOTE]
> 本主题介绍如何从 Azure 门户创建 Docker VM。 若要了解如何通过命令行创建 Docker VM，请参阅[如何从 Azure 命令行接口 (Azure CLI) 使用 Docker VM 扩展]。 若要查看容器及其优点的综合讨论，请参阅 [Docker 高级白板](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)。
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>从映像库创建新的 VM
第一个步骤需要可支持 Docker VM 扩展的 Linux 映像中的 Azure VM，使用映像库的 Ubuntu 14.04 LTS 映像作为示例服务器映像，并将 Ubuntu 14.04 Desktop 用作客户端。 在门户中，单击“+ 新建”以创建新的 VM 实例，并从可用选项或从完整映像库中选择 Ubuntu 14.04 LTS 映像，如下所示。

> [!NOTE]
> 目前，只有 2014 年 7 月以后发布的 Ubuntu 14.04 LTS 映像才支持 Docker VM 扩展。
> 
> 

![创建新的 Ubuntu 映像](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>创建 Docker 证书
在创建 VM 后，请确保客户端计算机上已安装 Docker。 （有关详细信息，请参阅 [Docker 安装说明](https://docs.docker.com/installation/#installation)。）

根据[使用 https 运行 Docker] 来创建 Docker 通信的证书和密钥文件，并将其放置在客户端计算机上的 **`~/.docker`** 目录中。

> [!NOTE]
> 门户中的 Docker VM 扩展目前需要 base64 编码的凭据。
> 
> 

在命令行中，使用 **`base64`** 或其他惯用的编码工具来创建 base64 编码的主题。 使用一组简单的证书和密钥文件来执行此操作，如下所示：

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>添加 Docker VM 扩展
要添加 Docker VM 扩展，请找到创建的 VM 实例，向下滚动到“扩展”，然后单击它以打开“VM 扩展”，如下所示。

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>添加扩展
单击“+ 添加”显示可添加到此 VM 的可用 VM 扩展。

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>选择 Docker VM 扩展
选择“Docker VM 扩展”，随后会显示 Docker 说明和重要链接；并单击底部的“创建”以开始执行安装过程。

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>添加证书和密钥文件：
在窗体字段中，输入 base64 编码版本的 CA 证书、服务器证书以及服务器密钥，如下图所示。

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> 请注意，（如上图所示）已按默认填入了 2376。 可以在此处输入任何终结点，但下一步是打开匹配的终结点。 如果更改默认值，请确保在下一步打开匹配的终结点。
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>添加 Docker 通信终结点
在查看创建的资源组时，可以选择与 VM 关联的网络安全组，并单击“入站安全规则”查看如下所示的规则。

![](media/portal-use-docker/AddingEndpoint.png)

单击“+ 添加”添加另一个规则，在默认情况下，请输入终结点的名称（在本示例中为 **Docker**）和“目标端口范围”2376。 设置显示 **TCP** 的协议值，并单击“确定”创建该规则。

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>测试 Docker 客户端和 Azure Docker 主机
查找并复制 VM 的域名，并在客户端计算机的命令行中，键入 `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:2376 info`（其中 *dockerextension* 已替换为 VM 的子域）。

结果应如下所示：

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

完成上述步骤后，Azure VM 上即会出现配置为从其他客户端远程连接的完全正常的 Docker 主机。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
现在，可以转到 [Docker 用户指南]，开始使用该 Docker VM。 如果想要通过命令行接口在 Azure VM 上自动创建 Docker主机，请参阅[如何从 Azure 命令行接口 (Azure CLI) 使用 Docker VM 扩展]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[如何从 Azure 命令行接口 (Azure CLI) 使用 Docker VM 扩展]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure Linux 代理]:../agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[使用 https 运行 Docker]:http://docs.docker.com/articles/https/
[Docker 用户指南]:https://docs.docker.com/userguide/
