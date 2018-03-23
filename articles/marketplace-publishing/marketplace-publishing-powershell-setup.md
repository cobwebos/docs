---
title: 设置 PowerShell 以便为 Marketplace 创建 VM | Microsoft Docs
description: 介绍设置 Azure PowerShell 并将其用作可选流程，以便创建要部署到的 VM 映像并在 Azure Marketplace 上进行销售
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: mbaldwin
ms.openlocfilehash: 72ee1ac612fc4c7191718009a78f55272f0a44cf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>设置 Azure PowerShell 以便为 Azure Marketplace 创建产品/服务
有关如何在 Azure 中设置 PowerShell 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 一个简单的方法是使用证书方法，这会下载并导入身份验证所需的证书。 若要获取所需的证书，请使用 **Get-AzurePublishSettingsFile** cmdlet。 在出现提示时，保存文件。 要将证书导入 PowerShell 会话中，请使用 **Import-AzurePublishSettingsFile** cmdlet。

若要为 PowerShell 会话配置和存储常见的 Microsoft Azure 订阅设置，请使用 **Set-AzureSubscription** 和 **Select-AzureSubscription** cmdlet：

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

第一个命令用于将默认的存储帐户与订阅（某些 VM 预配操作所需的）相关联。  第二个命令用于使订阅成为当前项（由其他 cmdlet 识别）。

## <a name="see-also"></a>另请参阅
* [入门：如何将产品/服务发布到 Azure Marketplace](marketplace-publishing-getting-started.md)
* [为 Marketplace 创建虚拟机映像](marketplace-publishing-vm-image-creation.md)

