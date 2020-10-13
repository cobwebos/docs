---
title: 排查 Azure NetApp 文件的双协议卷问题 |Microsoft Docs
description: 介绍可帮助你对 Azure NetApp 文件的双重协议问题进行故障排除的错误消息和解决方案。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/01/2020
ms.author: b-juche
ms.openlocfilehash: cc046a27fec1b9e361ff840c7ae0f077e2987b67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654119"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>双重协议卷故障排除

本文介绍创建或管理双协议卷时可能遇到的错误情况的解决方法。

## <a name="error-conditions-and-resolutions"></a>错误情况和解决方法

|     错误条件    |     解决方法    |
|-|-|
| 双重协议卷创建失败并出现错误 `This Active Directory has no Server root CA Certificate` 。    |     如果在创建双协议卷时发生此错误，请确保在 NetApp 帐户中上传根 CA 证书。    |
| 双重协议卷创建失败并出现错误 `Failed to validate LDAP configuration, try again after correcting LDAP configuration` 。    |  请考虑以下解决方法：   <ul><li>在将 Active Directory (AD) 加入到 NetApp 帐户中时，请确保添加了所需的根证书。 请参阅 [上传 Active Directory 证书颁发机构公共根证书](create-volumes-dual-protocol.md#upload-active-directory-certificate-authority-public-root-certificate)。   </li><li>DNS 服务器上可能缺少 AD 主机计算机 (PTR) 记录的指针。 需要在 DNS 服务器上创建反向查找区域，然后在该反向查找区域中添加 AD 主机计算机的 PTR 记录。 <br> 例如，假设 AD 计算机的 IP 地址为 `1.1.1.1` ，使用命令)  (找到的 ad 计算机的主机名 `hostname` `AD1` ，并且域名为 `myDomain.com` 。  添加到反向查找区域中的 PTR 记录应该是 `1.1.1.1`  ->  `AD1.myDomain.com` 。 </li></ul>  |
| 双重协议卷创建失败并出现错误 `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` 。 |  此错误表明 Active Directory 加入到 NetApp 帐户时 AD 密码不正确。 请用正确的密码更新 AD 连接，然后重试。 |
| 双重协议卷创建失败并出现错误 `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` 。 |   此错误表明 DNS 不可访问。 原因可能是 DNS IP 不正确，或者存在网络问题。 检查在 AD 连接中输入的 DNS IP，并确保 IP 正确。 <br> 此外，请确保 AD 和卷位于同一区域和同一 VNet 中。 如果它们在不同的 Vnet 中，请确保在两个 Vnet 之间建立 VNet 对等互连。|
| 在装载双协议卷时，权限被拒绝。 | 双协议卷支持 NFS 协议和 SMB 协议。  当你尝试访问 UNIX 系统上装入的卷时，系统将尝试将你使用的 UNIX 用户映射到 Windows 用户。 如果未找到映射，则会发生 "权限被拒绝" 错误。 <br> 当你使用 "root" 用户进行访问时，这种情况也适用。 <br> 若要避免 "权限被拒绝" 问题，请确保在 `pcuser` 访问装入点之前 Windows Active Directory 包含。 如果在 `pcuser` 遇到 "权限被拒绝" 问题后添加，请等待24小时，以便在再次尝试访问之前清除缓存条目。 |

## <a name="next-steps"></a>后续步骤  

* [创建双协议卷](create-volumes-dual-protocol.md)
* [为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md)
