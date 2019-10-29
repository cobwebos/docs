---
title: 启用适用于 Linux Vm 的 Azure 磁盘加密
description: 本文提供了有关为 Linux Vm 启用 Microsoft Azure 磁盘加密的说明。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a3d48d53c2d4d0c859b58a94b12ffa94590b18a5
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989637"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>适用于 Linux Vm 的 Azure 磁盘加密 

Azure 磁盘加密有助于保护和保护数据，以满足组织的安全性和符合性承诺。 它使用 Linux 的[DM dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能为 Azure 虚拟机（vm）的 OS 和数据磁盘提供卷加密，并与[Azure Key Vault](../../key-vault/index.yml)集成，以帮助你控制和管理磁盘加密密钥和机密。 

如果你使用的是[Azure 安全中心](../../security-center/index.yml)，则如果你有未加密的 vm，系统会发出警报。 这些警报显示为“高严重性”，建议加密这些 VM。

![Azure 安全中心磁盘加密警报](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果以前通过 Azure AD 使用 Azure 磁盘加密来加密 VM，则必须继续使用此选项来加密 VM。 有关详细信息，请参阅[Azure 磁盘加密与 Azure AD （以前的版本）](disk-encryption-overview-aad.md) 。 
> - 某些建议可能会导致数据、网络或计算资源使用量增加，从而产生额外许可或订阅成本。 必须具有有效的活动 Azure 订阅，才能在 Azure 的受支持区域中创建资源。

只需几分钟即可了解适用于 Linux 的 Azure 磁盘加密基础知识，只需使用[Azure CLI 快速入门创建和加密 LINUX vm](disk-encryption-cli-quickstart.md)或[使用 Azure Powershell 快速入门创建和加密 linux vm](disk-encryption-powershell-quickstart.md)即可。

## <a name="supported-vms-and-operating-systems"></a>支持的 Vm 和操作系统

### <a name="supported-vm-sizes"></a>支持的 VM 大小

Linux Vm 在[大小范围](sizes.md)内可用。 Azure 磁盘加密不适用于[基本、A 系列 vm](https://azure.microsoft.com/pricing/details/virtual-machines/series/)或不满足以下最低内存要求的虚拟机：

| 虚拟机 | 最小内存要求 |
|--|--|
| 仅加密数据卷时的 Linux Vm| 2GB |
| 在加密数据和 OS 卷时，以及根（/）文件系统使用情况是4GB 或更低的情况下的 Linux Vm | 8 GB |
| 在对数据和 OS 卷进行加密以及根（/）文件系统使用大于4GB 的情况下使用 Linux Vm | 根文件系统用法 * 2。 例如，16 GB 根文件系统使用至少需要32GB 的 RAM |

在 Linux 虚拟机上完成 OS 磁盘加密过程后，可以将 VM 配置为在内存更少的情况下运行。 

使用高级存储的 Vm 也可以使用 Azure 磁盘加密。 

### <a name="supported-operating-systems"></a>支持的操作系统

Azure[认可的 linux 分发](endorsed-distros.md)的一个子集上支持 Azure 磁盘加密，这本身就是所有 Linux 服务器可能的分发的子集。

![支持 Azure 磁盘加密的 Linux 服务器分发示意图](./media/disk-encryption/ade-supported-distros.png)

不是由 Azure 认可的 Linux 服务器分发不支持 Azure 磁盘加密;其中，仅以下分发和版本支持 Azure 磁盘加密：

| Linux 分发版 | 版本 | 支持加密的卷类型|
| --- | --- |--- |
| Ubuntu | 18.04| OS 和数据磁盘 |
| Ubuntu | 16.04| OS 和数据磁盘 |
| Ubuntu | 14.04.5</br>[其 Azure 优化内核更新到 4.15 或更高版本](disk-encryption-troubleshooting.md) | OS 和数据磁盘 |
| RHEL | 7.7 | 操作系统和数据磁盘（请参阅下面的注释） |
| RHEL | 7.6 | 操作系统和数据磁盘（请参阅下面的注释） |
| RHEL | 7.5 | 操作系统和数据磁盘（请参阅下面的注释） |
| RHEL | 7.4 | 操作系统和数据磁盘（请参阅下面的注释） |
| RHEL | 7.3 | 操作系统和数据磁盘（请参阅下面的注释） |
| RHEL | 7.2 | 操作系统和数据磁盘（请参阅下面的注释） |
| RHEL | 6.8 | 数据磁盘（请参阅下面的注释） |
| RHEL | 6.7 | 数据磁盘（请参阅下面的注释） |
| CentOS | 7.7 | OS 和数据磁盘 |
| CentOS | 7.6 | OS 和数据磁盘 |
| CentOS | 7.5 | OS 和数据磁盘 |
| CentOS | 7.4 | OS 和数据磁盘 |
| CentOS | 7.3 | OS 和数据磁盘 |
| CentOS | 7.2n | OS 和数据磁盘 |
| CentOS | 6.8 | 数据磁盘 |
| openSUSE | 42.3 | 数据磁盘 |
| SLES | 12-SP4 | 数据磁盘 |
| SLES | 12-SP3 | 数据磁盘 |

> [!NOTE]
> RHEL OS 和用于 RHEL7 即用即付映像的数据磁盘支持新的 ADE 实现。 ADE 目前不支持 RHEL 自带订阅 (BYOS) 映像。 

## <a name="additional-vm-requirements"></a>其他 VM 要求

Azure 磁盘加密要求在系统上存在 dm-dm-crypt 和 vfat 模块。 从默认映像删除或禁用 vfat 将会阻止系统读取密钥卷，并在后续重新启动时获取解锁磁盘所需的密钥。 从系统中删除 vfat 模块的系统强化步骤与 Azure 磁盘加密不兼容。 

在启用加密之前，要加密的数据磁盘必须正确地列在/etc/fstab 中。 为此条目使用永久性块设备名，因为每次重新启动后，不能依赖于使用“/dev/sdX”格式的设备名来与同一磁盘相关联，尤其是应用加密后。 有关此行为的详细信息，请参阅：[排查 Linux VM 设备名更改问题](troubleshoot-device-names-problems.md)

确保正确配置用于装载的 /etc/fstab 设置。 若要配置这些设置，请运行 mount -a 命令，或重新启动 VM 并以这种方法触发重新装载。 装载完成后，检查 lsblk 命令的输出，以验证驱动器是否仍已装载。 
- 如果在启用加密之前 /etc/fstab 文件未正确装载该驱动器，则 Azure 磁盘加密无法将其正确装载。
- 在加密过程中，Azure 磁盘加密进程会将装载信息移出 /etc/fstab，并移入其自身的配置文件中。 数据驱动器加密完成后，如果看到 /etc/fstab 中缺少条目，请不要担心。
- 在开始加密之前，请务必停止可以写入装入的数据磁盘并将其禁用的所有服务和进程，使其不会在重新启动后自动重新启动。 这可能会使文件在这些分区上保持打开状态，从而防止加密过程重新装入它们，从而导致加密失败。 
- 重新启动后，Azure 磁盘加密进程需要花费一段时间来装载新加密的磁盘。 重新启动后，这些磁盘并不是立即可用。 该进程需要一段时间来启动、解锁然后装载加密的驱动器，然后，这些驱动器才可供其他进程访问。 重新启动后，此进程可能需要一分钟以上的时间，具体时间取决于系统特征。

可用于装载数据磁盘并创建必要的/etc/fstab 条目的命令示例可以在[Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)（244-248 行）和[azure 磁盘加密先决条件 PowerShell 中找到脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)。 

## <a name="networking-requirements"></a>网络要求

若要启用 Azure 磁盘加密功能，Linux Vm 必须满足以下网络终结点配置要求：
  - 若要获取令牌以连接到密钥保管库，Linux VM 必须能够连接到 Azure Active Directory 终结点，\[login.microsoftonline.com\]。
  - 若要将加密密钥写入密钥保管库，Linux VM 必须能够连接到密钥保管库终结点。
  - Linux VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure VM 到 Internet 的访问，可以解析上述 URI，并配置特定的规则以允许与这些 IP 建立出站连接。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。  

## <a name="encryption-key-storage-requirements"></a>加密密钥存储要求  

Azure 磁盘加密需要 Azure Key Vault 来控制和管理磁盘加密密钥和机密。 Key vault 和 Vm 必须位于同一个 Azure 区域和订阅中。

有关详细信息，请参阅[创建和配置 Azure 磁盘加密的密钥保管库](disk-encryption-key-vault.md)。

## <a name="terminology"></a>术语
下表定义了 Azure 磁盘加密文档中使用的一些常用术语：

| 术语 | 定义 |
| --- | --- |
| Azure 密钥保管库 | Key Vault 是基于联邦信息处理标准 (FIPS) 验证的硬件安全模块。 这些标准有助于保护加密密钥和敏感机密。 有关详细信息，请参阅[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)文档和[为 Azure 磁盘加密创建和配置密钥保管库](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 经过了优化，可从命令行管理 Azure 资源。|
| DM-Crypt |[DM dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)是基于 linux 的透明磁盘加密子系统，用于在 linux vm 上启用磁盘加密。 |
| 密钥加密密钥（KEK） | 可用于保护或包装机密的非对称密钥（RSA 2048）。 可提供硬件安全模块 (HSM) 保护的密钥或软件保护的密钥。 有关详细信息，请参阅[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)文档和[为 Azure 磁盘加密创建和配置密钥保管库](disk-encryption-key-vault.md)。 |
| PowerShell cmdlet | 有关详细信息，请参阅 [Azure PowerShell cmdlet](/powershell/azure/overview)。 |


## <a name="next-steps"></a>后续步骤

- [快速入门-使用 Azure CLI 创建并加密 Linux VM](disk-encryption-cli-quickstart.md)
- [快速入门-使用 Azure Powershell 创建和加密 Linux VM](disk-encryption-powershell-quickstart.md)
- [Linux Vm 上的 Azure 磁盘加密方案](disk-encryption-linux.md)
- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [为 Azure 磁盘加密创建和配置密钥保管库](disk-encryption-key-vault.md)


