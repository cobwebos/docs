---
title: "在 Windows 虚拟机上使用 SAP | Microsoft Docs"
description: "了解如何在 Microsoft Azure 中的 Windows 虚拟机 (VM) 上使用 SAP"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: timlt
editor: 
tags: azure-service-management
keywords: 
ms.assetid: 1b455be4-c02f-43e3-8d39-c2d5f216e646
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: campaign-page
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: 1d6326d5f79ea4e975abd1aa90b0d4a635f4c31a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>在 Azure 中的 Windows 虚拟机上使用 SAP
云计算是一个广泛使用的术语，它在 IT 行业（从小公司到大型跨国企业）中受到越来越多的重视。 Microsoft Azure 是 Microsoft 提供的一个云服务平台，它提供了各种新的可能性。 现在客户能够快速将应用程序预配为云服务并可取消预配，因此他们不会受到技术或预算方面的限制。 公司不用在硬件基础结构中投入时间和预算，而是可以重点关注应用程序、业务流程以及它为客户和用户带来的好处。

借助 Microsoft Azure 虚拟机，Microsoft 提供全面的基础结构即服务 (IaaS) 平台。 Azure 虚拟机 (IaaS) 支持基于 SAP NetWeaver 的应用程序。 以下白皮书介绍如何在 Azure 中的 Windows 虚拟机上规划和实现基于 SAP NetWeaver 的应用程序。 也可以在 [Linux 虚拟机](../../linux/classic/sap-get-started.md)上实现基于 SAP NetWeaver 的应用程序。

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>Azure 上的 SAP NetWeaver - HA
标题：Azure 上的 SAP NetWeaver - 将 Azure 上的 Windows Server 故障转移群集与 SIOS DataKeeper 配合使用来群集 SAP ASCS/SCS 实例

摘要：本文档介绍如何使用 SIOS DataKeeper 在 Azure 上设置高度可用的 SAP ASCS/SCS 配置。 SAP 使用需要共享磁盘的 Windows Server 故障转移群集配置保护其单点故障组件，如 SAP ASCS/SCS 或入队复制服务。 这些 SAP 组件对于 SAP 系统的功能是非常必要的。 因此，高可用性功能需要落实到位，以确保这些组件可以承受服务器或 VM 失败，就像祼机和 Hyper-V 环境的 Windows 群集配置所做的那样。 截至 2015 年 8 月，Azure 自身不能提供这些关键 SAP 组件所需的基于 Windows 的高度可用配置所需的共享磁盘。 但是借助于 SIOS 开发的 DataKeeper 产品的帮助，可以在 Azure IaaS 平台上构建 SAP ASCS/SCS 所需的 Windows Server 故障转移群集配置。 此文以分步方式介绍如何在 Azure 中使用 SIOS Datakeeper 提供的共享磁盘安装 Windows Server 故障转移群集配置。 此文介绍 Azure 上 Windows 和 SAP 端配置的详细信息，这些配置以最佳方式使高可用性配置正常工作。 此文是对 SAP 安装文档和 SAP 说明的补充，这些文档代表在给定平台上安装和部署 SAP 软件的主要资源。

更新时间：2015 年 8 月

[立即下载此指南](http://go.microsoft.com/fwlink/?LinkId=613056)

