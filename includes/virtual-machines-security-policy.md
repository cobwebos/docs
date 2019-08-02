---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f289da08c63f655006801d89f82640c6e3c6b7be
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68622046"
---
为了所运行的应用程序，请务必确保虚拟机 (VM) 安全。 保护 VM 可以添加一个或多个 Azure 服务和功能，这些服务和功能涵盖保护对 VM 的访问和保护数据存储。 按照本文提供的信息操作，可确保 VM 和应用程序安全。

## <a name="antimalware"></a>Antimalware

现代云环境的威胁局势非常多变，增加了为了满足合规性和安全要求，维护有效保护机制方面的压力。 [适用于 Azure 的 Microsoft 反恶意软件](../articles/security/fundamentals/antimalware.md)是一种免费实时保护功能，可帮助识别并删除病毒、间谍软件和其他恶意软件。 可配置警报，以便在已知恶意或不需要的软件试图安装自身或在 VM 上运行时通知你。

## <a name="azure-security-center"></a>Azure 安全中心

[Azure 安全中心](../articles/security-center/security-center-intro.md)可帮助防范、检测和应对针对 VM 的威胁。 安全中心提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

安全中心的实时访问可以跨 VM 部署应用，用以锁定发往 Azure VM 的入站流量，降低遭受攻击的可能性，同时还可根据需要轻松连接到 VM。 当启用实时访问且用户请求访问 VM 时，安全中心将检查用户对该 VM 的权限。 如果用户具有正确权限，则会批准请求并且安全中心会自动将网络安全组 (NSG) 配置为：在限定时间内允许发往选定端口的入站流量。 在该时间到期后，安全中心会将 NSG 还原为以前的状态。 

## <a name="encryption"></a>加密

为了增强 [Windows VM](../articles/virtual-machines/windows/encrypt-disks.md) 和 [Linux VM](../articles/virtual-machines/linux/encrypt-disks.md) 的安全性，可以加密 Azure 中的虚拟磁盘。 Windows VM 上的虚拟磁盘使用 BitLocker 进行静态加密。 Linux VM 上的虚拟磁盘是使用 dm-crypt 静态加密的。 

加密 Azure 中的虚拟磁盘不会产生费用。 使用软件保护将加密密钥存储在 Azure 密钥保管库中，或者，可在已通过 FIPS 140-2 级别 2 标准认证的硬件安全模块 (HSM) 中导入或生成密钥。 这些加密密钥用于加密和解密附加到 VM 的虚拟磁盘。 可以控制这些加密密钥，以及审核对它们的使用。 打开和关闭 VM 时，Azure Active Directory 服务主体将提供一个安全机制用于颁发这些加密密钥。

## <a name="key-vault-and-ssh-keys"></a>Key Vault 和 SSH 密钥

机密和证书可以建模为资源并由 [Key Vault](../articles/key-vault/key-vault-whatis.md) 提供。 可以使用 Azure PowerShell 为 [Windows VM](../articles/virtual-machines/windows/key-vault-setup.md) 创建密钥保管库，为 [Linux VM](../articles/virtual-machines/linux/key-vault-setup.md) 创建 Azure CLI。 还可以创建用于加密的密钥。

密钥保管库访问策略单独授予对密钥、机密和证书的权限。 例如，可以向用户提供仅针对密钥的访问权限，但不提供针对机密的权限。 但是，访问密钥、机密或证书的权限是保管库级别的。 换而言之，[密钥保管库访问策略](../articles/key-vault/key-vault-secure-your-key-vault.md)不支持对象级别的权限。

连接到 VM 时，应使用公钥加密，提供更安全的方式登录到 VM。 此过程涉及使用安全外壳 (SSH) 命令进行公钥和私钥交换，对自己（而不是用户名和密码）进行身份验证。 密码易受到强力破解攻击，特别是在面向 Internet 的 VM（如 Web 服务器）上。 使用安全外壳 (SSH) 密钥对，可以创建使用 SSH 密钥进行身份验证的 [Linux VM](../articles/virtual-machines/linux/mac-create-ssh-keys.md)，从而无需密码即可登录。 还可以使用 SSH 密钥从 [Windows VM](../articles/virtual-machines/linux/ssh-from-windows.md) 连接到 Linux VM。

## <a name="managed-identities-for-azure-resources"></a>Azure 资源的托管标识

生成云应用程序时需要应对的常见挑战是，如何管理代码中用于云服务身份验证的凭据。 保护这些凭据是一项重要任务。 理想情况下，这些凭据永远不会出现在开发者工作站上，也不会被签入源代码管理系统中。 虽然 Azure Key Vault 可用于安全存储凭据、机密以及其他密钥，但代码需要通过 Key Vault 的身份验证才能检索它们。 

Azure Active Directory (Azure AD) 中的 Azure 资源托管标识功能可以解决此问题。 此功能为 Azure 服务提供了 Azure AD 中的自动托管标识。 可以使用此标识向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。  在 VM 上运行的代码可以从只能从 VM 中访问的两个终结点请求令牌。 有关此服务的更多详细信息，请查看 [Azure 资源的托管标识](../articles/active-directory/managed-identities-azure-resources/overview.md)概述页。   

## <a name="policies"></a>策略

可以使用 [Azure 策略](../articles/azure-policy/azure-policy-introduction.md)为组织的 [Windows VM](../articles/virtual-machines/windows/policy.md) 和 [Linux VM](../articles/virtual-machines/linux/policy.md) 定义所需行为。 通过使用策略，组织可以在整个企业中强制实施各种约定和规则。 强制实施所需行为有助于消除风险，同时为组织的成功做出贡献。

## <a name="role-based-access-control"></a>基于角色的访问控制

使用[基于角色的访问控制 (RBAC)](../articles/role-based-access-control/overview.md)，可以在团队中对职责进行分配，仅向 VM 上的用户授予执行作业所需的访问权限。 可以仅允许某些操作，而不是向 VM 上的每个人授予不受限制的权限。 可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/role) 或 [Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md) 为 [Azure 门户](../articles/role-based-access-control/role-assignments-portal.md)中的 VM 配置访问控制。


## <a name="next-steps"></a>后续步骤
- 完成相关步骤，使用适用于 [Linux](../articles/security/fundamentals/overview.md) 或 [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md) 的 Azure 安全中心监视虚拟机的安全性。