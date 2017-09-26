---
title: "Red Hat 更新基础结构 (RHUI) | Microsoft Docs"
description: "了解用于 Microsoft Azure 中按需 Red Hat Enterprise Linux 实例的 Red Hat 更新基础结构 (RHUI)"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/10/2017
ms.author: borisb
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 43d9095bcbd490abc6d01c214a17b8586f92ed1e
ms.contentlocale: zh-cn
ms.lasthandoff: 09/15/2017

---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>用于 Azure 中按需 Red Hat Enterprise Linux VM 的 Red Hat 更新基础结构 (RHUI)
 [Red Hat 更新基础结构](https://access.redhat.com/products/red-hat-update-infrastructure)允许云提供程序（如 Azure）镜像 Red Hat 托管的存储库内容，创建包含 Azure 特定内容的自定义存储库，并将其提供给最终用户 VM 使用。

已预先配置 Red Hat Enterprise Linux (RHEL) 即用即付 (PAYG) 映像来访问 Azure RHUI。 无需执行任何其他配置，只需在 RHEL 实例准备好获取最新更新后运行 `sudo yum update` 即可。 RHEL PAYG 的软件费涵盖了此服务。

## <a name="important-information-about-azure-rhui"></a>有关 Azure RHUI 的重要信息

1. RHUI 目前仅支持给定 RHEL 系列（RHEL6 或 RHEL7）中的最新次要版本。 运行 `sudo yum update` 时，已连接到 RHUI 的 RHEL VM 实例会升级到最新次要版本。 

    例如，如果从 RHEL 7.2 PAYG 映像预配 VM 并运行 `sudo yum update`，最终会获得 RHEL 7.4 VM（RHEL7 系列中的当前最新次要版本）。

    为了避免此行为，需要根据[为 Azure 创建并上传基于 Red Hat 的虚拟机](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文中所述生成自己的映像，并将其连接到不同的更新基础结构（[直接连接到 Red Hat 内容传送服务器](https://access.redhat.com/solutions/253273)，或连接到 Red Hat Satellite 服务器）。

2. RHEL PAYG 映像价格涵盖了 Azure 托管 RHUI 的访问权限。 从 Azure 托管的 RHUI 取消注册 PAYG RHEL VM 不会将虚拟机转换为自带许可 (BYOL) 类型的 VM。 如果将同一 VM 注册到其他更新源，则可能会导致双重间接收费：首次为 Azure RHEL 软件费用，第二次为以前购买的 Red Hat 订阅费用。 如果始终需要使用 Azure 托管的 RHUI 以外的更新基础结构，请考虑根据[为 Azure 创建和上传基于 Red Hat 的虚拟机](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中所述创建并部署自己的（BYOL 类型）映像。

3. Azure 中的两类 RHEL PAYG 映像（RHEL for SAP HANA、RHEL for SAP Business Applications）已根据 SAP 认证需要，连接到保留在特定 RHEL 次要版本上的专用 RHUI 通道。 

4. 对 Azure 托管的 RHUI 的访问限制为 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)内的 VM。 若要通过本地网络基础结构代理所有 VM 流量，可能需要为 RHEL PAYG VM 设置用户定义的路由才能访问 Azure RHUI。

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI 内容传送服务器的 IP
在 RHEL 按需映像可用的所有区域中都提供了 RHUI。 目前包括 [Azure 状态仪表板](https://azure.microsoft.com/status/)页上列出的所有公共区域、Azure 美国政府区域和 Azure 德国区域。 

如果使用网络配置进一步限制来自 RHEL PAYG VM 的访问，根据所处环境，请确保允许使用以下 IP 以便 `yum update` 能够正常工作。 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure 基础结构更新

2016 年 9 月，我们部署了更新的 Azure RHUI。2017 年 4 月，我们关闭了旧版 Azure RHUI。 如果一直在使用 2016 年 9 月或之后的 RHEL PAYG 映像（或其快照），则会自动连接到新的 Azure RHUI。 但是，如果使用更旧版本的快照/VM，则需要更新其配置才能按如下所述访问 Azure RHUI。

新的Azure RHUI 服务器是使用 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)部署的，因此任何 VM 都可以使用单一终结点 (rhui-1.microsoft.com)，而无需考虑区域。 

### <a name="troubleshooting-connection-problems-to-azure-rhui"></a>排查 Azure RHUI 连接问题
如果从 Azure RHEL PAYG VM 连接到 Azure RHUI 时遇到问题，请遵循以下步骤
1. 检查 Azure RHUI 终结点的 VM 配置

    检查 `/etc/yum.repos.d/rh-cloud.repo` 文件 `[rhui-microsoft-azure-rhel*]` 部分的 baseurl 是否包含对 `rhui-[1-3].microsoft.com` 的引用。 如果是 - 使用的是新 Azure RHUI。

    如果它指向 `mirrorlist.*cds[1-4].cloudapp.net` 模式的位，则需要进行配置更新。 使用的是旧 VM 快照，请将它更新为指向新的 Azure RHUI。

2. 对 Azure 托管 RHUI 的访问限制为 [Microsoft Azure 数据中心 IP 范围] (https://www.microsoft.com/download/details.aspx?id=41653) 内的 VM。
 
3. 如果使用是新配置并已确认 VM 从 Azure IP 范围建立了连接，但仍无法连接到 Azure RHUI - 请向 Microsoft 或 Red Hat 提出支持案例。

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>执行手动更新过程以使用 Azure RHUI 服务器
此过程仅供参考，RHEL PAYG 映像已包含用于连接 Azure RHUI 的正确配置。

（通过 curl）下载公钥签名

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

验证下载的密钥

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

检查输出，验证 `keyid` 和 `user ID packet`：

```bash
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

安装公钥

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

下载、验证和安装客户端 RPM。 

> [!NOTE]
> 包版本已发生更改。若要手动连接到 Azure RHUI，可以通过从库预配最新的映像，找到每个 RHEL 系列的最新版客户端包。
> 

下载：适用于 RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
```

适用于 RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
```

验证：

```bash
rpm -Kv azureclient.rpm
```

检查输出中的包签名是否正确

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

安装 RPM

```bash
sudo rpm -U azureclient.rpm
```

完成后，验证是否可以从 VM 访问 Azure RHUI。

## <a name="next-steps"></a>后续步骤
若要通过 Azure 应用商店的即用即付映像创建 Red Hat Enterprise Linux VM 并利用 Azure 托管的 RHUI，请转到 [Azure 应用商店](https://azure.microsoft.com/marketplace/partners/redhat/)。 
