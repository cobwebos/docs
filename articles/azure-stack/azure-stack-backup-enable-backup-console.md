---
title: "从管理门户为 Azure 堆栈启用备份 |Microsoft 文档"
description: "启用基础结构备份服务通过管理门户，以便可以还原 Azure 堆栈，如果故障。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 456a0db9771f5963c8d4375d54a22257f6ca1c56
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>从管理门户为 Azure 堆栈启用备份

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

以便 Azure 堆栈可以生成备份，请启用基础结构备份服务通过管理门户。 你可以使用这些备份来还原出现故障时的环境。

> [!Note]  
> 启用通过控制台备份之前，你需要配置备份服务。 你可以配置使用 PowerShell 的备份服务。 有关详细信息，请参阅[使用 PowerShell 的 Azure 堆栈启用备份](azure-stack-backup-enable-backup-powershell.md)。

## <a name="enable-backup"></a>启用备份

1. 打开 Azure 堆栈管理门户在[https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)。
2. 选择**更多的服务** > **基础结构备份**。 选择**配置**中**基础结构备份**边栏选项卡。

    ![Azure 堆栈-备份控制器设置](media\azure-stack-backup\azure-stack-backup-settings.png)。

3. 键入的路径**备份存储位置**。 为一个单独的设备上托管的文件共享的路径使用通用命名约定 (UNC) 字符串。 UNC 字符串指定的资源，如共享的文件或设备的位置。 对于服务，你可以使用 IP 地址。 若要确保灾难发生后的备份数据的可用性，设备应在单独的位置。
    > [!Note]  
    > 如果你的环境支持从 Azure 堆栈的基础结构网络到您的企业环境的名称解析，可以使用 FQDN，而不是 IP。
4. 类型**用户名**使用的域和用户名。 例如，`Contoso\administrator`。
5. 类型**密码**用户。
5. 再次键入密码**确认密码**。
6. 提供在预共享的密钥**加密密钥**框。 使用此密钥加密备份文件。 请确保将此密钥存储在安全的位置。 一旦你首次设置此项，或在将来轮换密钥，你无法查看此接口从此密钥。 若要生成预共享的密钥的详细说明，请按照脚本在[使用 PowerShell 的 Azure 堆栈启用备份](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key)。 
7. 选择**确定**以保存备份的控制器的设置。

若要执行备份，你需要下载 Azure 堆栈工具，并运行 PowerShell cmdlet**开始 AzSBackup**你 Azure 堆栈的管理节点上。 有关详细信息，请参阅[备份 Azure 堆栈](azure-stack-backup-back-up-azure-stack.md )。

## <a name="next-steps"></a>后续步骤

 - 了解如何运行备份。 请参阅[备份 Azure 堆栈](azure-stack-backup-back-up-azure-stack.md )。
- 了解如何验证你的备份运行。 请参阅[在管理门户中完成的确认备份](azure-stack-backup-back-up-azure-stack.md )。
