---
title: Windows Server 的 azure Stack 相关的常见问题解答 |Microsoft Docs
description: 适用于 Windows Server 的 Azure Stack Marketplace 常见问题列表
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: sethm
ms.reviewer: avishwan
ms.openlocfilehash: 91404f01a1a675ac59898336ef8aa81e1d2638b6
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51579227"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Azure Stack Marketplace 常见问题解答中的 Windows Server

本文回答了一些有关 Windows Server 映像中的常见问题[Azure Stack Marketplace](azure-stack-marketplace.md)。

## <a name="marketplace-items"></a>市场项

### <a name="how-do-i-update-to-a-newer-windows-image"></a>如何更新到较新的 Windows 映像？

首先，确定如果任何 Azure 资源管理器模板，请参阅特定版本。 如果是这样，更新这些模板，或保留较旧的映像版本。 最好是使用**版本： 最新**。

接下来，如果任何虚拟机规模集引用的特定版本，您应考虑这些是否会更高版本，扩展并决定是否要保留较旧版本。 如果上述条件均不适用，请下载较新的之前删除旧映像 Marketplace 中。 使用 Marketplace 管理当下载原始的方式进行此操作。 然后下载较新版本。

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>适用于 Windows Server Marketplace 映像在 Azure Stack 上的许可选项有哪些？

Microsoft 提供了两个版本的 Windows Server 映像，通过 Azure Stack Marketplace:

- **使用即付即用**： 这些映像运行的全价 Windows 计量。 
   应使用的用户： 使用的 EA 客户*消耗计费模型*;不想使用 SPLA 许可的 Csp。
- **自带许可 (BYOL)**： 这些映像运行基本指标。
   应使用的用户： EA 客户，使用 Windows Server 许可证;使用 SPLA 许可的 Csp。

Azure Stack 不支持 azure 混合使用权益 (AHUB)。 通过"容量"模型许可的客户必须使用 BYOL 映像。 如果要测试与 Azure Stack 开发工具包 (ASDK)，可以使用上述任何选项。

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>如果我下载了错误的版本提供我的租户/用户呢？

通过 Marketplace 管理首先删除不正确的版本。 等待它完成完全 （查看完成时，不 Marketplace 管理边栏选项卡的通知）。 然后下载正确版本。

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>如果我的用户错误地检查以前的 Windows 中的"我拥有的许可证"框生成，并且它们没有许可证？

请参阅[返回到即用即付的权益将 Windows Server Vm](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1)。

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-ea-entitlement"></a>如果我有较旧的映像和我的用户忘记检查"我拥有的许可证"框中，或我们使用我们自己的映像和我们确实有 EA 权利？

请参阅[转换现有 VM 使用 Azure 混合权益 for Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server)。 请注意，Azure 混合权益不适用于 Azure Stack 中，但不适用于此设置的效果。

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>使用 Windows Server，如 SQL 或机器学习服务器的其他 Vm 呢？

这些映像是否适用**licenseType**参数，因此它们是付费使用。 此参数可以设置 （请参见上一个常见问题解答回答）。 这仅适用于 Windows Server 软件，不向需要自带许可证的分层产品 （如 SQL)。 即付使用授权不适用于分层的软件产品。

### <a name="i-have-an-ea-and-i-create-my-own-images-how-do-i-make-sure-they-are-billed-correctly"></a>我具有 ea 的情况并创建我自己的图像;如何确保正确计费？

您可以添加**licenseType: Windows_Server** Azure 资源管理器模板中。 必须将此设置添加到每个虚拟机资源块。

## <a name="activation"></a>激活

若要激活 Azure Stack 上的 Windows Server 虚拟机，必须满足以下条件：

- OEM 已在 Azure Stack 中每个主机系统上设置适当的 BIOS 标记。
- 必须使用 Windows Server 2012 R2 和 Windows Server 2016[虚拟机自动激活](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))。 在 Azure Stack 上不支持密钥管理服务 (KMS) 和其他激活服务。

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>如何验证我的虚拟机是否已激活？

从提升的命令提示符运行以下命令： 

```shell
slmgr /dlv
``` 

如果正确激活它，就会看到以下明确地指示主机名显示在`slmgr`输出。 不依赖于在显示屏上的水印它们可能不是最新状态，或从你背后的不同虚拟机显示。

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>我的 VM 不设置为使用 AVMA，如何解决它？

从提升的命令提示符运行以下命令： 

```shell
slmgr /ipk <AVMA key> 
```

请参阅文章[虚拟机自动激活](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))这些密钥用于你的映像。

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>我创建我自己的 Windows Server 映像，我可以如何请确保它们使用 AVMA？

建议您执行`slmgr /ipk`命令行中使用相应的密钥在运行之前`sysprep`命令。 或者，在任何 Unattend.exe 安装文件中包括 AVMA 密钥。

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>我正尝试使用我在 Azure 上创建的 Windows Server 2016 映像，并且不激活或使用 KMS 激活。

运行 `slmgr /ipk` 命令。 Azure 映像可能不正确地故障回复到 AVMA，但是，如果它们可以访问 Azure KMS 系统，它们将激活。 建议您确保这些 Vm 已设置为使用 AVMA。

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>我已执行所有这些步骤，但仍没有激活我的虚拟机。

请联系硬件供应商联系以验证已安装正确的 BIOS 标记。

### <a name="what-about-earlier-versions-of-windows-server"></a>Windows Server 的早期版本呢？

[虚拟机自动激活](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))早期版本的 Windows Server 中不支持。 你将需要手动激活 Vm。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [Azure Stack Marketplace 概述](azure-stack-marketplace.md)
- [从 Azure marketplace 项下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)
