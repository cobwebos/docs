---
title: Azure VM 上的 SAP HANA 可用性 - 概述 | Microsoft Docs
description: 介绍了 Azure 原生 VM 中的 SAP HANA 操作。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7049a4b5159687ab928cda7ddc6b1a35959529ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Azure 虚拟机的 SAP HANA 高可用性

可以使用许多 Azure 功能在 Azure VM 上部署 SAP HANA 等任务关键型数据库。 本文提供了有关如何实现 Azure VM 中承载的 SAP HANA 实例的可用性的指南。 本文介绍了几个方案，可以使用 Azure 基础结构实现这些方案来提高 Azure 中 SAP HANA 的可用性。 

## <a name="prerequisites"></a>先决条件

本文假定你熟悉 Azure 上的基础结构即服务 (IaaS) 基础知识，包括： 

- 如何通过 Azure 门户或 PowerShell 部署虚拟机或虚拟网络。
- 使用 Azure 跨平台命令行接口 (Azure CLI)，包括用来使用 JavaScript 对象表示法 (JSON) 模板的选项。

本文还假定你熟悉如何安装 SAP HANA 实例以及如何管理和操作 SAP HANA 实例。 尤其重要的是，务必要熟悉 HANA 系统复制的设置和操作。 这包括诸如备份和还原 SAP HANA 数据库之类的任务。

以下文章很好地概述了如何在 Azure 中使用 SAP HANA：

- [在 Azure VM 上手动安装单实例 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [在 Azure VM 中设置 SAP HANA 系统复制](sap-hana-high-availability.md)
- [备份 Azure VM 上的 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

熟悉关于 SAP HANA 的以下文章也是一个不错的主意：

- [SAP HANA 的高可用性](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [常见问题解答：SAP HANA 的高可用性](https://archive.sap.com/documents/docs/DOC-66702)
- [为 SAP HANA 执行系统复制](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 新增功能：高可用性](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [针对 SAP HANA 系统复制的网络建议](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA 系统复制](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA 服务自动重启](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [配置 SAP HANA 系统复制](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

除了熟悉如何在 Azure 中部署 VM 外，在 Azure 中定义可用性体系结构之外，我们还建议你先阅读[在 Azure 中管理 Windows 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)。

## <a name="service-level-agreements-for-azure-components"></a>Azure 组件的服务级别协议

Azure 针对不同组件（如网络、存储和 VM）提供了不同的可用性 SLA。 所有 SLA 都有文档记录。 有关详细信息，请参阅 [Microsoft Azure 服务级别协议](https://azure.microsoft.com/support/legal/sla/)。 

[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) 介绍了针对以下两种不同配置的两个不同 SLA：

- 为 OS 磁盘和所有数据磁盘使用 [Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)的单个 VM。 此选项提供每月 99.9% 的正常运行时间。
- 组织到 [Azure 可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中的多个（至少两个）VM。 此选项提供每月 99.95% 的正常运行时间。

对照 Azure 组件可以提供的 SLA 度量你的可用性要求。 然后，选择你的 SAP HANA 方案来实现所需级别的可用性。

## <a name="next-steps"></a>后续步骤

- 了解[一个 Azure 区域内的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)。
- 了解[跨 Azure 区域的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)。 















  


