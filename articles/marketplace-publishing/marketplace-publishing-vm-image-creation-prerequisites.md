---
title: "为 Azure Marketplace 创建虚拟机映像的技术先决条件 | Microsoft Docs"
description: "了解创建虚拟机映像并将其部署到 Azure Marketplace 以供其他人购买的要求。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: af3e2ad623d8d7bfafe676411f9ae3fbee78aab8
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017

---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>为 Azure Marketplace 创建虚拟机映像的技术先决条件
在开始之前，请仔细阅读过程，并了解执行每个步骤的位置和原因。 在开始产品/服务创建过程前，应尽可能多地准备公司信息和其他数据、下载必需的工具和/或创建技术组件。 通过查看本文，应该清楚了解这些项目。  

## <a name="download-needed-tools--applications"></a>下载所需的工具和应用程序
在开始过程前，应已准备好以下项目：

* 根据所面向的操作系统，从 [Azure 下载](https://azure.microsoft.com/downloads/)页安装 [Azure PowerShell cmdlet](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) 或 [Linux 命令行接口工具](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409)。
* 从 CodePlex 安装 Azure 存储器资源管理器。
* 下载并安装 Azure 认证的认证测试工具：
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913)。 需要基于 Windows 的计算机才能运行认证工具。 如果未提供基于 Windows 的计算机，可以在 Azure 中使用基于 Windows 的 VM 才能运行工具。

## <a name="platforms-supported"></a>支持的平台
可以在 Windows 或 Linux 上开发基于 Azure 的 VM。 发布过程的某些元素（例如创建 Azure 兼容的虚拟硬盘 (VHD)）使用其他工具和步骤，具体取决于所使用的操作系统：  

* 如果使用的是 Linux，请参阅[虚拟机映像发布指南](marketplace-publishing-vm-image-creation.md)的“创建 Azure 兼容的 VHD（基于 Linux）”部分。
* 如果使用的是 Windows,，请参阅[虚拟机映像发布指南](marketplace-publishing-vm-image-creation.md)的“创建 Azure 兼容的 VHD（基于 Windows）”部分。

> [!NOTE]
> 需要访问基于 Windows 的虚拟机以便：
> 
> * 运行证书验证工具。
> * 创建用于 VHD 证书提交的 VHD 共享访问签名 URL。
> 
> 

## <a name="develop-your-vhd"></a>开发 VHD
可以在云中或本地开发 Azure VHD：

* 基于云的开发意味着，所有开发步骤均在驻留在 Azure 上的 VHD 中远程执行。
* 本地开发需要下载 VHD 并使用本地基础结构进行开发。 尽管此操作可行，但不建议这样做。 请注意，针对 Windows 或 SQL 本地开发要求具有相关的本地许可证密钥。 在创建 VM 后，无法包括或安装 SQL Server。 还必须以 Azure 门户中已批准 SQL 映像上的产品/服务为基础。 如果决定在本地开发，必须执行一些不同于在云中开发的步骤。 可以在[创建本地 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)中找到相关信息。

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md

