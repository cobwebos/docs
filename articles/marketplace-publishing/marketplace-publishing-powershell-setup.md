---
title: 设置 PowerShell 以便为市场创建 VM | Microsoft Docs
description: 介绍设置 Azure PowerShell 并将其用作可选流程，以便创建要部署到的 VM 映像并在 Azure 市场上进行销售
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: e5175558f18dfc903c280ea6bbe487e0a3ee8189
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076597"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>设置 Azure PowerShell 以便为 Azure 市场创建产品/服务

有关如何在 Azure 中设置 PowerShell 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 一个简单的方法是使用证书方法，这会下载并导入身份验证所需的证书。 若要获取所需的证书，请使用 **Get-AzurePublishSettingsFile** cmdlet。 在出现提示时，保存文件。 要将证书导入 PowerShell 会话中，请使用 **Import-AzurePublishSettingsFile** cmdlet。

若要为 PowerShell 会话配置和存储常见的 Microsoft Azure 订阅设置，请使用 **Set-AzureSubscription** 和 **Select-AzureSubscription** cmdlet：

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

第一个命令用于将默认的存储帐户与订阅（某些 VM 预配操作所需的）相关联。  第二个命令用于使订阅成为当前项（由其他 cmdlet 识别）。

有关详细信息，请参阅以下文章：
* [入门：如何将产品/服务发布到 Azure 市场](marketplace-publishing-getting-started.md)
* [为市场创建虚拟机映像](marketplace-publishing-vm-image-creation.md)

