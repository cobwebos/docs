---
title: 为 Azure NetApp 文件配置 NFSv 4.1 Kerberos 加密 |Microsoft Docs
description: 介绍如何为 Azure NetApp 文件配置 NFSv 4.1 Kerberos 加密以及对性能的影响。
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
ms.date: 9/29/2020
ms.author: b-juche
ms.openlocfilehash: b683719fa2d0c1e7b5333c2ddf9c93f2797ade9b
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461472"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>为 Azure NetApp 文件配置 NFSv 4.1 Kerberos 加密

Azure NetApp 文件在 Kerberos 模式下支持 NFS 客户端加密 (krb5.conf、krb5i 和 krb5p) 与 AES-256 加密。 本文介绍将 NFSv 4.1 卷与 Kerberos 加密配合使用所需的配置。

## <a name="requirements"></a>要求

以下要求适用于 NFSv 4.1 客户端加密： 

* Active Directory 域服务 (AD DS) 连接以便于 Kerberos 票证 
* 为客户端和 Azure NetApp 文件创建 DNS A/PTR 记录 NFS 服务器 IP 地址
* Linux 客户端  
    本文为 RHEL 和 Ubuntu 客户端提供指导。  其他客户端将使用类似的配置步骤。 
* NTP 服务器访问  
    你可以使用 (AD DC) 域控制器的常用 Active Directory 域控制器之一。

## <a name="create-an-nfs-kerberos-volume"></a>创建 NFS Kerberos 卷

1.  按照为 [Azure NetApp 文件创建 NFS 卷](azure-netapp-files-create-volumes.md) 中的步骤创建 nfsv 4.1 卷。   

    在 "创建卷" 页上，将 NFS 版本设置为 **nfsv 4.1**，并将 Kerberos 设置为 " **已启用**"。

    > [!IMPORTANT] 
    > 创建卷后，无法修改 Kerberos 启用选项。

    ![创建 NFSv 4.1 Kerberos 卷](../media/azure-netapp-files/create-kerberos-volume.png)  

