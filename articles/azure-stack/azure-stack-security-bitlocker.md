---
title: Azure Stack 中的静态加密的数据
description: 了解如何对 Azure Stack 保护静态加密数据
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 12/06/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: f9e76b255647f62b273fef8336ed845e365261cf
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731742"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>在 Azure Stack 中的静态加密的数据

Azure Stack 保护用户和基础结构数据在存储子系统级别使用的静态加密。 Azure Stack 的存储子系统使用 128 位 AES 加密使用 BitLocker 进行加密。 BitLocker 密钥保留在内部的密钥存储区中。

静态数据加密是主要的符合性标准 (例如，PCI DSS，FedRAMP、 HIPAA) 的许多常见的要求。 Azure Stack 使你能够满足这些要求不带任何额外的工作或所需的配置。 有关详细信息，Azure Stack 如何帮助你满足符合性标准，请参阅[Microsoft 服务信任门户](https://aka.ms/AzureStackCompliance)。

> [!NOTE]
> 静态数据加密保护数据免受正在访问的用户组以物理方式窃取了一个或多个硬盘驱动器。 静态数据加密不能防止数据被截获通过网络 （在传输过程中的数据）、 当前正在使用的数据 （数据在内存中） 或多个一般情况下，数据被 exfiltrated 时在系统已启动并运行。

## <a name="retrieving-bitlocker-recovery-keys"></a>检索 BitLocker 恢复密钥

静态数据的 azure Stack BitLocker 密钥在内部管理。 不需要向其提供的常规操作或在系统启动过程。 但是，支持方案可能需要 BitLocker 恢复密钥，以使系统处于联机状态。  

> [!WARNING]
> 检索 BitLocker 恢复密钥并将其存储在 Azure Stack 外部安全的位置。 在某些支持方案期间没有恢复密钥可能导致数据丢失，并且需要从备份映像的系统还原。

检索 BitLocker 恢复密钥要求有权[特权终结点](azure-stack-privileged-endpoint.md)(PEP)。 在 PEP 会话中，运行 Get AzsRecoveryKeys cmdlet。

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

可选参数*Get AzsRecoveryKeys* cmdlet:

| 参数 | 描述 | 类型 | 需要 |
|---------|---------|---------|---------|
|*raw* | 恢复密钥、 计算机名称和密码的每个加密卷的号之间返回原始数据的映射  | 开关 | 无 (Designed 的支持方案)|


## <a name="troubleshoot-issues"></a>排查问题

在极端情况下，BitLocker 解锁请求可能会导致特定的卷不启动失败。 具体取决于一些体系结构的组件的可用性，这种失败可能导致停机时间和数据丢失如果你没有你的 BitLocker 恢复密钥。

> [!WARNING]
> 检索 BitLocker 恢复密钥并将其存储在 Azure Stack 外部安全的位置。 在某些支持方案期间没有恢复密钥可能导致数据丢失，并且需要从备份映像的系统还原。

如果您怀疑您的系统遇到了 BitLocker，如无法启动，Azure Stack 的问题联系支持部门。 支持需要 BitLocker 恢复密钥。 BitLocker 大多数相关的问题，可能会解决该特定的 VM/主机/卷在 FRU 操作。 对于其他情况下，可以执行手动解锁过程，请使用 BitLocker 恢复密钥。 如果 BitLocker 恢复密钥不可用，唯一的选项是从备份映像还原。 具体取决于执行上次备份后，可能会遇到数据丢失。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Stack 安全性](azure-stack-security-foundations.md)
- 有关 BitLocker 如何保护 Csv 的详细信息，请参阅[来保护群集使用 BitLocker 的共享卷和存储区域网络](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker)。