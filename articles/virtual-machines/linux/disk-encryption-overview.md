---
title: 启用适用于 Linux VM 的 Azure 磁盘加密
description: 本文提供了有关为 Linux Vm 启用 Microsoft Azure 磁盘加密的说明。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 80ca7d8ed89ba0a3b196f1b2b29384c749cf1b22
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792541"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>适用于 Linux VM 的 Azure 磁盘加密 

Azure 磁盘加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 它使用 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能为 Azure 虚拟机 (VM) 的 OS 和数据磁盘提供卷加密，并与 [Azure Key Vault](../../key-vault/index.yml) 集成，帮助你控制和管理磁盘加密密钥和机密。 

如果使用 [Azure 安全中心](../../security-center/index.yml)，则当 VM 未加密时，你会收到警报。 这些警报显示为“高严重性”，建议加密这些 VM。

![Azure 安全中心磁盘加密警报](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果之前是使用 Azure 磁盘加密与 Azure AD 来加密 VM，则必须继续使用此选项来加密 VM。 有关详细信息，请参阅[使用 Azure AD 进行的 Azure 磁盘加密（以前的版本）](disk-encryption-overview-aad.md)。 
> - 某些建议可能会导致数据、网络或计算资源使用量增加，从而产生额外的许可或订阅成本。 必须具有有效的活动 Azure 订阅，才能在 Azure 的受支持区域中创建资源。
> - 目前，第 2 代 VM 不支持 Azure 磁盘加密。 有关详细信息，请参阅 [Azure 对第 2 代 VM 的支持](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)。

只需几分钟即可了解适用于 Linux 的 Azure 磁盘加密基础知识，只需使用[Azure CLI 快速入门创建和加密 LINUX vm](disk-encryption-cli-quickstart.md) ，或者[使用 Azure PowerShell 快速入门创建和加密 linux vm](disk-encryption-powershell-quickstart.md)即可。

## <a name="supported-vms-and-operating-systems"></a>支持的 VM 和操作系统

### <a name="supported-vms"></a>支持的 VM

Linux VM 提供了[多种大小](sizes.md)。 Azure 磁盘加密不适用于[基本的 A 系列 VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/)，也不适用于不符合以下最低内存要求的虚拟机：

| 虚拟机 | 最低内存要求 |
|--|--|
| Linux VM（仅当加密数据卷时）| 2 GB |
| Linux VM（加密数据卷和 OS 卷，并且根 (/) 文件系统占用的空间为 4GB 或更少） | 8 GB |
| Linux VM（加密数据卷和 OS 卷，并且根 (/) 文件系统占用的空间大于 4GB） | 根文件系统空间占用量 * 2。 例如，如果根文件系统的空间占用量为 16 GB，则至少需要 32GB RAM |

在 Linux 虚拟机上完成 OS 磁盘加密过程后，可将 VM 配置为以更少的内存运行。 

Azure 磁盘加密还可用于使用高级存储的 VM。

Azure 磁盘加密不适用于[第2代 vm](generation-2.md#generation-1-vs-generation-2-capabilities)）和[Lsv2 系列 vm](../lsv2-series.md)）。 有关更多例外，请参阅 [Azure 磁盘加密：不支持的方案](disk-encryption-linux.md#unsupported-scenarios)。

### <a name="supported-operating-systems"></a>支持的操作系统

有一部分 [Azure 认可的 Linux 分发版](endorsed-distros.md)（其本身是所有可能的 Linux 服务器分发版的一部分）支持 Azure 磁盘加密。

![支持 Azure 磁盘加密的 Linux 服务器分发版文氏图](./media/disk-encryption/ade-supported-distros.png)

未经 Azure 认可的 Linux 服务器发行版不支持 Azure 磁盘加密；在认可的发行版中，只有以下发行版和版本支持 Azure 磁盘加密：

| 发布者 | 产品/服务 | SKU | URN | 支持加密的卷类型 |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18.04-LTS | 规范： UbuntuServer： 18.04-LTS：最新 | OS 和数据磁盘 |
| Canonical | Ubuntu 18.04 | 18.04-每日-LTS | 规范： UbuntuServer： 18.04-LTS：最新 | OS 和数据磁盘 |
| Canonical | Ubuntu 16.04 | 16.04-DAILY-LTS | 规范： UbuntuServer： 16.04-LTS：最新 | OS 和数据磁盘 |
| Canonical | Ubuntu 14.04。5</br>[其 Azure 优化内核更新到 4.15 或更高版本](disk-encryption-troubleshooting.md) | 14.04.5-LTS | 规范： UbuntuServer： 14.04.5-LTS：最新 | OS 和数据磁盘 |
| Canonical | Ubuntu 14.04。5</br>[其 Azure 优化内核更新到 4.15 或更高版本](disk-encryption-troubleshooting.md) | 14.04.5-DAILY-LTS | 规范： UbuntuServer： 14.04.5-LTS：最新 | OS 和数据磁盘 |
| RedHat | RHEL 7。7 | 7.7 | RedHat： RHEL：7.7：最新 | OS 和数据磁盘（参阅下面的注释） |
| RedHat | RHEL 7。7 | 7-RAW | RedHat： RHEL： 7-RAW：最新 | OS 和数据磁盘（参阅下面的注释） |
| RedHat | RHEL 7。7 | 7-LVM | RedHat： RHEL： 7-LVM：最新 | OS 和数据磁盘（参阅下面的注释） |
| RedHat | RHEL 7。6 | 7.6 | RedHat： RHEL：7.6：最新 | OS 和数据磁盘（参阅下面的注释） |
| RedHat | RHEL 7.5 | 7.5 | RedHat： RHEL：7.5：最新 | OS 和数据磁盘（参阅下面的注释） |
| RedHat | RHEL 7.4 | 7.4 | RedHat： RHEL：7.4：最新 | OS 和数据磁盘（参阅下面的注释） |
| RedHat | RHEL 7.3 | 7.3 | RedHat： RHEL：7.3：最新 | OS 和数据磁盘（参阅下面的注释） |
| RedHat | RHEL 7.2 | 7.2 | RedHat： RHEL：7.2：最新 | OS 和数据磁盘（参阅下面的注释） |
| RedHat | RHEL 6。8 | 6.8 | RedHat： RHEL：6.8：最新 | 数据磁盘（参阅下面的注释） |
| RedHat | RHEL 6。7 | 6.7 | RedHat： RHEL：6.7：最新 | 数据磁盘（参阅下面的注释） |
| OpenLogic | CentOS 7。7 | 7.7 | OpenLogic： CentOS：7.7：最新 | OS 和数据磁盘 |
| OpenLogic | CentOS 7。7 | 7-LVM | OpenLogic： CentOS： 7-LVM：最新 | OS 和数据磁盘 |
| OpenLogic | CentOS 7。6 | 7.6 | OpenLogic： CentOS：7.6：最新 | OS 和数据磁盘 |
| OpenLogic | CentOS 7.5 | 7.5 | OpenLogic： CentOS：7.5：最新 | OS 和数据磁盘 |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic： CentOS：7.4：最新 | OS 和数据磁盘 |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic： CentOS：7.3：最新 | OS 和数据磁盘 |
| OpenLogic | CentOS 7.2 n | 7.2n | OpenLogic： CentOS： 7.2 n：最新 | OS 和数据磁盘 |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic： CentOS：7.1：最新 | 仅数据磁盘 |
| OpenLogic | CentOS 7。0 | 7.0 | OpenLogic： CentOS：7.0：最新 | 仅数据磁盘 |
| OpenLogic | CentOS 6.8 | 6.8 | OpenLogic： CentOS：6.8：最新 | 仅数据磁盘 |
| SUSE | openSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:latest | 仅数据磁盘 |
| SUSE | SLES 优先级 12-SP4 | 12-SP4 | SUSE:SLES-Priority:12-SP4:latest | 仅数据磁盘 |
| SUSE | SLES 优先级 12-SP3 | 12-SP3 | SUSE:SLES-Priority:12-SP3:latest | 仅数据磁盘 |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:latest | 仅数据磁盘 |

> [!NOTE]
> RHEL OS 和用于 RHEL7 即用即付映像的数据磁盘支持新的 Azure 磁盘加密。  
>
> RHEL 自带订阅金牌映像也支持 ADE，但仅在注册订阅**后**才支持。 有关详细信息，请参阅[在 Azure 中 Red Hat Enterprise Linux 自带订阅金牌映像](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>其他 VM 要求

Azure 磁盘加密要求系统上存在 dm-dm-crypt 和 vfat 模块。 在默认映像中删除或禁用 vfat 会阻止系统读取密钥卷，以及在后续重新启动时获取用于解锁磁盘的密钥。 从系统中删除 vfat 模块的系统强化步骤与 Azure 磁盘加密不兼容。 

在启用加密之前，要加密的数据磁盘必须在 /etc/fstab 中正确列出。 为此条目使用永久性块设备名，因为每次重新启动后，不能依赖于使用“/dev/sdX”格式的设备名来与同一磁盘相关联，尤其是应用加密后。 有关此行为的更多详细信息，请参阅：[排查 Linux VM 设备名称更改问题](troubleshoot-device-names-problems.md)

确保正确配置用于装载的 /etc/fstab 设置。 若要配置这些设置，请运行 mount -a 命令，或重新启动 VM 并以这种方法触发重新装载。 装载完成后，检查 lsblk 命令的输出，以验证驱动器是否仍已装载。 
- 如果在启用加密之前 /etc/fstab 文件未正确装载该驱动器，则 Azure 磁盘加密无法将其正确装载。
- 在加密过程中，Azure 磁盘加密进程会将装载信息移出 /etc/fstab，并移入其自身的配置文件中。 数据驱动器加密完成后，如果看到 /etc/fstab 中缺少条目，请不要担心。
- 在开始加密之前，请务必停止可能正在向装载的数据磁盘写入数据的所有服务和进程并将其禁用，使其不会在重新引导后自动重启。 这可能会使文件在这些分区上保持打开状态，从而阻止加密过程重新装载这些分区，导致加密失败。 
- 重新启动后，Azure 磁盘加密进程需要花费一段时间来装载新加密的磁盘。 重新启动后，这些磁盘并不是立即可用。 该进程需要一段时间来启动、解锁然后装载加密的驱动器，然后，这些驱动器才可供其他进程访问。 重新启动后，此进程可能需要一分钟以上的时间，具体时间取决于系统特征。

在 [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)（第 244-248 行）和 [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)中可以找到能够用于装载数据磁盘和创建所需 /etc/fstab 条目的命令示例。 

## <a name="networking-requirements"></a>网络要求

若要启用 Azure 磁盘加密功能，Linux VM 必须符合以下网络终结点配置要求：
  - 若要获取令牌以连接到密钥保管库，Linux VM 必须能够连接到 Azure Active Directory 终结点\[login.microsoftonline.com。\]
  - 若要将加密密钥写入密钥保管库，Linux VM 必须能够连接到密钥保管库终结点。
  - Linux VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure VM 到 Internet 的访问，可以解析上述 URI，并配置特定的规则以允许与这些 IP 建立出站连接。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。  

## <a name="encryption-key-storage-requirements"></a>加密密钥存储要求  

Azure 磁盘加密需要 Azure Key Vault 来控制和管理磁盘加密密钥和机密。 密钥保管库和 VM 必须位于同一 Azure 区域和订阅中。

有关详细信息，请参阅[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。

## <a name="terminology"></a>术语
下表定义了 Azure 磁盘加密文档中的一些常用术语：

| 术语 | 定义 |
| --- | --- |
| Azure Key Vault | Key Vault 是基于联邦信息处理标准 (FIPS) 验证的硬件安全模块。 这些标准有助于保护加密密钥和敏感机密。 有关详细信息，请参阅 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文档和[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 经过了优化，可从命令行管理 Azure 资源。|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) 是基于 Linux 的透明磁盘加密子系统，用于在 Linux VM 上启用磁盘加密。 |
| 密钥加密密钥 (KEK) | 可用于保护或包装机密的非对称密钥 (RSA 2048)。 可提供硬件安全模块 (HSM) 保护的密钥或软件保护的密钥。 有关详细信息，请参阅 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文档和[创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。 |
| PowerShell cmdlet | 有关详细信息，请参阅 [Azure PowerShell cmdlet](/powershell/azure/overview)。 |


## <a name="next-steps"></a>后续步骤

- [快速入门 - 使用 Azure CLI 创建和加密 Linux VM](disk-encryption-cli-quickstart.md)
- [快速入门 - 使用 Azure PowerShell 创建和加密 Linux VM](disk-encryption-powershell-quickstart.md)
- [Linux VM 上的 Azure 磁盘加密方案](disk-encryption-linux.md)
- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [创建和配置用于 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)


