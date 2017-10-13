---
title: "使用 Azure 中的加密保护传输中的个人数据 | Microsoft Docs"
description: "使用 Azure 中的加密保护个人数据"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 461ddfda796bfe6639e27f6c4cd53e82b4e397d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Azure 加密技术：使用加密保护传输中的个人数据

本文帮助读者了解 Azure 加密技术保护以及如何使用它来保护传输中的数据。 

在网络中传输个人数据时保护数据的隐私性，是多层深层防御安全策略的必不可少的组成部分。 传输中加密旨在防止攻击者截获传输内容，从而能够查看或利用这些数据。

## <a name="scenario"></a>方案

总部位于美国的一家大型邮轮公司正在拓展其运营，以便在地中海、亚得里亚海和波罗的海以及英属岛屿提供路线。 为支持这些工作，该公司并购了意大利、德国、丹麦和英国的几家小型邮轮公司。 

该公司使用 Microsoft Azure 在云中存储企业数据。 这些数据包括个人身份信息，例如其全球客户群的姓名、地址、电话号码和信用卡信息。 另外，还包括传统的人力资源信息，例如，有关所有地点的公司员工的地址、电话号码、纳税识别号和其他信息。 该邮轮公司还维护一个奖励和忠诚计划会员的大型数据库，其中包含用于跟踪与当前和既往客户之间的关系的个人信息。

客户的个人数据从公司的远程办公室和世界各地的旅行社输入到该数据库中。 包含客户信息的文档通过网络传输到 Azure 存储。

## <a name="problem-statement"></a>问题陈述

在与 Azure 服务之间来回传输客户和员工的个人数据时，该公司必须保护这些数据的隐私。

## <a name="company-goal"></a>公司目标

公司的目标是确保个人数据在离开磁盘后经过加密。 如果未经授权的人员截获了离开磁盘的个人数据，必须以不可读的形式呈现数据。 对于用户和管理员而言，应用加密的过程应该简单或完全透明。

## <a name="solutions"></a>解决方案

Azure 服务提供多种工具和技术来帮助保护传输中的个人数据。

### <a name="azure-storage"></a>Azure 存储空间

云中存储的数据必须从客户端（可以在世界上的任何实际位置）传输到 Azure 数据中心。 当用户检索数据时，数据以相反的方向再次传输。 通过公共 Internet 传输的数据始终存在被攻击者截获的风险。 在不同的位置之间移动个人数据时，必须使用传输级加密来保护数据，以确保数据的隐私性。

