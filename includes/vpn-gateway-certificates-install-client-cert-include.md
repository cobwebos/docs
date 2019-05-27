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
ms.openlocfilehash: 0068bd151c3d7d243b05c326ec73a201f4131296
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146805"
---
如果想要从另一台客户端计算机（而不是用于生成客户端证书的计算机）创建 P2S 连接，需要安装客户端证书。 安装客户端证书时，需要使用导出客户端证书时创建的密码。

1. 找到 *.pfx* 文件并将其复制到客户端计算机。 在客户端计算机上，双击 *.pfx* 文件以进行安装。 将“存储位置”保留为“当前用户”，并单击“下一步”。
2. 在“要导入的**文件**”页上，不要进行任何更改。 单击“下一步”。
3. 在“私钥保护”页上，输入证书的密码，或验证安全主体是否正确，并单击“下一步”。
4. 在“**证书存储**”页上，保留默认位置，并单击“**下一步**”。
5. 单击“**完成**”。 在证书安装的“**安全警告**”上，单击“**是**”。 可随时单击“是”，因为证书已生成。 现已成功导入证书。