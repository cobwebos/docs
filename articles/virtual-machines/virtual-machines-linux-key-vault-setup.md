---
title: "在 Azure Resource Manager 中为虚拟机设置密钥保管库 | Microsoft Docs"
description: "如何设置与 Azure Resource Manager 虚拟机搭配使用的密钥保管库。"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: bc60d5659c80ad1245d7a34b4cd81c0ae73a5ba9


---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>在 Azure Resource Manager 中为虚拟机设置密钥保管库

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

在 Azure Resource Manager 堆栈中，密码/证书被建模为密钥保管库资源提供程序所提供的资源。 若要了解有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/key-vault-whatis.md)

为了让密钥保管库能与 Azure Resource Manager 虚拟机搭配使用，必须将密钥保管库上的 *EnabledForDeployment* 属性设置为 true。 可以在各种客户端中执行此操作。

## <a name="use-cli-to-set-up-key-vault"></a>使用 CLI 设置密钥保管库
若要使用命令行接口 (CLI) 创建密钥保管库，请参阅[使用 CLI 管理密钥保管库](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)。

使用 CLI 时，必须先创建密钥保管库，然后分配部署策略。 可以使用以下命令来执行此操作：

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>使用模板设置密钥保管库
使用模板时，必须将密钥保管库资源的 `enabledForDeployment` 属性设置为 `true`。

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

有关使用模板创建密钥保管库时可以配置的其他选项，请参阅[创建密钥保管库](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)。




<!--HONumber=Nov16_HO3-->


