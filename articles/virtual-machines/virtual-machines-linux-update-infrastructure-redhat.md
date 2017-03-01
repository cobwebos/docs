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
ms.date: 02/13/2017
ms.author: borisb
translationtype: Human Translation
ms.sourcegitcommit: 6b54633b6beed738a93070aa4235ee4e24333b5e
ms.openlocfilehash: e1867aa3c5339373b494744ba26c750bcc11b5b5
ms.lasthandoff: 02/16/2017


---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>用于 Azure 中按需 Red Hat Enterprise Linux VM 的 Red Hat 更新基础结构 (RHUI)
从 Azure 应用商店中提供的按需 Red Hat Enterprise Linux (RHEL) 映像创建的虚拟机已注册，以访问 Azure 中部署的 Red Hat 更新基础结构 (RHUI)。  按需 RHEL 实例可以访问区域 yum 存储库，并且能够接收增量更新。

受 RHUI 管理的 yum 存储库列表是预配时在 RHEL 实例中配置的。 无需执行任何其他配置，只需在 RHEL 实例准备好获取最新更新后运行 `yum update` 即可。

> [!NOTE]
> 2016 年 9 月，我们部署了更新的 Azure RHUI；2017 年 1 月，我们开始分阶段关闭旧版 Azure RHUI。 如果你从 2016 年 9 月或其后开始使用 RHEL 映像（或其快照），很可能无需执行任何操作。 但是，如果拥有更旧版快照/VM，则需要更新其配置才能不间断地访问 Azure RHUI。
> 

## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure 基础结构更新
自 2016 年 9 月起，Azure 拥有一组新的 Red Hat 更新基础结构 (RHUI) 服务器。 这些服务器使用 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)部署，因此任何 VM 都可以使用单一终结点 (rhui-1.micrsoft.com)，而无需考虑区域。 Azure 应用商店中的新 RHEL 即用即付 (PAYG) 映像（日期为 2016 年 9 月的版本或更高版本）指向新的 Azure RHUI 服务器，不需要执行任何其他操作。

### <a name="determine-if-action-is-required"></a>确定是否需要执行操作
如果从 Azure RHEL PAYG VM 连接到 Azure RHUI 时遇到问题，请按照下列步骤操作
1. 检查 Azure RHUI 终结点的 VM 配置

    检查 `/etc/yum.repos.d/rh-cloud.repo` 文件 `[rhui-microsoft-azure-rhel*]` 部分的 baseurl 是否包含对 `rhui-[1-3].microsoft.com` 的引用。 如果包含，则你正在使用新版 Azure RHUI。

    如果它指向 `mirrorlist.*cds[1-4].cloudapp.net` 模式的位，则需要进行配置更新。

    如果使用是新配置而仍无法连接到 Azure RHUI，请则使用 Microsoft 或 Red Hat 发起支持案例。

    > [!NOTE]
    > 对 Azure 托管的 RHUI 的访问限制为 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)内的 VM。
    > 

2. 如果在执行此检查时仍可使用旧版 Azure RHUI，而你想自动更新配置，请执行以下命令：

    `sudo yum update RHEL6` 或 `sudo yum update RHEL7`，具体取决于 RHEL 系列版本。

3. 如果无法再连接到旧版 Azure RHUI，请按照下一节中所述的手动步骤操作。

4. 请确保更新设置受影响的 VM 的源映像/快照上的配置。

### <a name="phased-shutdown-of-the-old-azure-rhui"></a>旧 Azure RHUI 的分阶段关闭
在旧 Azure RHUI 的关闭期间，我们如下限制对它的访问：

1. 进一步限制访问 (ACL) 已连接到它的 IP 地址集。 可能的副作用：如果继续使用旧版 Azure RHUI，新 VM 可能无法连接到它。 具有动态 IP、经历关机/解除分配/启动序列的 RHEL VM 可能会收到新的 IP，因此也可能开始无法连接到旧版 Azure RHUI