HTTPS 协议通过 Internet 提供安全的加密信道。 访问 Azure 存储中的对象以及调用 REST API 时，应使用 HTTPS。 使用[共享访问签名](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (SAS) 委托对 Azure 存储对象的访问权限时，应强制使用 HTTPS 协议。 有两种类型的 SAS：服务 SAS 和帐户 SAS。

#### <a name="how-do-i-construct-a-service-sas"></a>如何构造服务 SAS？

服务 SAS 只能委托对以下一个存储服务中的资源的访问权限：Blob、队列、表或文件服务。 若要构造服务 SAS，请执行以下操作：

1. 指定签名的版本字段

2. 指定签名的资源（仅限 Blob 和文件服务）

3. 指定用于替代响应标头的查询参数（仅限 Blob 服务和文件服务）

4. 指定表名称（仅限表服务）

5. 指定访问策略

6. 指定签名有效性间隔

8. 指定权限

9. 指定 IP 地址或 IP 范围

10. 指定 HTTP 协议

11. 指定表访问范围

12. 指定签名的标识符

13. 指定签名

有关详细说明，请参阅[构造服务 SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN)。

#### <a name="how-do-i-construct-an-account-sas"></a>如何构造帐户 SAS？

帐户 SAS 可委托对一个或多个存储服务中的资源的访问权限。 还可以委派对 blob 容器、表、队列和文件共享执行读取、写入和删除操作的访问权限，而这是服务 SAS 所不允许的。 帐户 SAS 的构造方式类似于服务 SAS。 有关详细说明，请参阅[构造帐户 SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)。

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>调用 REST API 时如何强制实施 HTTPS？

在调用 REST API 来访问存储帐户中的对象时，可以通过为存储帐户启用“需要安全传输”来强制使用 HTTPS。 

1. 在 Azure 门户中，选择“创建存储帐户”，或针对现有存储帐户选择“设置”，再选择“配置”。

2. 在“需要安全传输”下，选择“已启用”。

![创建存储帐户](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

有关详细说明，包括如何以编程方式启用“需要安全传输”，请参阅[需要安全传输](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer)。

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>如何在 Azure 文件存储中加密数据？

若要使用 [Azure 文件存储](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal)加密传输中的数据，可以在 Windows 8、8.1 和 10 以及 Windows Server 2012 R2 和 Windows Server 2016 中使用 SMB 3.x。 使用 Azure 文件服务时，如果启用了“需要安全传输”，未加密的任何连接将失败。 这包括使用 SMB 2.1、未加密的 SMB 3.0 以及某些版本的 Linux SMB 客户端的方案。

#### <a name="azure-client-side-encryption"></a>Azure 客户端加密

用于保护在客户端应用程序与 Azure 存储之间传输的个人数据的另一个选项是[客户端加密](https://docs.microsoft.com/azure/storage/storage-client-side-encryption)。 将数据传输到 Azure 存储之前以及从 Azure 存储中检索数据时，数据会经过加密；在客户端上收到数据后，数据会经过解密。

### <a name="azure-site-to-site-vpn"></a>Azure 站点到站点 VPN

保护在企业网络或用户与 Azure 虚拟网络之间传输的个人数据的有效方式是使用[站点到站点](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)或[点到站点](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)虚拟专用网络 (VPN)。 VPN 连接通过 Internet 创建安全加密隧道。

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>如何创建站点到站点 VPN 连接？

站点到站点 VPN 可将企业网络中的多个用户连接到 Azure。 若要在 Azure 门户中创建站点到站点连接，请执行以下操作：

1. 创建虚拟网络。

2. 指定 DNS 服务器。

3. 创建网关子网。

4. 创建 VPN 网关。 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. 创建本地网关。

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. 配置 VPN 设备。

7. 创建 VPN 连接。

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. 验证 VPN 连接。

有关如何在 Azure 门户中创建站点到站点连接的更多详细说明，请参阅 [在 Azure 门户中创建站点到站点连接] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)。

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>如何创建点到站点 VPN 连接？

点到站点 VPN 可在单台客户端计算机与虚拟网络之间创建安全连接。 要从远程位置（例如从家里或会议室）连接到 Azure 时，此连接会很有用。 若要在 Azure 门户中创建点到站点连接，请执行以下操作：

1. 创建虚拟网络。

2. 添加网关子网。

3. 指定 DNS 服务器。 （可选）

4. 创建虚拟网络网关。

5. 生成证书。

6. 添加客户端地址池。

7. 上传根证书的公共证书数据。

8. 生成和安装 VPN 客户端配置包。

9. 安装已导出的客户端证书。

10. 连接到 Azure。

11. 验证连接。

有关更多详细说明，请参阅[使用证书身份验证配置与 VNet 的点到站点连接：Azure 门户](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)。

### <a name="ssltls"></a>SSL/TLS

Microsoft 建议始终使用 SSL/TLS 协议来跨不同的位置交换数据。 选择使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) 通过专用高速 WAN 链接移动大型数据集的组织还可以使用 SSL/TLS 或其他协议在应用程序级别加密数据，以提供额外的保护。

### <a name="encryption-by-default"></a>默认加密

Microsoft 使用加密来保护在客户与 Azure 云服务之间传输的数据。 如果通过 Azure 门户与 Azure 存储交互，则所有事务都将通过 HTTPS 发生。

[传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) 是 Microsoft 数据中心尝试与连接到 Microsoft 云服务的客户端系统协商的协议。 TLS 进行严格的身份验证，消息隐私性和完整性强（允许检测消息篡改、拦截和伪造），具有良好的互操作性，算法灵活，易于部署和使用。

此外还会采用[完美正向保密](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS)，以便客户的客户端系统与 Microsoft 云服务之间的每个连接使用唯一的密钥。 与 Microsoft 云服务建立的连接还会利用基于 RSA 的 2048 位加密密钥长度。 TLS、RSA 2048 位密钥长度和 PFS 的组合，大大提高了截获和访问在 Microsoft 云服务与客户之间传输的数据的难度。

传输中的数据始终在 [Data Lake Store] 中加密 (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview)。 除了在存储到永久性介质之前对数据加密，还会始终通过 HTTPS 对传输数据进行保护。 HTTPS 是 Data Lake Store REST 接口唯一支持的协议。

## <a name="summary"></a>摘要

公司可以通过强制与 Azure 存储建立 HTTPS 连接、使用共享访问签名并在存储帐户中启用“需要安全传输”，来实现保护个人数据和此类数据的隐私性的目标。 他们还可以通过使用 SMB 3.0 连接并实施客户端加密来保护个人数据。 从企业网络到 Azure 虚拟网络的站点到站点 VPN 连接，以及从单个用户建立的点到站点 VPN 连接，会创建一个可用于安全传输个人数据的安全隧道。 Microsoft 的默认加密实践会进一步保护个人数据的隐私。

## <a name="next-steps"></a>后续步骤

- [Azure 数据安全与加密最佳做法](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [规划和设计 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [VPN 网关常见问题](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [为 Azure 应用服务购买和配置 SSL 证书](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
