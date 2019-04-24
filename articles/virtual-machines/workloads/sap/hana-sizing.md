---
title: 对 Azure SAP HANA（大型实例）调整大小 | Microsoft Docs
description: 对 Azure SAP HANA（大型实例）调整大小。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d403c284eefc1351aabb632977832f2bf11757a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477116"
---
# <a name="sizing"></a>调整大小

为 HANA 大型实例调整大小与为一般 HANA 调整大小没有什么不同。 对于现有的已部署系统，需要从其他 RDBMS 迁移到 HANA，SAP 提供了许多在现有 SAP 系统上运行的报告。 如果数据库移到 HANA，这些报告会针对 HANA 实例检查数据并计算内存需求。 请阅读以下 SAP 说明来获取有关如何运行这些报告以及如何获取其最新修补程序或版本的详细信息：

- [SAP 说明 #1793345 - 为 HANA 上的 SAP 套件调整大小](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP 说明 #1872170 - HANA 和 S/4 HANA 上的套件的大小调整报告](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP 说明 #2121330-常见问题解答：SAP BW on HANA 再大小调整报告](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP 说明 #1736976 - 适用于 HANA 上的 BW 的大小调整报告](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP 说明 #2296290 - 新的适用于 HANA 上的 BW 的大小调整报告](https://launchpad.support.sap.com/#/notes/2296290)

为实现绿色的字段实施，可以使用 SAP Quick Sizer 来计算在 HANA 的基础上实施 SAP 软件时的内存需求。

HANA 的内存需求将随数据量增长而增加。 请注意当前的内存消耗并能够预测将来的内存消耗。 然后，可以根据内存需求将需求映射到其中一个 HANA 大型实例 SKU。

**后续步骤**
- 请参阅[加入要求](hana-onboarding-requirements.md)