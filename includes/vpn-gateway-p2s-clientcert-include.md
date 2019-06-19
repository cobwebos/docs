---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 31ccf14c82f6248c74d6af932fe9e338d26d2747
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157222"
---
在使用点到站点连接连接到 VNet 的每台客户端计算机上，必须安装客户端证书。 请从根证书生成它，然后将它安装在每个客户端计算机上。 如果未安装有效的客户端证书，则当客户端尝试连接到 VNet 时，身份验证会失败。

可以为每个客户端生成唯一证书，也可以对多个客户端使用同一证书。 生成唯一客户端证书的优势是能够吊销单个证书。 否则，如果多个客户端使用相同的客户端证书进行身份验证而你将其撤销，则需为所有使用该证书的客户端生成并安装新证书。

可以通过以下方法生成客户端证书：

- **企业证书：**

  - 如果使用的是企业证书解决方案，生成具有通用名称值格式的客户端证书*名称\@yourdomain.com*。 而不要使用“域名\用户名”格式。 
  - 请确保客户端证书基于“用户”证书模板，该模板将“客户端身份验证”列为用户列表中的第一项。  检查证书的方式是：双击证书，然后在“详细信息”选项卡中查看“增强型密钥用法”   。

- **自签名根证书：** 按照下述某篇 P2S 证书文章中的步骤操作，使创建的客户端证书兼容 P2S 连接。 这些文章中的步骤可生成兼容的客户端证书： 

  * [Windows 10 PowerShell 说明](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert)：这些说明要求使用 Windows 10 和 PowerShell 来生成证书。 生成的证书可以安装在任何受支持的 P2S 客户端上。
  * [MakeCert 说明](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)：如果无权访问 Windows 10 计算机来生成证书，请使用 MakeCert。 虽然 MakeCert 已弃用，但仍可使用它来生成证书。 可以将生成的证书安装在任何受支持的 P2S 客户端上。
  * [Linux 说明](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  从自签名根证书生成客户端证书时，该证书会自动安装在用于生成该证书的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，请以 .pfx 文件格式导出该证书以及整个证书链。 这样做会创建一个 .pfx 文件，其中包含的根证书信息是客户端进行身份验证所必需的。 

**导出证书**

如需导出证书的步骤，请参阅[使用 PowerShell 为点到站点连接生成和导出证书](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)。
