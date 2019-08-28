---
title: Azure SAP HANA（大型实例）的认证 | Microsoft Docs
description: Azure SAP HANA（大型实例）的认证。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c2c919d6e85a2b708dc247b1642e7e8987df81b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099884"
---
# <a name="certification"></a>认证

除了 NetWeaver 认证以外，SAP 还要求通过 SAP HANA 的特殊认证，以便在某些基础结构（如 Azure IaaS）上支持 SAP HANA。

NetWeaver 上的核心 sap 说明以及对 SAP HANA 认证的程度是[sap 说明 #1928533-Azure 上的 sap 应用程序:支持的产品和 Azure VM](https://launchpad.support.sap.com/#/notes/1928533)类型。

在 [经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)站点中可找到 Azure 上 SAP HANA（大型实例）单元的认证记录。 

经 SAP HANA 认证的 IaaS 平台站点所述的“Azure 上的 SAP HANA（大型实例）”类型可让 Microsoft 和 SAP 客户在 Azure 中部署大型 SAP Business Suite、SAP BW、S/4 HANA、BW/4HANA 或其他 SAP HANA 工作负荷。 此解决方案基于通过 SAP HANA 认证的专用硬件模具（[SAP HANA 定制数据中心集成 – TDI](https://scn.sap.com/docs/DOC-63140)）。 以 SAP HANA TDI 配置的解决方案运行可以确信所有基于 SAP HANA 的应用程序（包括 SAP HANA 上的 SAP Business Suite、SAP HANA 上的 SAP BW、S4/HANA 和 BW4/HANA）会在硬件基础结构上正常工作。

与在 VM 中运行 SAP HANA 相比，此解决方案具有一项优势。 它提供的内存量要大得多。 若要启用此解决方案，需要在一些重要的方面有所了解：

- SAP 应用层和非 SAP 应用程序在普通的 Azure 硬件模具中托管的 VM 上运行。
- 客户的本地基础结构、数据中心和应用程序部署通过 ExpressRoute（建议）或虚拟专用网络 (VPN) 连接到云平台。 Active Directory 和 DNS 也扩展到 Azure 中。
- HANA 工作负荷的 SAP HANA 数据库实例在 Azure 上的 SAP HANA（大型实例）中运行。 大型实例模具已连接到 Azure 网络，因此，VM 中运行的软件可与 HANA 大型实例中运行的 HANA 实例进行交互。
- Azure 上的 SAP HANA（大型实例）的硬件是 IaaS 中提供的专用硬件，其上已预装 SUSE Linux Enterprise Server或 Red Hat Enterprise Linux。 在虚拟机上，操作系统的进一步更新和维护由你负责。
- 在 HANA 大型实例的单元上安装 HANA 或者安装运行 SAP HANA 所需的任何其他组件都由你负责。 Azure 上的 SAP HANA 的所有相关日常操作与管理同样由你负责。
- 除了此处所述的解决方案以外，还可以在连接到 Azure 上的 SAP HANA（大型实例）的 Azure 订阅中安装其他组件。 例如，用于实现与 SAP HANA 数据库通信的组件或者直接与其通信的组件（跳接服务器、RDP 服务器、SAP HANA Studio、适用于 SAP BI 方案的 SAP 数据服务，或网络监视解决方案）。
- 与在 Azure 中一样，HANA 大型实例提供支持性的高可用性和灾难恢复功能。

**后续步骤**
- 请参阅[对 HLI 可用的 SKU](hana-available-skus.md) 