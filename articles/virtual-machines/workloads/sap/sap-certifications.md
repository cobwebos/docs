---
title: 适用于 SAP 的 Microsoft Azure 认证 | Microsoft Docs
description: Azure 平台上 SAP 的当前配置和认证的更新列表。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: f78d5f839c5dd7816ae28eeed175503427abb43c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87035836"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>在 Microsoft Azure 上运行的 SAP 认证和配置

SAP 和 Microsoft 具有悠久的合作历史，建立了强大的合作伙伴关系，使其客户可以互惠互利。 Microsoft 会不断更新其平台并向 SAP 提交新认证详细信息，确保 Microsoft Azure 是运行 SAP 工作负荷的最佳平台。 下表概述了 Azure 支持的配置以及不断增加的 SAP 认证的列表。 此列表是一种概述列表，在此列表中可能会有所建议，也可能来自官方 SAP 列表。 有关如何获取详细数据的信息，请参见[Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)一文

## <a name="sap-hana-certifications"></a>SAP HANA 认证
引用：

- [SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)，提供对本机 Azure VM 和 HANA 大型实例的 SAP HANA 支持。

| SAP 产品 | 支持的 OS | Azure 产品/服务 |
| --- | --- | --- |
| SAP HANA Developer Edition（包括由 SQLODBC、ODBO（仅限 Windows）、ODBC 和 JDBC 驱动程序组成的 HANA Client 软件）、HANA Studio 和 HANA 数据库 | Red Hat Enterprise Linux、SUSE Linux Enterprise | D 系列 VM 系列 |
| HANA 上的 Business One | SUSE Linux Enterprise | DS14_v2、M32ts、M32ls、M64ls、M64s <br /> [经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | 可控制的 GS5 可用性。 完全支持 M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2、 <br /> Azure 上的 SAP HANA（大型实例）[SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA、OLTP | Red Hat Enterprise Linux、SUSE Linux Enterprise | M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2 <br /> Azure 上的 M416s_v2、M416ms_v2、SAP HANA (大型实例) [SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| 适用于 BW 和 OLAP 的 HANA 企业版 | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2 <br /> Azure 上的 M416s_v2、M416ms_v2、SAP HANA (大型实例) [SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2 <br /> Azure 上的 M416s_v2、M416ms_v2 SAP HANA (大型实例)  <br /> [经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

请注意，SAP 在[经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中使用术语“群集”作为“横向扩展”的同义词，而不是高可用性“群集”的同义词

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 认证
在 Microsoft 和 SAP 的全面支持下，Microsoft Azure 已针对以下 SAP 产品进行认证。
引用：

- [1928533 - Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533)，适用于所有基于 SAP NetWeaver 的应用程序，包括 SAP TREX、SAP LiveCache 和 SAP 内容服务器。 以及所有数据库，但 SAP HANA 除外。


| SAP 产品 | 来宾 OS | RDBMS | 虚拟机类型 |
| --- | --- | --- | --- |
| SAP 业务套件软件 | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle（仅限 Windows 和 Oracle Linux）、DB2、SAP ASE |A5 到 A11、D11 到 D14、DS11 到 DS14、DS11_v2 到 DS15_v2、GS1 到 GS5、D2s_v3 到 D64s_v3、D2as_v4 D64as_v4、E2s_v3 到 E64s_v3、E2as_v4 到 E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |
| 多合一 SAP 业务软件 | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle（仅限 Windows 和 Oracle Linux）、DB2、SAP ASE |A5 到 A11、D11 到 D14、DS11 到 DS14、DS11_v2 到 DS15_v2、GS1 到 GS5、D2s_v3 到 D64s_v3、D2as_v4 D64as_v4、E2s_v3 到 E64s_v3、E2as_v4 到 E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |
| SAP BusinessObjects BI | Windows |不适用 |A5 到 A11、D11 到 D14、DS11 到 DS14、DS11_v2 到 DS15_v2、GS1 到 GS5、D2s_v3 到 D64s_v3、D2as_v4 D64as_v4、E2s_v3 到 E64s_v3、E2as_v4 到 E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |
| SAP NetWeaver | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle（仅限 Windows 和 Oracle Linux）、DB2、SAP ASE |A5 到 A11、D11 到 D14、DS11 到 DS14、DS11_v2 到 DS15_v2、GS1 到 GS5、D2s_v3 到 D64s_v3、D2as_v4 D64as_v4、E2s_v3 到 E64s_v3、E2as_v4 到 E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>在 Azure 上支持的其他 SAP 工作负荷

| SAP 产品 | 来宾 OS | RDBMS | 虚拟机类型 |
| --- | --- | --- | --- |
| SQL Server 上的 SAP Business On | Windows  | SQL Server | 所有 NetWeaver 认证的 VM 类型<br /> [SAP 说明 #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows 和 Linux | | 所有 NetWeaver 认证的 VM 类型<br /> SAP 说明 #2451795 |
| SAP 业务对象 BI 平台 | Windows 和 Linux | | SAP 说明 #2145537 |
| SAP 数据服务 4.2 | | | SAP 说明 #2288344 |
| SAP Hybris Commerce 平台  | Windows | SQL Server、Oracle | 所有 NetWeaver 认证的 VM 类型 <br /> [Hybris 文档](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris Commerce 平台  | SLES 12 或更高 | SAP HANA | 所有 NetWeaver 认证的 VM 类型 <br /> [Hybris 文档](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris Commerce 平台  | RHEL 7 或更高 | SAP HANA | 所有 NetWeaver 认证的 VM 类型 <br /> [Hybris 文档]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP (Hybris) Commerce Platform 1811 及更高版本  | Windows、SLES 或 RHEL | SQL Azure DB | 所有 NetWeaver 认证的 VM 类型 <br /> [Hybris 文档](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
