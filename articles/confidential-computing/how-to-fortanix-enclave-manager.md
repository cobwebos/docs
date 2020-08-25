---
title: 如何使用 Fortanix Enclave Manager 运行应用程序
description: 了解如何使用 Fortanix Enclave Manager 转换容器化映像
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: d46354b18d5c3317cc1ed67d839feb26b7b6b5d0
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815838"
---
# <a name="how-to-run-an-application-with-fortanix-enclave-manager"></a>如何：使用 Fortanix Enclave Manager 运行应用程序 

在 Azure 机密计算中使用 [Fortanix Enclave Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) 和 [Fortanix 节点代理](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) 从 [Fortanix](https://www.fortanix.com/)开始运行应用程序。


Fortanix 是第三方软件供应商，其中的产品和服务构建在 Azure 基础结构之上。 其他第三方提供商在 Azure 上提供类似的机密计算服务，例如 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 和 [Scone](https://sconedocs.github.io)。  

> [!Note] 
 > 本文档中提及的产品不受 MICROSOFT 控制。 MICROSOFT 仅为你提供此信息，并且对这些非 MICROSOFT 产品的引用不表示 MICROSOFT 认可。



本教程介绍如何将应用程序映像转换为机密的计算保护映像。 此环境使用由 Azure 的 DCsv2 系列支持 Intel SGX 的虚拟机提供支持的 [Fortanix](https://www.fortanix.com/) 软件。 此解决方案协调重要的安全策略，如身份验证和数据访问控制。

 有关特定于 Fortanix 的支持，请加入 [Fortanix 时差社区](https://fortanix.com/community/) ，并使用频道 #enclavemanager。


## <a name="prerequisites"></a>先决条件

1. 如果没有 Fortanix Enclave 管理器帐户，请在开始前 [注册](https://em.fortanix.com/auth/sign-up) 。
1. 用于推送转换后的应用程序映像的专用 [Docker](https://docs.docker.com/) 注册表。
1. 如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

> [!NOTE]
> 免费试用帐户无法访问本教程中使用的虚拟机。 请升级为即用即付订阅。

## <a name="add-an-application-to-fortanix-enclave-manager"></a>向 Fortanix Enclave Manager 添加应用程序
1. 登录到 [FORTANIX EM](https://fortanix.com)
1. 导航到 " **帐户** " 页，然后选择 " **添加帐户** " 以创建新帐户。 
    
![创建帐户](media/how-to-fortanix-enclave-manager/create-account.png)

1. 创建帐户后，单击 " **选择** " 以选择新创建的帐户。 现在，我们可以开始注册计算节点和创建应用程序。 
1. 选择 " **+ 应用程序** " 按钮以添加应用程序。 在此示例中，我们将添加 Flask Server Enclave OS 应用程序。 

1. 选择 Enclave OS 应用程序的 " **添加** " 按钮。 

    ![添加应用程序](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > 本教程仅介绍如何添加 Enclave OS 应用程序。 了解有关将 EDP Rust 应用程序引入 Fortanix Enclave Manager 的[详细](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Enclave-Manager)信息。 

6. 在本教程中，我们将为示例应用程序使用 Fortanix 的 docker 注册表。 填写以下信息中的详细信息。 使用专用 docker 注册表保留输出图像。 
 
    - **应用程序名称**： Python 应用程序服务器
    - **说明**： Python Flask 服务器
    - **输入图像名称**： fortanix/python-flask
    - **输出映像名称**： fortanx/python-flask-sgx
    - **ISVPRODID**：1
    - **ISVSVM**：1
    - **内存大小**： 1 GB
    - **线程计数**：128

    *可选*：运行该应用程序。
    - **Docker 中心**： [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **应用**： fortanix/python-flask

        运行下面的命令：
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. 添加证书。 使用以下详细信息填写信息，然后选择 " **下一步**"：
    - **域**： myapp. dom
    - **类型**： Enclave Manager 颁发的证书 
    - **密钥路径**：/appkey.pem
    - **密钥类型**： RSA
    - **证书路径**：/appcert.pem
    - **RSA 密钥大小**：2048位
    

## <a name="create-an-image"></a>创建映像
Fortanix EM 图像是应用程序的软件版本或版本。 每个映像都与一个 enclave 哈希 (MRENCLAVE) 相关联。 
1. 在 "**添加映像**" 页上，输入**输出映像名称**的**注册表凭据**。 这些凭据用于访问将在其中推送映像的专用 docker 注册表。 

    ![创建映像](media/how-to-fortanix-enclave-manager/create-image.png)
1. 提供图像标记，然后选择 " **创建**"。

    ![添加标记](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>域和映像允许列表 
将其域添加到允许列表中的应用程序将从 Fortanix Enclave Manager 获取 TLS 证书。 同样，当应用程序从转换后的映像中运行时，它将尝试联系 Fortanix Enclave Manager。 然后，应用程序将要求提供 TLS 证书。 

切换到左侧的 " **任务** " 选项卡，并批准待定的请求以允许域和映像。 

## <a name="enroll-compute-node-agent-in-azure"></a>在 Azure 中注册计算节点代理

### <a name="generate-and-copy-join-token"></a>生成和复制联接标记
在 Fortanix Enclave Manager 中，你将创建一个令牌。 此令牌允许 Azure 中的计算节点对自身进行身份验证。 需要为 Azure 虚拟机指定此令牌。
1. 在管理控制台中，选择 " **+ 注册节点** " 按钮。 
1. 选择 " **生成令牌** " 以生成联接标记。 复制令牌。

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>将节点注册到 Azure Marketplace 中的 Fortanix 节点代理

创建 Fortanix 节点代理会将虚拟机、网络接口、虚拟网络、网络安全组和公共 IP 地址部署到 Azure 资源组中。 对于虚拟机，你的 Azure 订阅将按小时计费。 在创建 Fortanix 节点代理之前，请查看 DCsv2 系列的 Azure [虚拟机定价页](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 。 删除不使用的 Azure 资源。 

1. 请前往 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) ，并以 azure 凭据登录。
1. 在搜索栏中，键入 " **Fortanix 机密计算节点代理**"。 选择在名为 **Fortanix** 的搜索框中显示的应用程序，以导航到该产品的主页。 
     ![搜索 marketplace](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. 选择 " **立即获取**"，根据需要填写信息，并选择 " **继续**"。 你将重定向到 Azure 门户。 
1. 选择 " **创建** " 以输入 "Fortanix 保密计算节点代理部署" 页。
1. 在此页上，你将输入用于部署虚拟机的信息。 具体而言，此 VM 是安装了 Fortanix Node Agent 软件的 Azure 中的 DCsv2 系列启用了 Intel SGX 的虚拟机。 节点代理将允许转换的映像在 Azure 中的 Intel SGX 节点上安全运行。  选择要在其中部署虚拟机和关联资源的 **订阅** 和 **资源组** 。 
 
    > [!NOTE]
    > 在 Azure 中部署 DCsv2 系列虚拟机时存在一些限制。 可能需要请求额外内核的配额。 有关详细信息，请参阅 [Azure vm 上的机密计算解决方案](https://docs.microsoft.com/azure/confidential-computing/virtual-machine-solution) 。 

1. 选择可用区域。
1. 在 " **节点名称** " 字段中输入虚拟机的名称。 
1. 输入用户名和密码 (或 SSH 密钥) ，以便对虚拟机进行身份验证。
1. 将默认 OS 磁盘大小保留为200，并选择 VM 大小 (Standard_DC4s_v2 可满足本教程) 
1. 粘贴前面在 " **联接令牌** " 字段中生成的标记。

     ![部署资源](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent.png)

1. 选择“查看 + 创建”  。 确保验证通过，然后选择 " **创建**"。 部署所有资源后，计算节点现已在 Enclave Manager 中注册。 

## <a name="run-the-application-image-on-the-compute-node"></a>在计算节点上运行应用程序映像
通过执行以下命令来运行应用程序。 请确保将节点 IP、端口和转换后的映像名称更改为你的特定应用程序的输入。 
 
在本教程中，要执行的命令为：     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

其中： 
- *52.152.206.164* 是节点代理主机 IP
- *9092* 是节点代理侦听的端口
- *fortanix/flask-sgx*是转换后的应用，可在 Fortanix Enclave 管理 Web 门户的**images**表中 "**映像名称**" 列下的 "映像" 选项卡中找到。 
    
## <a name="verify-and-monitor-the-running-application"></a>验证并监视正在运行的应用程序
1. 转到 [Fortanix Enclave Manager](https://em.fortanix.com/console)
1. 确保你在注册了节点的 **帐户** 内工作
1. 选择左侧导航窗格中的顶部图标，导航到 **管理控制台** 。 
1. 选择 " **应用程序** " 选项卡
1. 验证是否有正在运行的应用程序和关联的计算节点


## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、虚拟机和关联的资源，可以将其删除。 删除资源组将取消注册与转换后的映像相关联的节点。 

选择虚拟机的资源组，然后选择“删除”。 确认资源组的名称，以完成删除资源的操作。

若要删除创建的 Fortanix Enclave 管理器帐户，请在 Enclave 管理器中转到 " [帐户" 页](https://em.fortanix.com/accounts) 。 将鼠标悬停在要删除的帐户上。 选择右上角的黑色黑色点，然后选择 " **删除帐户**"。

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Fortanix 工具将应用程序映像转换为在机密计算虚拟机之上运行。 有关 Azure 上的机密计算虚拟机的详细信息，请参阅[有关虚拟机的解决方案](virtual-machine-solutions.md)。 

若要了解有关 Azure 机密计算产品/服务的详细信息，请参阅 [azure 机密计算概述](overview.md)

