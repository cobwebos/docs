---
title: Azure VMware 解决方案（按云简单 ） - 为私有云配置 vSAN 加密
description: 描述如何配置 vSAN 软件加密功能，以便云简单私有云可以使用在 Azure 虚拟网络中运行的密钥管理服务器。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020635"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>为云简单私有云配置 vSAN 加密

您可以配置 vSAN 软件加密功能，以便云简单私有云可以使用在 Azure 虚拟网络中运行的密钥管理服务器。

VMware 在使用 vSAN 加密时需要使用符合外部 KMIP 1.1 标准的第三方密钥管理服务器 （KMS） 工具。 您可以利用 VMware 认证且可用于 Azure 的任何受支持的 KMS。

本指南介绍如何使用在 Azure 虚拟网络中运行的 HyTrust 密钥控制 KMS。 类似的方法可用于任何其他经过认证的第三方 KMS 解决方案的 vSAN。

此 KMS 解决方案要求您：

* 在 Azure 虚拟网络中安装、配置和管理 VMware 认证的第三方 KMS 工具。
* 为 KMS 工具提供您自己的许可证。
* 使用在 Azure 虚拟网络中运行的第三方 KMS 工具在私有云中配置和管理 vSAN 加密。

## <a name="kms-deployment-scenario"></a>KMS 部署方案

KMS 服务器群集在 Azure 虚拟网络中运行，通过配置的 Azure ExpressRoute 连接从私有云 vCenter 可进行 IP 可伸路。

![..Azure 虚拟网络中的媒体/KMS 群集](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解决方案

部署过程具有以下步骤：

1. [验证是否满足先决条件](#verify-prerequisites-are-met)
2. [云简单门户：获取快速路由对等信息](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure 门户：将虚拟网络连接到私有云](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure 门户：在虚拟网络中部署 HyTrust 密钥控制群集](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI：配置KMIP服务器](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI：配置 vSAN 加密以在 Azure 虚拟网络中使用 KMS 群集](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>满足验证先决条件

在部署之前验证以下内容：

* 所选 KMS 供应商、工具和版本位于 vSAN 兼容性列表中。
* 所选供应商支持要在 Azure 中运行的工具的版本。
* KMS 工具的 Azure 版本符合 KMIP 1.1 标准。
* 已创建 Azure 资源管理器和虚拟网络。
* 云简单私有云已创建。

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>云简单门户：获取快速路由对等信息

要继续设置，需要 ExpressRoute 的授权密钥和对等电路 URI 以及对 Azure 订阅的访问。 此信息可在云简单门户中的虚拟网络连接页面上找到。 有关说明，请参阅[设置到私有云的虚拟网络连接](virtual-network-connection.md)。 如果您在获取信息时遇到任何问题，请打开[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure 门户：将虚拟网络连接到私有云

1. 使用 Azure 门户按照[ExpressRoute 配置虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)中的说明为虚拟网络创建虚拟网络网关。
2. 使用门户，按照[将虚拟网络连接到 ExpressRoute 电路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)中的说明，将虚拟网络链接到云简单快速路由电路。
3. 使用云简单欢迎电子邮件中收到的 CloudSimple 快速路由电路信息将虚拟网络链接到 Azure 中的云简单快速路由电路。
4. 输入授权密钥和对等电路 URI，为连接指定名称，然后单击 **"确定**"。

![创建虚拟网络时提供 CS 等体电路 URI](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure 门户：在虚拟网络中的 Azure 资源管理器中部署 HyTrust 密钥控制群集

要在虚拟网络中的 Azure 资源管理器中部署 HyTrust 密钥控制群集，请执行以下任务。 有关详细信息，请参阅[HyTrust 文档](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)。

1. 按照 HyTrust 文档中的说明创建具有指定入站规则的 Azure 网络安全组 （nsg-hytrust）。
2. 在 Azure 中生成 SSH 密钥对。
3. 从 Azure 应用商店中的映像部署初始密钥控制节点。  使用生成的密钥对的公钥，选择**nsg-hytrust**作为密钥控制节点的网络安全组。
4. 将 KeyControl 的专用 IP 地址转换为静态 IP 地址。
5. SSH 到密钥控制 VM 使用其公共 IP 地址和前面提到的密钥对的私钥。
6. 当 SSH shell 中提示`No`时，选择将节点设置为初始密钥控制节点。
7. 通过重复此过程的步骤 3-5 并选择`Yes`何时提示添加到现有群集，添加其他 KeyControl 节点。

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI：配置 KMIP 服务器

转到公共*ip*https:// ，其中*公共 ip*是 KeyControl 节点 VM 的公共 IP 地址。 按照[HyTrust 文档中](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)的步骤操作。

1. [配置 KMIP 服务器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [为 VMware 加密创建证书包](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI：配置 vSAN 加密以在 Azure 虚拟网络中使用 KMS 群集

按照 HyTrust 说明[在 vCenter 中创建 KMS 群集](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)。

![在 vCenter 中添加 KMS 群集详细信息](media/vsan-config01.png)

在 vCenter 中，转到**群集>配置**并选择 vSAN**的"常规**"选项。 启用加密并选择以前添加到 vCenter 的 KMS 群集。

![启用 vSAN 加密并在 vCenter 中配置 KMS 群集](media/vsan-config02.png)

## <a name="references"></a>参考

### <a name="azure"></a>Azure

[使用 Azure 门户配置 ExpressRoute 的虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[使用门户将虚拟网络连接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>海信

[HyTrust 数据控制和微软 Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[配置 KMPI 服务器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[为 VMware 加密创建证书包](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[在 vSphere 中创建 KMS 群集](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
