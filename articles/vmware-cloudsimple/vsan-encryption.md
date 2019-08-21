---
title: Azure VMware 解决方案 (按 CloudSimple)-配置适用于私有云的 vSAN 加密
description: 介绍如何配置 vSAN 软件加密功能, 以便你的 CloudSimple 私有云可以使用 Azure 虚拟网络中运行的密钥管理服务器。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 288a05fc09e0c59a01a8d4c9940c0d51cabdd28f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640964"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>为 CloudSimple 私有云配置 vSAN 加密

你可以配置 vSAN 软件加密功能, 使你的 CloudSimple 私有云可以使用 Azure 虚拟网络中运行的密钥管理服务器。

使用 vSAN 加密时, VMware 需要使用外部 KMIP 1.1 兼容的第三方密钥管理服务器 (KMS) 工具。 你可以利用 VMware 提供的任何受支持的 KMS, 并可用于 Azure。 

本指南介绍如何使用在 Azure 虚拟网络中运行的 HyTrust KeyControl KMS。 类似的方法可用于 vSAN 的任何其他经过认证的第三方 KMS 解决方案。

此 KMS 解决方案要求您:

* 在 Azure 虚拟网络中安装、配置和管理 VMware 认证的第三方 KMS 工具。
* 为 KMS 工具提供自己的许可证。
* 使用 Azure 虚拟网络中运行的第三方 KMS 工具在私有云中配置和管理 vSAN 加密。

## <a name="kms-deployment-scenario"></a>KMS 部署方案

KMS 服务器群集在 Azure 虚拟网络中运行, 可通过配置的 Azure ExpressRoute 连接从私有云 vCenter 访问 IP。

![..Azure 虚拟网络中的/media/KMS 群集](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解决方案

部署过程包括以下步骤:

1. [验证是否满足先决条件](#verify-prerequisites-are-met)
2. [CloudSimple 门户:获取 ExpressRoute 对等互连信息](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure 门户：将虚拟网络连接到私有云](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure 门户：在虚拟网络中部署 HyTrust KeyControl 群集](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI:配置 KMIP 服务器](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI:将 vSAN 加密配置为使用 Azure 虚拟网络中的 KMS 群集](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>验证是否满足先决条件

在部署之前, 请验证以下各项:

* 选择的 KMS 供应商、工具和版本位于 vSAN 兼容性列表上。
* 所选供应商支持在 Azure 中运行的工具版本。
* Azure 版本的 KMS 工具符合 KMIP 1.1 标准。
* 已创建 Azure 资源管理器和虚拟网络。
* 已创建 CloudSimple 私有云。

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple 门户:获取 ExpressRoute 对等互连信息

若要继续安装, 需要提供 authorization 密钥和对等线路 URI, 以便 ExpressRoute 以及对 Azure 订阅的访问权限。 此信息可在 CloudSimple 门户中的 "虚拟网络连接" 页面上找到。 有关说明, 请参阅[设置与私有云的虚拟网络连接](virtual-network-connection.md)。 如果在获取信息时遇到任何问题, 请[提出支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure 门户：将虚拟网络连接到私有云

1. 按照[使用 Azure 门户配置 ExpressRoute 的虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)中的说明, 为虚拟网络创建虚拟网络网关。
2. 按照[使用门户将虚拟网络连接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)中的说明, 将虚拟网络链接到 CloudSimple ExpressRoute 线路。
3. 使用 CloudSimple 中的欢迎电子邮件中收到的 CloudSimple ExpressRoute 线路信息, 将虚拟网络链接到 Azure 中的 CloudSimple ExpressRoute 线路。
4. 输入 "授权密钥" 和 "对等线路 URI", 为连接指定一个名称, 然后单击 **"确定"** 。

![创建虚拟网络时提供 CS 对等线路 URI](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure 门户：在虚拟网络中的 Azure 资源管理器中部署 HyTrust KeyControl 群集

若要在 Azure 资源管理器的虚拟网络中部署 HyTrust KeyControl 群集, 请执行以下任务。 有关详细信息, 请参阅[HyTrust 文档](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)。

1. 按照 HyTrust 文档中的说明, 创建具有指定入站规则的 Azure 网络安全组 (nsg-hytrust)。
2. 在 Azure 中生成 SSH 密钥对。
3. 从 Azure Marketplace 中的映像部署初始 KeyControl 节点。  使用生成的密钥对的公钥, 并选择**nsg-hytrust**作为 KeyControl 节点的网络安全组。
4. 将 KeyControl 的专用 IP 地址转换为静态 IP 地址。
5. 使用 SSH 连接到 KeyControl VM, 使用以前提到的密钥对的公共 IP 地址和私钥。
6. 当在 SSH shell 中出现提示时`No` , 选择将节点设置为初始 KeyControl 节点。
7. 通过重复此过程的步骤3-5 添加其他 KeyControl 节点, 并`Yes`选择 "添加到现有群集时提示"。

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI:配置 KMIP 服务器

中转到 https://*公共*ip, 其中*公共 ip*是 KeyControl 节点 VM 的公共 ip 地址。 请按照[HyTrust 文档](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)中的步骤进行操作。

1. [配置 KMIP 服务器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [为 VMware 加密创建证书捆绑](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI:将 vSAN 加密配置为使用 Azure 虚拟网络中的 KMS 群集

按照 HyTrust 说明[在 vCenter 中创建 KMS 群集](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)。

![在 vCenter 中添加 KMS 群集详细信息](media/vsan-config01.png)

在 vCenter 中, 前往**群集 > "配置**", 并选择 "**常规**" 选项。 启用加密并选择以前添加到 vCenter 的 KMS 群集。

![在 vCenter 中启用 vSAN 加密和配置 KMS 群集](media/vsan-config02.png)

## <a name="references"></a>参考资料

### <a name="azure"></a>Azure

[使用 Azure 门户为 ExpressRoute 配置虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[使用门户将虚拟网络连接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl 和 Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[配置 KMPI 服务器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[为 VMware 加密创建证书捆绑](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[在 vSphere 中创建 KMS 群集](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
