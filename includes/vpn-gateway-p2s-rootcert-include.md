---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "67172810"
---
使用通过企业解决方案生成的根证书（推荐），或者生成自签名证书。 创建根证书后，将公共证书数据（不是私钥）作为 Base64 编码的 X.509 .cer 文件导出。 然后，将公共证书数据上传到 Azure 服务器。

* **企业证书：** 如果你使用的是企业解决方案，则可以使用现有的证书链。 获取要使用的根证书的 .cer 文件。
* **自签名根证书：** 如果未使用企业证书解决方案，请创建自签名根证书。 否则，创建的证书将不兼容 P2S 连接，客户端在尝试连接时会收到连接错误。 可以使用 Azure PowerShell、MakeCert 或 OpenSSL。 以下文章中的步骤介绍了如何生成兼容的自签名根证书：

  * [Windows 10 PowerShell 指令](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md)：这些指令需要 Windows 10 和 PowerShell 才能生成证书。 从根证书生成的客户端证书可以安装在任何受支持的 P2S 客户端上。
  * [MakeCert 指令](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)：使用 MakeCert 的前提是，无法接触用于生成证书的 Windows 10 计算机。 虽然 MakeCert 已弃用，但仍可使用它来生成证书。 从根证书生成的客户端证书可以安装在任何受支持的 P2S 客户端上。
  * [Linux 说明](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
