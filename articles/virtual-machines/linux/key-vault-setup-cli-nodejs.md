---
title: 使用 Azure CLI 1.0 为 Linux VM 设置密钥保管库 | Microsoft 文档
description: 如何使用 Azure CLI 1.0 设置用于 Azure 资源管理器虚拟机的密钥保管库。
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: a9225429e878415334b0c8a66777902395606d63
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>使用 Azure CLI 1.0 在 Azure 资源管理器中为虚拟机设置密钥保管库
在 Azure 资源管理器堆栈中，密码/证书被建模为密钥保管库资源提供程序所提供的资源。 若要了解有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/key-vault-whatis.md) 为了让密钥保管库能与 Azure 资源管理器虚拟机搭配使用，必须将密钥保管库上的 *EnabledForDeployment* 属性设置为 true。 可以在各种客户端中执行此操作。 本文说明如何设置用于 Azure 虚拟机的密钥保管库。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务

- [Azure CLI 1.0](#quick-commands) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于资源管理部署模型的下一代 CLI

## <a name="use-cli-10-to-set-up-key-vault"></a>使用 CLI 1.0 设置密钥保管库
若要使用命令行接口 (CLI) 创建密钥保管库，请参阅[使用 CLI 管理密钥保管库](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault)。

使用 CLI 1.0 时，必须先创建密钥保管库，然后分配部署策略。 然后可以使用以下命令分配策略：

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
