---
title: "设置 PowerShell 以便为应用商店创建 VM | Microsoft Docs"
description: "介绍设置 Azure PowerShell 并将其用作可选流程，以便创建要部署到的 VM 映像并在 Azure 应用商店上进行销售"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7ea4b87e723c2ce194186af39ba8e6f0f593347a
ms.lasthandoff: 04/27/2017


---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>设置 Azure PowerShell 以便为 Azure 应用商店创建产品/服务
有关如何在 Azure 中设置 PowerShell 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 一个简单的方法是使用证书方法，这将下载并导入身份验证所需的证书。 若要获取所需的证书，请使用 **Get-AzurePublishSettingsFile** cmdlet。 在出现提示时，保存文件。 若要将证书导入 PowerShell 会话中，请使用 **Import-AzurePublishSettingsFile** cmdlet。

若要为 PowerShell 会话配置和存储常见的 Microsoft Azure 订阅设置，请使用 **Set-AzureSubscription** 和 **Select-AzureSubscription** cmdlet：

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

第一个命令用于将默认的存储帐户与订阅（某些 VM 预配操作所需的）相关联。  第二个命令用于使订阅成为当前项（由其他 cmdlet 识别）。

## <a name="see-also"></a>另请参阅
* [入门：如何将产品/服务发布到 Azure 应用商店](marketplace-publishing-getting-started.md)
* [为应用商店创建虚拟机映像](marketplace-publishing-vm-image-creation.md)


