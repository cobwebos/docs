---
title: 启用适用于 Linux VM 的 Azure 磁盘加密
description: 本文提供有关如何为 Linux VM 启用 Microsoft Azure 磁盘加密的说明。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5c227c6ab24d6b71445354d1b17d238e80bf6313
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655844"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>适用于 Linux VM 的 Azure 磁盘加密 

Azure 磁盘加密有助于保护数据，使组织能够信守在安全性与符合性方面作出的承诺。 它使用 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能为 Azure 虚拟机 (VM) 的 OS 和数据磁盘提供卷加密，并与 [Azure 密钥保管库](../../key-vault/index.yml)集成，以帮助控制和管理磁盘加密密钥和机密。 

如果使用 [Azure 安全中心](../../security-center/index.yml)，当 VM 未加密时，你会收到警报。 这些警报显示为“高严重性”，建议加密这些 VM。

![Azure 安全中心磁盘加密警报](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果之前是结合使用 Azure 磁盘加密和 Azure AD 来加密 VM，必须继续使用这种方式来加密 VM。 有关详细信息，请参阅[结合使用 Azure 磁盘加密和 Azure AD（以前的版本）](disk-encryption-overview-aad.md)。 
> - 某些建议可能会导致数据、网络或计算资源使用量增加，从而产生额外许可或订阅成本。 必须具有有效的活动 Azure 订阅，才能在 Azure 的受支持区域中创建资源。
> - 当前第 2 代 VM 不支持 Azure 磁盘加密。 有关详细信息，请参阅 [Azure 中对第 2 代 VM 的支持](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)。

只需花几分钟时间学习[使用 Azure CLI 创建 Linux VM 并对其进行加密快速入门](disk-encryption-cli-quickstart.md)或者[使用 Azure PowerShell 创建 Linux VM 并对其进行加密快速入门](disk-encryption-powershell-quickstart.md)，即可了解适用于 Linux 的 Azure 磁盘加密的基础知识。

## <a name="supported-vms-and-operating-systems"></a>支持的 VM 和操作系统

### <a name="supported-vms"></a>支持的 VM

Linux VM 具有[各种大小](sizes.md)。 在[基本、A 系列 VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/) 或不满足以下最低内存要求的虚拟机上，Azure 磁盘加密不可用：

| 虚拟机 | 内存最低要求 |
|--|--|
| Linux VM（仅加密数据卷时）| 2 GB |
| Linux VM（加密数据和 OS 卷，并且根 (/) 文件系统使用量为 4GB 或更低时） | 8 GB |
| Linux VM（加密数据和 OS 卷，并且根 (/) 文件系统使用量大于 4GB 时） | 根文件系统使用量 * 2。 例如，根文件系统使用量为 16 GB，则至少需要 32GB 的 RAM |

在 Linux 虚拟机上完成 OS 磁盘加密进程后，可将 VM 配置为以更少的内存运行。 

Azure 磁盘加密还可用于使用高级存储的 VM。

Azure 磁盘加密在[第 2 代 VM](generation-2.md#generation-1-vs-generation-2-capabilities) 和 [Lsv2 系列 VM](../lsv2-series.md) 上不可用。 有关更多特例，请参阅 [Azure 磁盘加密：不支持的方案](disk-encryption-linux.md#unsupported-scenarios)。

### <a name="supported-operating-systems"></a>支持的操作系统

[Azure 认可的 Linux 发行版](endorsed-distros.md)的子集支持 Azure 磁盘加密，而 Linux 发行版本身就是所有 Linux 服务器可能的发行版的子集。

![支持 Azure 磁盘加密的 Linux 服务器发行版的维恩图](./media/disk-encryption/ade-supported-distros.png)

未经 Azure 认可的 Linux 服务器发行版不支持 Azure 磁盘加密，而在认可的那些发行版中，只有以下发行版和版本支持 Azure 磁盘加密：

| 发布者 | 产品/服务 | SKU | URN | 支持加密的卷类型 |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18.04-LTS | Canonical:UbuntuServer:18.04-LTS:latest | OS 和数据磁盘 |
| Canonical | Ubuntu 18.04 | 18.04-DAILY-LTS | Canonical:UbuntuServer:18.04-DAILY-LTS:latest | OS 和数据磁盘 |
| Canonical | Ubuntu 16.04 | 16.04-DAILY-LTS | Canonical:UbuntuServer:16.04-DAILY-LTS:latest | OS 和数据磁盘 |
| Canonical | Ubuntu 14.04.5</br>[其 Azure 优化内核更新到 4.15 或更高版本](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical:UbuntuServer:14.04.5-LTS:latest | OS 和数据磁盘 |
| Canonical | Ubuntu 14.04.5</br>[其 Azure 优化内核更新到 4.15 或更高版本](disk-encryption-troubleshooting.md) | 14.04.5-DAILY-LTS | Canonical:UbuntuServer:14.04.5-DAILY-LTS:latest | OS 和数据磁盘 |
| RedHat | RHEL 7.7 | 7.7 | RedHat:RHEL:7.7:latest | OS 和数据磁盘（请参阅下面的注释） |
| RedHat | RHEL 7.7 | 7-RAW | RedHat:RHEL:7-RAW:latest | OS 和数据磁盘（请参阅下面的注释） |
| RedHat | RHEL 7.7 | 7-LVM | RedHat:RHEL:7-LVM:latest | OS 和数据磁盘（请参阅下面的注释） |
| RedHat | RHEL 7.6 | 7.6 | RedHat:RHEL:7.6:latest | OS 和数据磁盘（请参阅下面的注释） |
| RedHat | RHEL 7.5 | 7.5 | RedHat:RHEL:7.5:latest | OS 和数据磁盘（请参阅下面的注释） |
| RedHat | RHEL 7.4 | 7.4 | RedHat:RHEL:7.4:latest | OS 和数据磁盘（请参阅下面的注释） |
| RedHat | RHEL 7.3 | 7.3 | RedHat:RHEL:7.3:latest | OS 和数据磁盘（请参阅下面的注释） |
| RedHat | RHEL 7.2 | 7.2 | RedHat:RHEL:7.2:latest | OS 和数据磁盘（请参阅下面的注释） |
| RedHat | RHEL 6.8 | 6.8 | RedHat:RHEL:6.8:latest | 数据磁盘（请参阅下面的注释） |
| RedHat | RHEL 6.7 | 6.7 | RedHat:RHEL:6.7:latest | 数据磁盘（请参阅下面的注释） |
| OpenLogic | CentOS 7.7 | 7.7 | OpenLogic:CentOS:7.7:latest | OS 和数据磁盘 |
| OpenLogic | CentOS 7.7 | 7-LVM | OpenLogic:CentOS:7-LVM:latest | OS 和数据磁盘 |
| OpenLogic | CentOS 7.6 | 7.6 | OpenLogic:CentOS:7.6:latest | OS 和数据磁盘 |
| OpenLogic | CentOS 7.5 | 7.5 | OpenLogic:CentOS:7.5:latest | OS 和数据磁盘 |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic:CentOS:7.4:latest | OS 和数据磁盘 |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic:CentOS:7.3:latest | OS 和数据磁盘 |
| OpenLogic | CentOS 7.2n | 7.2n | OpenLogic:CentOS:7.2n:latest | OS 和数据磁盘 |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic:CentOS:7.1:latest | 仅数据磁盘 |
| OpenLogic | CentOS 7.0 | 7.0 | OpenLogic:CentOS:7.0:latest | 仅数据磁盘 |
| OpenLogic | CentOS 6.8 | 6.8 | OpenLogic:CentOS:6.8:latest | 仅数据磁盘 |
| SUSE | openSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:latest | 仅数据磁盘 |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE:SLES:12-SP4:latest | 仅数据磁盘 |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:latest | 仅数据磁盘 |

> [!NOTE]
> RHEL OS 和 RHEL7 即用即付映像的数据磁盘支持实现新的 Azure 磁盘加密。  
>
> RHEL 自带订阅黄金映像仅在注册了订阅之后支持 ADE。 有关详细信息，请参阅 [Azure 中的 Red Hat Enterprise Linux 自带订阅黄金映像](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>其他 VM 要求

Azure 磁盘加密要求系统上存在 dm-crypt 和 vfat 模块。 从默认映像中删除或禁用 vfat 将阻止系统读取密钥卷，并阻止获取在后续重启时解锁磁盘所需的密钥。 从系统中删除 vfat 模块的系统强化步骤与 Azure 磁盘加密不兼容。 

在启用加密之前，必须在 /etc/fstab 中正确列出要加密的数据磁盘。 创建条目时，请使用“nofail”选项，然后选择一个永久性的块设备名称（因为采用“/dev/sdX”格式的设备名称在重启期间可能不会与同一磁盘关联，尤其是在加密之后。有关此行为的更多详细信息，请参阅：[排查 Linux VM 设备名称更改问题](troubleshoot-device-names-problems.md)）。

确保正确配置用于装载的 /etc/fstab 设置。 若要配置这些设置，请运行 mount -a 命令，或重新启动 VM 并以这种方法触发重新装载。 装载完成后，检查 lsblk 命令的输出，以验证驱动器是否仍已装载。 

- 如果在启用加密之前 /etc/fstab 文件未正确装载该驱动器，则 Azure 磁盘加密无法将其正确装载。
- 在加密过程中，Azure 磁盘加密进程会将装载信息移出 /etc/fstab，并移入其自身的配置文件中。 数据驱动器加密完成后，如果看到 /etc/fstab 中缺少条目，请不要担心。
- 在开始加密之前，请务必停止所有可能正在写入已装载的数据磁盘的服务和进程并禁用它们，以使它们在重启后不会自动重启。 这可能会使文件在这些分区上保持打开状态，从而阻止加密过程重新装载它们，导致加密失败。 
- 重新启动后，Azure 磁盘加密进程需要花费一段时间来装载新加密的磁盘。 重新启动后，这些磁盘并不是立即可用。 该进程需要一段时间来启动、解锁然后装载加密的驱动器，然后，这些驱动器才可供其他进程访问。 重新启动后，此进程可能需要一分钟以上的时间，具体时间取决于系统特征。

下面是用于装载数据磁盘和创建必要的 /etc/fstab 条目的命令示例：

```bash
UUID0="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun0)"
UUID1="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun1)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>网络要求

若要启用 Azure 磁盘加密功能，Linux VM 必须符合以下网络终结点配置要求：
  - 要获取用于连接密钥保管库的令牌，Linux VM 必须能够连接到 Azure Active Directory 终结点 \[login.microsoftonline.com\]。
  - 要将加密密钥写入密钥保管库，Linux VM 必须能够连接到密钥保管库终结点。
  - Linux VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure VM 到 Internet 的访问，可以解析上述 URI，并配置特定的规则以允许与这些 IP 建立出站连接。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。  

## <a name="encryption-key-storage-requirements"></a>加密密钥存储要求  

Azure 磁盘加密要求使用 Azure 密钥保管库来控制和管理磁盘加密密钥和机密。 密钥保管库和 VM 必须位于同一 Azure 区域和订阅中。

有关详细信息，请参阅[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。

## <a name="terminology"></a>术语
下表定义了 Azure 磁盘加密文档中使用的一些常用术语：

| 术语 | 定义 |
| --- | --- |
| Azure Key Vault | Key Vault 是基于联邦信息处理标准 (FIPS) 验证的硬件安全模块。 这些标准有助于保护加密密钥和敏感机密。 有关详细信息，请参阅 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)文档和[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 经过了优化，可从命令行管理 Azure 资源。|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) 是基于 Linux 的透明磁盘加密子系统，用于在 Linux VM 上启用磁盘加密。 |
| 密钥加密密钥 (KEK) | 可用于保护或包装机密的非对称密钥 (RSA 2048)。 可提供硬件安全模块 (HSM) 保护的密钥或软件保护的密钥。 有关详细信息，请参阅 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)文档和[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。 |
| PowerShell cmdlet | 有关详细信息，请参阅 [Azure PowerShell cmdlet](/powershell/azure/overview)。 |


## <a name="next-steps"></a>后续步骤

- [快速入门 - 使用 Azure CLI 创建和加密 Linux VM](disk-encryption-cli-quickstart.md)
- [快速入门 - 使用 Azure Powershell 创建和加密 Linux VM](disk-encryption-powershell-quickstart.md)
- [Linux VM 上的 Azure 磁盘加密方案](disk-encryption-linux.md)
- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)


