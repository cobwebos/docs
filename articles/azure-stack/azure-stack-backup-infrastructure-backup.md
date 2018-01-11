---
title: "Azure 基础结构备份服务堆栈的备份和数据恢复 |Microsoft 文档"
description: "你可以备份和还原配置和使用基础结构备份服务的服务数据。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 84ce0d72ff826ecb3f5deff165db00a1e50ae89d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Azure 基础结构备份服务堆栈的备份和数据恢复

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以备份和还原配置和使用基础结构备份服务的服务数据。 每个 Azure 堆栈安装包含该服务的实例。 你可以使用由 Azure 堆栈云重新部署的服务创建的备份还原标识、 安全性和 Azure 资源管理器数据。

当你准备好你的云投入生产，你可以启用备份。 不要启用备份，如果您计划执行测试和验证的时间很长时间。

使备份服务之前，请确保你有[就地要求](#verify-requirements-for-the-infrastructure-backup-service)。

> [!Note]  
> 基础结构备份服务不包括用户数据和应用程序。 请参阅以下文章获得说明备份和还原[应用程序服务](https://aka.ms/azure-stack-app-service)， [SQL](https://aka.ms/azure-stack-ms-sql)，和[MySQL](https://aka.ms/azure-stack-mysql)资源提供程序和关联的用户数据...

## <a name="the-infrastructure-backup-service"></a>基础结构备份服务

服务包含以下功能。

| 功能                                            | 说明                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 备份基础结构服务                     | 跨 Azure 堆栈中的基础结构服务的子集协调备份。 如果没有灾难，则可以重新部署的一部分还原数据。 |
| 压缩和加密的导出的备份数据 | 备份数据进行压缩和加密系统之前导出到由管理员提供的外部存储位置。                |
| 监视备份作业                              | 系统通知时备份作业的失败和修正步骤。                                                                                                |
| 备份管理体验                       | 备份 RP 支持启用备份。                                                                                                                         |
| 云恢复                                     | 如果没有灾难性数据丢失，可以使用备份还原作为部署的一部分的核心 Azure 堆栈信息。                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>验证基础结构备份服务的要求

- **存储位置**  
  你需要从 Azure 堆栈中可以包含七个备份可访问文件共享。 每个备份都约为 10 GB。 你的共享应能够存储 140 GB 的备份。 有关 Azure 堆栈基础结构备份服务选择的存储位置的详细信息，请参阅[备份控制器要求](azure-stack-backup-reference.md#backup-controller-requirements)。
- **凭据**  
  你需要的域用户帐户和凭据，例如，你可能使用 Azure 堆栈管理员凭据。
- **加密密钥**  
  使用此密钥加密备份文件。 请确保将此密钥存储在安全的位置。 一旦你首次设置此项，或在将来轮换密钥，你无法查看此接口从此密钥。 若要生成预共享的密钥的详细说明，请按照脚本在[使用 PowerShell 的 Azure 堆栈启用备份](http://azure-stack-backup-enable-backup-powershell.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何[从管理门户的 Azure 堆栈启用备份](azure-stack-backup-enable-backup-console.md)。
- 了解如何[使用 PowerShell 的 Azure 堆栈启用备份](azure-stack-backup-enable-backup-powershell.md)。
- 了解如何[备份 Azure 堆栈](azure-stack-backup-back-up-azure-stack.md )
- 了解如何[从灾难性数据丢失中恢复](azure-stack-backup-recover-data.md)
