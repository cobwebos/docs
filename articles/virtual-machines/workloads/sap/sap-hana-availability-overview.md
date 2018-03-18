---
title: "Azure VM 上的 SAP HANA 可用性 - 概述 | Microsoft Docs"
description: "Azure 本机 VM 上的 SAP HANA 操作"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Azure 虚拟机的 SAP HANA 高可用性指南
Azure 提供了许多功能，可用于在 Azure VM 中部署 SAP HANA 等任务关键型数据库。 本文档提供有关如何实现 Azure 虚拟机中托管的 SAP HANA 实例的可用性的指南。 在此文档中介绍了几个方案，这些方案可以在 Azure 基础结构中实现以提高 Azure 上 SAP HANA 的可用性。 

## <a name="prerequisites"></a>先决条件
本指南假定你熟悉如下 Azure 上的基础结构即服务 (IaaS) 基础知识： 

- 如何通过 Azure 门户或 PowerShell 部署虚拟机或虚拟网络。
- Azure 跨平台命令行接口 (CLI)，包括用来使用 JavaScript 对象表示法 (JSON) 模板的选项。

本指南还假定你了解如何安装 SAP HANA 实例以及如何管理和操作 SAP HANA 实例。 尤其是了解 HANA 系统复制方面的设置和操作，或者了解如何完成备份和还原 SAP HANA 数据库等任务。

让你全面了解 Azure 中的 SAP HANA 主题的其他文章包括：

- [在 Azure VM 上手动安装单实例 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [在 Azure VM 中设置 SAP HANA 系统复制](sap-hana-high-availability.md)
- [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

应对 SAP HANA 感到熟悉的文章和内容列出如下：

- [SAP HANA 的高可用性](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [常见问题解答：SAP HANA 的高可用性](https://archive.sap.com/documents/docs/DOC-66702)
- [如何为 SAP HANA 执行系统复制](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 新增功能：高可用性](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [SAP HANA 系统复制的网络建议](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA 系统复制](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA 服务自动重启](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [配置 SAP HANA 系统复制](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)


除了熟悉如何在 Azure 中部署 VM 外，我们还建议先阅读[在 Azure 中管理 Windows 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)一文，然后再继续在 Azure 中定义可用性体系结构。

## <a name="service-level-agreements-for-different-azure-components"></a>不同 Azure 组件的服务级别协议
Azure 对于不同组件（如网络、存储和 VM）提供不同可用性 SLA。 所有这些 SLA 均在 [Microsoft Azure 服务级别协议](https://azure.microsoft.com/support/legal/sla/)页中记录，并可以在该页开头找到。 如果查阅[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，会发现 Azure 为两个不同的配置提供两个不同的 SLA。 SLA 的定义如下：

- 对于 OS 磁盘使用 [Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)的单个 VM 和所有数据磁盘提供 99.9% 的每月运行时间百分比
- [Azure 可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中组织的多个（至少两个）VM 提供 99.95% 的每月运行时间百分比

针对 Azure 组件可提供的 SLA 度量可用性要求，然后决定需要使用 SAP HANA 实现的不同方案，以达到所需要提供的可用性。

## <a name="next-steps"></a>后续步骤
继续阅读以下文档：

- [一个 Azure 区域内的 SAP HANA 可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [跨 Azure 区域的 SAP HANA 可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


