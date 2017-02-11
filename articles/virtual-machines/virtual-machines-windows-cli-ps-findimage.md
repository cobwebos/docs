---
title: "导航和选择 Windows VM 映像 | Microsoft Docs"
description: "了解在使用 Resource Manager 部署模型创建 Windows 虚拟机时如何确定映像的发布者、产品和 SKU。"
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 78f3769cd38bbcc6dbe8ac6241d6d5c3a65ccd00


---
# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell-or-the-cli"></a>使用 PowerShell 或 CLI 在 Azure 中导航和选择 Windows 虚拟机映像
本主题介绍如何查找每个可能部署到目标位置的 VM 映像发布者、产品、SKU 和版本。 举例来说，某些常用 Windows VM 映像包括：

## <a name="table-of-commonly-used-windows-images"></a>常用 Windows 映像表
| PublisherName | 产品 | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]




<!--HONumber=Nov16_HO3-->


