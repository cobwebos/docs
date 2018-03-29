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
ms.openlocfilehash: 517ebbf739c64c0364dc21386fee86ebc740e997
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
生成客户端证书时，该证书会自动安装在用于生成它的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，需要导出生成的客户端证书。

1. 若要导出客户端证书，请打开“管理用户证书”。 生成的客户端证书默认位于“Certificates - Current User\Personal\Certificates”中。 右键单击要导出的客户端证书，单击“所有任务”，并单击“导出”打开“证书导出向导”。

  ![导出](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. 在“证书导出向导”中，单击“下一步”以继续。

  ![下一步](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. 选择“是，导出私钥”，并单击“下一步”。

  ![导出私钥](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. 在“导出文件格式”页上，保留选择默认值。 请务必选中“包括证书路径中的所有证书(如果可能)”。 此外，此设置还会导出成功客户端身份验证所需的根证书信息。 否则，客户端身份验证会失败，因为客户端没有受信任的根证书。 然后单击“下一步”。

  ![导出文件格式](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. 在“**安全性**”页上，必须保护私钥。 如果选择使用密码，请务必记下或牢记为此证书设置的密码。 然后单击“下一步”。

  ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. 在“要导出的文件”中，“浏览”到要将证书导出的目标位置。 在“文件名”中，为证书文件命名。 然后单击“下一步”。

  ![要导出的文件](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. 单击“完成”导出证书。

  ![完成](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)