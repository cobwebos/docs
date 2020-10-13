---
title: 使用 Azure 门户排查与使用 GPU Azure Stack Edge Pro 相关的激活错误 |Microsoft Docs
description: 介绍如何对 Azure Stack Edge Pro GPU 激活和与密钥保管库相关的问题进行故障排除。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: alkohli
ms.openlocfilehash: 33254c170c309626ecfa9099bc4d86578148f4c1
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941302"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>排查 Azure Stack Edge Pro GPU 设备上的激活问题 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文介绍如何解决 Azure Stack Edge Pro GPU 设备上的激活问题。 


## <a name="activation-errors"></a>激活错误

下表汇总了与设备激活相关的错误以及相应的建议解决方法。

| 错误消息| 建议的解决方法 |
|------------------------------------------------------|--------------------------------------|
| 如果在使用激活密钥激活设备前删除了用于激活的 Azure Key Vault，则会收到此错误。 <br> ![密钥保管库错误1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | 如果已删除密钥保管库，则可以在保管库处于清除保护期间时恢复密钥保管库。 按照 [恢复密钥保管库](/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步骤操作。 <br>如果清除保护持续时间已过，则无法恢复密钥保管库。 联系 Microsoft 支持部门了解后续步骤。 |
| 如果在激活设备后删除了 Azure Key Vault，然后尝试执行涉及加密的任何操作，例如： **添加用户**、 **添加共享**、 **配置计算**，则会收到此错误。 <br> ![密钥保管库错误2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | 如果已删除密钥保管库，则可以在保管库处于清除保护期间时恢复密钥保管库。 按照恢复密钥保管库中的步骤操作。 <br>如果清除保护持续时间已过，则无法恢复密钥保管库。 联系 Microsoft 支持部门了解后续步骤。 |
| 如果删除了 Azure Key Vault 中的通道完整性密钥，然后尝试执行涉及加密的任何操作，例如： **添加用户**、 **添加共享**、 **配置计算** ，则会收到此错误。 <br> ![密钥保管库错误3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | 如果密钥保管库中的通道完整性密钥被删除，但仍处于清除持续时间内，请按照 [撤消密钥保管库密钥删除](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)中的步骤进行操作。 <br>如果清除保护持续时间已过，并且备份了密钥，则可以从备份还原，否则无法恢复密钥。 联系 Microsoft 支持部门了解后续步骤。 |
| 如果因任何错误而导致激活密钥生成失败，则会收到此错误。 通知中提供了更多详细信息。 <br> ![密钥保管库错误4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | 请等待几分钟，然后重试该操作。 如果问题持续出现，请联系 Microsoft 支持。 |
| 如果用户具有只读权限，则不允许用户生成激活密钥并显示此错误。 <br> ![Key vault 错误5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | 这可能是因为你没有正确的访问权限，或者  *KeyVault* 未注册。<li>请确保在用于 Azure Stack Edge 资源的资源组级别拥有 "所有者" 或 "参与者" 访问权限。</li><li>请确保已注册 KeyVault 资源提供程序。 若要注册资源提供程序，请访问用于 Azure Stack Edge 资源的订阅。 请参阅 **资源提供程序**，搜索 *KeyVault* 并选择并 **注册**。</li> |

## <a name="next-steps"></a>后续步骤

- 详细了解如何 [排查设备问题](azure-stack-edge-gpu-troubleshoot.md)。
