---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97d33bfcc8251b10ba121b7fb013800904450563
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>能否将我自己的内部 PKI 根 CA 用于点到站点连接？

是的。 以前只可使用自签名根证书。 仍可上传 20 个根证书。

### <a name="what-tools-can-i-use-to-create-certificates"></a>可以使用哪些工具来创建证书？

可以使用企业 PKI 解决方案（内部 PKI）、Azure PowerShell、MakeCert 和 OpenSSL。

### <a name="certsettings"></a>是否有证书设置和参数的说明？

* **内部 PKI/企业 PKI 解决方案：**请参阅[生成证书](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)的步骤。

* **Azure PowerShell：**请参阅 [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) 一文了解相关步骤。

* **MakeCert：**请参阅 [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) 一文了解相关步骤。

* **OpenSSL：** 

    * 导出证书时，请务必将根证书转换为 Base64。

    * 对于客户端证书：

      * 创建私钥时，请将长度指定为 4096。
      * 创建证书时，对于 *-extensions* 参数，指定 *usr_cert*。