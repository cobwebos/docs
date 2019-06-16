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
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157371"
---
如果在连接时遇到问题，请检查以下项：

- 如果你已通过**证书导出向导**导出客户端证书，请确保已将其导出为 .pfx 文件并选中了“包括证书路径中的所有证书(如果可能)”。  使用此值将其导出时，也会导出根证书信息。 在客户端计算机上安装证书后，还会安装 .pfx 文件中的根证书。 若要验证是否安装了根证书，请打开“管理用户证书”  ，然后选择“受信任的根证书颁发机构\证书”  。 验证是否列出了根证书，必须存在根证书才能进行身份验证。

- 如果使用的是由企业 CA 解决方案颁发的证书，并且无法进行身份验证，请在客户端证书上验证身份验证顺序。 通过双击客户端证书，选择“详细信息”选项卡并选择“增强型密钥用法”来检查身份验证列表顺序。   确保此列表中的第一项是“客户端身份验证”。  如果不是，请基于将“客户端身份验证”作为列表中第一项的用户模板颁发客户端证书。 

- 如需更多的 P2S 故障排除信息，请参阅[排查 P2S 连接问题](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。