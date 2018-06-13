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
ms.openlocfilehash: 8a49653b4083cbfd17656d701225dcb14f91f637
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197021"
---
在使用点到站点连接连接到 VNet 的每台客户端计算机上，必须安装客户端证书。 客户端证书从根证书生成，安装在每个客户端计算机上。 如果未安装有效的客户端证书，而客户端尝试连接到 VNet，则身份验证会失败。

可以为每个客户端生成唯一证书，也可以对多个客户端使用同一证书。 生成唯一客户端证书的优势是能够吊销单个证书。 否则，如果多个客户端使用相同的客户端证书，则在需要撤销它时，必须为所有使用该证书进行身份验证的客户端生成并安装新证书。

可以通过以下方法生成客户端证书：

- **企业证书：**

  - 如果使用的是企业证书解决方案，请使用通用名称值格式“name@yourdomain.com”生成客户端证书，而不要使用“域名\用户名”格式。
  - 请确保客户端证书基于“用户”证书模板，该模板使用“客户端身份验证”（而不是“智能卡登录”等）作为使用列表中的第一项。可以通过双击客户端证书，并查看“详细信息”>“增强型密钥用法”来检查证书。

- 自签名根证书：必须遵循下面的 P2S 证书文章之一中的步骤。 否则，创建的客户端证书将不兼容 P2S 连接，客户端在尝试连接时会收到错误。 下述文章之一中的步骤可以生成兼容的客户端证书： 

  * [Windows 10 PowerShell 指令](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert)：这些指令需要 Windows 10 和 PowerShell 才能生成证书。 生成的证书可以安装在任何受支持的 P2S 客户端上。
  * [MakeCert 指令](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)：使用 MakeCert 的前提是，无法接触用于生成证书的 Windows 10 计算机。 MakeCert 已弃用，但仍可使用 MakeCert 生成证书。 生成的证书可以安装在任何受支持的 P2S 客户端上。

  根据上述说明从自签名根证书生成客户端证书时，该证书会自动安装在用于生成该证书的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，则需以 .pfx 格式导出该证书以及整个证书链。 这样会创建一个 .pfx 文件，其中包含的根证书信息是客户端成功进行身份验证所必需的。 有关证书导出步骤，请参阅[证书 - 导出客户端证书](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)。