2. 关闭镜像内容传送服务器。 可能的副作用：随着更多 CDS 的关闭，可能出现 `yum update` 服务时间延长、超时次数增多，最终无法再连接到旧版 Azure RHUI。

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>新 RHUI 内容传送服务器的 IP 是
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

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>使用新 Azure RHUI 服务器的手动更新过程
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

下载、验证和安装客户端 RPM

下载：适用于 RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

适用于 RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
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

完成后，确认可以从 VM 访问 Azure RHUI

### <a name="all-in-one-script-for-automating-the-preceding-task"></a>用于自动执行上述任务的多合一脚本
根据需要使用以下脚本，自动执行将受影响的 VM 更新到新的 Azure RHUI 服务器的任务。

```sh
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>RHUI 概述
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) 提供高度可缩放的解决方案，用于为 Red Hat 认证的云提供商所托管的 Red Hat Enterprise Linux 云实例管理 yum 存储库内容。 在上游 Pulp 项目的基础上，云提供商可以通过 RHUI 在本地镜像 Red Hat 所托管的存储库内容、使用自己的内容创建自定义存储库，以及使用经过负载均衡的内容传送系统将这些存储库提供给庞大的最终用户群。

## <a name="regions-where-rhui-is-available"></a>提供 RHUI 的区域
在 RHEL 按需映像可用的所有区域中都提供了 RHUI。 它当前包括 [Azure 状态仪表板](https://azure.microsoft.com/status/)页上列出的所有公共区域、Azure 美国政府区域和 Azure 德国区域。 从 RHEL 按需映像预配的 VM 的 RHUI 访问权限包含在其价格中。 未来扩展 RHEL 按需可用性时，会更新其他区域/国家云可用性。

> [!NOTE]
> 对 Azure 托管的 RHUI 的访问限制为 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)内的 VM。
> 
> 

## <a name="get-updates-from-another-update-repository"></a>从另一个更新存储库获取更新
如果需要从其他更新存储库（而不是 Azure 托管的 RHUI）获取更新，则首先需要从 RHUI 取消注册实例。 然后，需要使用所需的更新基础结构（例如 Red Hat Satellite 或 Red Hat 客户门户 CDN）重新注册它们。 需要这些服务的相应 Red Hat 订阅，并需要注册 [Azure 中的 Red Hat 云访问](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure)。

若要取消注册 RHUI 并重新注册到你的更新基础结构，请执行以下步骤：

1. 编辑 /etc/yum.repos.d/rh-cloud.repo，将所有 `enabled=1` 更改为 `enabled=0`。 例如：
   
   ```bash
   sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
   ```
   
2. 编辑 /etc/yum/pluginconf.d/rhnplugin.conf，将 `enabled=0` 更改为 `enabled=1`。
3. 然后注册到所需的基础结构，如 Red Hat 客户门户。 请按照 [how to register and subscribe a system to the Red Hat Customer Portal](https://access.redhat.com/solutions/253273)（如何在 Red Hat 客户门户中注册和订阅系统）中的 Red Hat 解决方案指南进行操作。

> [!NOTE]
> 对 Azure 托管的 RHUI 的访问权限包含在 RHEL 即用即付 (PAYG) 映像价格中。 从 Azure 托管的 RHUI 取消注册 PAYG RHEL VM 不会将虚拟机转换为自带许可 (BYOL) 类型的 VM。 如果将同一 VM 注册到其他更新源，则可能会导致双重收费：首次为 Azure RHEL 软件费用，第二次为 Red Hat 订阅费用。 
> 
> 如果始终需要使用 Azure 托管的 RHUI 以外的更新基础结构，请考虑创建和部署自己的（BYOL 类型）映像，如[为 Azure 创建和上传基于 Red Hat 的虚拟机](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文中所述。
> 

## <a name="next-steps"></a>后续步骤
若要通过 Azure 应用商店的即用即付映像创建 Red Hat Enterprise Linux VM 并利用 Azure 托管的 RHUI，请转到 [Azure 应用商店](https://azure.microsoft.com/marketplace/partners/redhat/)。 能够在 RHEL 实例中使用 `yum update`，而无需任何其他设置。


