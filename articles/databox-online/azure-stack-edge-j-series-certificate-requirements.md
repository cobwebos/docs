---
title: Azure Stack 边缘的证书要求和故障排除 |Microsoft Docs
description: 描述证书要求以及对 Azure Stack Edge 设备的证书错误进行故障排除。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: b24b745a53b632ce32cda37058363bf974d400b3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268256"
---
# <a name="certificate-requirements"></a>证书要求

本文介绍在 Azure Stack 边缘设备上安装证书之前必须满足的证书要求。 要求与 PFX 证书、颁发机构、证书使用者名称和使用者备用名称以及受支持的证书算法相关。

## <a name="certificate-issuing-authority"></a>证书颁发机构

证书颁发要求如下所示：

* 证书必须由内部证书颁发机构或公共证书颁发机构颁发。

* 不支持使用自签名证书。

* 证书的 " *颁发给：* " 字段不得与 " *颁发者：* " 字段相同，因为根 CA 证书除外。



## <a name="certificate-algorithms"></a>证书算法

证书算法必须具有以下要求：

* 证书必须使用 RSA 密钥算法。

* 证书签名算法不能为 SHA1。

* 最小密钥大小为4096。

## <a name="certificate-subject-name-and-subject-alternative-name"></a>证书使用者名称和使用者可选名称

证书必须具有以下使用者名称和使用者备用名称要求：

* 可以使用一个证书，涵盖证书的使用者备用名称中的所有名称空间 (SAN) 字段。 或者，你可以为每个命名空间使用单个证书。 这两种方法都需要在必要时使用通配符（如 (Blob) 的二进制大型对象）。

* 确保 "使用者名称") 中 (公用名的使用者名称是使用者可选名称扩展中的使用者可选名称的一部分。

* 可以使用一个通配符证书，涵盖证书的 SAN 字段中的所有命名空间。

* 创建终结点证书时使用下表：

    |类型 |使用者名称 (SN)   |使用者可选名称 (SAN)   |使用者名称示例 |
    |---------|---------|---------|---------|
    |Azure 资源管理器|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 存储|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |本地 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |两个终结点的多 SAN 单一证书|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |节点|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate 是硬编码的。  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX 证书

Azure Stack Edge 设备上安装的 PFX 证书应满足以下要求：

* 当你从 SSL 颁发机构获取证书时，请确保你获取证书的完整签名链。

* 导出 PFX 证书时，请确保已选择 " **包括证书链中的所有证书（如果可能）** " 选项。

* 为终结点、本地 UI、节点、VPN 和 Wi-fi 使用 PFX 证书，因为 Azure Stack 边缘都需要公钥和私钥。 私钥必须设置本地计算机密钥属性。

* 证书的 PFX 加密应当为 3DES。 这是从 Windows 10 客户端或 Windows Server 2016 证书存储导出时使用的默认加密。 有关与3DES 相关的详细信息，请参阅 [三重 DES](https://en.wikipedia.org/wiki/Triple_DES)。

* 证书 PFX 文件的 "*密钥用法*" 字段中必须具有有效的*数字签名*和*KeyEncipherment*值。

* 在 "*增强型密钥用法*" 字段中，证书 PFX 文件必须具有*服务器身份验证 (1.3.6.1.5.5.7.3.1) *和*客户端身份验证 (1.3.6.1.5.5.7.3.2) *的值。

* 如果使用的是 Azure Stack 准备情况检查程序工具，则在部署时所有证书 PFX 文件的密码必须相同。 有关详细信息，请参阅 [使用 Azure Stack 中心就绪检查程序工具为 Azure Stack Edge 创建证书](azure-stack-edge-j-series-create-certificates-tool.md)。

* 证书 PFX 的密码必须是复杂密码。 请记下此密码，因为它用作部署参数。

有关详细信息，请参阅 [导出具有私钥的 PFX 证书](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 边缘使用证书](azure-stack-edge-j-series-manage-certificates.md)

[使用 Azure Stack 中心就绪检查程序工具为 Azure Stack 边缘创建证书](azure-stack-edge-j-series-create-certificates-tool.md)

[导出具有私钥的 PFX 证书](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[证书错误疑难解答](azure-stack-edge-j-series-certificate-troubleshooting.md)