2. 选择 " **导出策略** "，以匹配所需的访问级别和安全选项 (kerberos 5、kerberos 5I 或 kerberos 5p) 。   

    对于 Kerberos 的性能影响，请参阅 [nfsv 4.1 上 Kerberos 的性能影响](#kerberos_performance)。  

    你还可以通过在 Azure NetApp 文件导航窗格中单击 "导出策略" 来修改卷的 Kerberos 安全方法。

3.  单击 " **查看 + 创建** " 创建 nfsv 4.1 卷。

## <a name="configure-the-azure-portal"></a>配置 Azure 门户 

1.  按照 [创建 Active Directory 连接](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)中的说明进行操作。  

    Kerberos 要求你至少在 Active Directory 中创建一个计算机帐户。 提供的帐户信息用于创建 SMB *和* Nfsv 4.1 Kerberos 卷的帐户。 创建卷时，将自动创建此计算机。

2.  在 " **Kerberos 领域**" 下，输入 **AD 服务器名称** 和 **KDC IP** 地址。

    AD 服务器和 KDC IP 可以是同一服务器。 此信息用于创建由 Azure NetApp 文件使用的 SPN 计算机帐户。 创建计算机帐户后，Azure NetApp 文件将使用 DNS 服务器记录根据需要定位其他 KDC 服务器。 

    ![Kerberos 领域](../media/azure-netapp-files/kerberos-realm.png)
 
3.  单击 " **联接** " 以保存配置。

## <a name="configure-active-directory-connection"></a>配置 Active Directory 连接 

配置 NFSv 4.1 Kerberos 会在 Active Directory 中创建两个计算机帐户：
* SMB 共享的计算机帐户
* NFSv 4.1 的计算机帐户-可以通过前缀来识别此帐户 `NFS-` 。 

创建第一个 NFSv 4.1 Kerberos 卷后，请使用以下 PowerShell 命令设置加密类型或计算机帐户：

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>配置 NFS 客户端 

按照为 [Azure NetApp 文件配置 nfs 客户端](configure-nfs-clients.md) 中的说明配置 nfs 客户端。  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>装载 NFS Kerberos 卷

1. 从 " **卷** " 页中，选择要装载的 NFS 卷。

2. 从卷中选择 " **装入说明** " 以显示说明。

    例如： 

    ![Kerberos 卷的装载说明](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. 为新卷 (装入点) 创建目录。  

4. 为计算机帐户将默认加密类型设置为 AES 256：  
    `Set-ADComputer $COMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * 对于每个计算机帐户，只需运行一次此命令。
    * 可以从域控制器或安装了 [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) 的 PC 上运行此命令。 
    * `$COMPUTERACCOUNT`变量是在部署 Kerberos 卷 Active Directory 中创建的计算机帐户。 这是前缀为的帐户 `NFS-` 。 
    * `$ANFSERVICEACCOUNT`变量是在创建计算机帐户的组织单位上具有委派控制权限的非特权 Active Directory 用户帐户。 

5. 在主机上装载卷： 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT`变量是 `host:/export` 在装载说明中找到的路径。
    * `$ANFMOUNTPOINT`变量是 Linux 主机上用户创建的文件夹。

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>NFSv 4.1 上的 Kerberos 对性能的影响 

本部分帮助你了解 NFSv 4.1 上 Kerberos 的性能影响。

### <a name="available-security-options"></a>可用的安全选项 

当前可用于 NFSv 4.1 卷的安全选项如下所示： 

* **sec = sys** 使用本地 UNIX Uid 和 gid，方法是使用 AUTH_SYS 对 NFS 操作进行身份验证。
* **sec = krb5.conf** 使用 Kerberos V5 而不是本地 UNIX Uid 和 gid 来对用户进行身份验证。
* **sec = krb5i** 使用 Kerberos V5 进行用户身份验证，并使用安全校验和对 NFS 操作执行完整性检查，以防止数据篡改。
* **sec = krb5p** 使用 Kerberos V5 进行用户身份验证和完整性检查。 它会加密 NFS 流量，以防止流量被探查。 此选项是最安全的设置，但它还涉及到最大的性能开销。

### <a name="performance-vectors-tested"></a>已测试性能向量

本部分介绍各种选项的单个客户端性能影响 `sec=*` 。

* 对性能的影响在两个级别进行了测试：低并发性 (低负载) 和高并发性 (i/o 和吞吐量) 的上限。  
* 测试了三种类型的工作负荷：  
    * 小型操作随机读取/写入 (使用 FIO) 
    * 使用 FIO) 的大型操作顺序读取/写入 (
    * 由应用程序（例如 git）生成的元数据繁重工作负荷

### <a name="expected-performance-impact"></a>预期的性能影响 

主要有两个方面：光源负载和上限。 以下列表描述了安全设置和方案按方案列出的性能影响。 对安全参数进行所有比较 `sec=sys` 。 测试是使用单个客户端在单个卷上完成的。 

Krb5.conf 对性能的影响：

* 低并发性 (r/w) ：
    * 顺序延迟增加了0.3 毫秒。
    * 随机 i/o 延迟增加了0.2 毫秒。
    * 元数据 i/o 延迟增加了0.2 毫秒。
* 高并发性 (r/w) ： 
    * 最大顺序吞吐量为不受影响 by krb5.conf。
    * 当工作负荷转移到纯写入时，将总体影响降到零时，最大随机 i/o 数将减少30%，并将总体影响降到零。 
    * 最大元数据工作负荷减少30%。

Krb5i 对性能的影响： 

* 低并发性 (r/w) ：
    * 顺序延迟增加了0.5 毫秒。
    * 随机 i/o 延迟增加了0.2 毫秒。
    * 元数据 i/o 延迟增加了0.2 毫秒。
* 高并发性 (r/w) ： 
    * 最大顺序吞吐率降低了70%，而不考虑工作负荷组合。
    * 当工作负荷转移到纯写入时，整体影响降低到25% 时，最大随机 i/o 数降低了50%。 
    * 最大元数据工作负荷减少30%。

Krb5p 对性能的影响：

* 低并发性 (r/w) ：
    * 顺序延迟增加了0.8 毫秒。
    * 随机 i/o 延迟增加了0.2 毫秒。
    * 元数据 i/o 延迟增加了0.2 毫秒。
* 高并发性 (r/w) ： 
    * 最大顺序吞吐率降低了85%，而不考虑工作负荷组合。 
    * 当工作负荷转移到纯写入时，整体影响降低到43% 时，最大随机 i/o 数降低了65%。 
    * 最大元数据工作负荷减少30%。

## <a name="next-steps"></a>后续步骤  

* [有关 Azure NetApp 文件的常见问题解答](azure-netapp-files-faqs.md)
* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建 Active Directory 连接器](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md) 
