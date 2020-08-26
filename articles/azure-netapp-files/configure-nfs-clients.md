---
title: 为 Azure NetApp 文件配置 NFS 客户端 |Microsoft Docs
description: 介绍如何配置 NFS 客户端以与 Azure NetApp 文件一起使用。
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
ms.topic: how-to
ms.date: 08/19/2020
ms.author: b-juche
ms.openlocfilehash: 20cbc9b33e567ffe306aae694bb835d95c2d861e
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88704971"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>为 Azure NetApp 文件配置 NFS 客户端

本文中所述的 NFS 客户端配置是在 [配置 nfsv 4.1 Kerberos 加密](configure-kerberos-encryption.md) 或 [创建双协议卷](create-volumes-dual-protocol.md)时安装的一部分。 可在 Azure NetApp 文件中使用多种 Linux 分发版。 本文介绍两种更常用的环境配置： RHEL 8 和 Ubuntu 18.04。 

无论使用何种 Linux 风格，都需要以下配置：
* 配置 NTP 客户端以避免出现时间偏差问题。
* 配置 Linux 客户端的 DNS 条目以进行名称解析。  
    此配置包括 "A" (前进) 记录和 PTR (反向) 记录。 
* 对于 "域加入"，请在目标 Active Directory (创建一个计算机帐户，该帐户是在领域加入命令) 期间创建的。 
    > [!NOTE] 
    > `$SERVICEACCOUNT`下面的命令中使用的变量应该是具有在目标组织单位中创建计算机帐户的权限或委派的用户帐户。
* 使客户端能够装载 NFS 卷和其他相关的监视工具。

## <a name="rhel-8-configuration"></a>RHEL 8 配置 

1. 安装包：   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. 配置 NTP 客户端：  
    RHEL 8 `chrony` 默认情况下使用。  遵循 [使用 Chrony suite 配置 NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)中的配置准则。

3. 加入 Active Directory 域：  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu 配置 

1. 安装包：  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. 配置 NTP 客户端。  
    Ubuntu 18.04 `chrony` 默认情况下使用。  按照 Ubuntu Bionic 中的配置准则 [进行操作：使用 chrony 配置 NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)。

3. 联接 Active Directory 域：  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>后续步骤  

* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [为 Azure NetApp 文件创建双协议卷](create-volumes-dual-protocol.md)

