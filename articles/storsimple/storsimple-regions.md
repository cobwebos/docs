---
title: "StorSimple 区域可用性 | Microsoft Docs"
description: "说明提供各种 StorSimple 设备模型的 Azure 区域。"
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: d47109d541a3df93d9234e27e53d1538f6bc4c6e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="available-regions-for-your-storsimple"></a>StorSimple 的可用区域

## <a name="overview"></a>概述

Azure 数据中心在全球多个地理位置运行，目的是满足客户对性能、要求和数据位置偏好方面的需求。 Azure 地理位置是至少包含一个 Azure 区域的规定世界区域。 Azure 区域是包含一个或多个数据中心的地理位置内的某个区域。

选择 Azure 区域很重要，同时选择区域受各种因素影响，例如数据驻留和自主性、服务可用性、性能、成本和冗余。 有关如何选择区域的详细信息，请参阅[哪个 Azure 区域适合我？](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

具体说来，对于 StorSimple 解决方案，区域的选择取决于以下因素：

- 提供 StorSimple 设备管理器服务的区域。
- 提供 StorSimple 物理设备、云设备或虚拟设备的国家/地区。
- 存储 StorSimple 数据的存储帐户所在位置可以获得最佳性能的区域。

本教程介绍 StorSimple 设备管理器服务、本地物理设备以及云设备的区域可用性。 本文中包含的信息适用于 StorSimple 8000 和 1200 系列设备。

## <a name="region-availability-for-storsimple-device-manager-service"></a>StorSimple 设备管理器服务的区域可用性

目前有 12 个公共区域和 2 个 Azure 政府区域支持 StorSimple 设备管理器服务。

首次创建 StorSimple 设备管理器服务时定义区域或位置。 一般情况下，请选择离部署设备的地理区域最近的位置。 不过，设备和服务也可部署在不同的位置。

下面是一个区域列表，其中的 StorSimple 设备管理器服务适用于 Azure 公有云，可以进行部署。

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

对于 Azure 政府云，StorSimple 设备管理器服务在美国爱荷华州政府和美国弗吉尼亚州政府数据中心提供。

## <a name="region-availability-for-data-stored-in-storsimple"></a>存储在 StorSimple 中的数据的区域可用性

StorSimple 数据以物理方式存储在 Azure 存储帐户中，这些帐户在所有 Azure 区域可用。 创建 Azure 存储帐户时，会选择存储帐户的主位置，这决定了数据的驻留区域。

首次创建 StorSimple 设备管理器服务并将某个存储帐户与之关联时，可以将 StorSimple 设备管理器服务和 Azure 存储置于两个不同的位置。 在这种情况下，需要分别创建 StorSimple 设备管理器和 Azure 存储帐户。

通常情况下，选择离存储帐户的服务最近的区域。 不过，最近的 Microsoft Azure 区域实际上不一定是延迟最低的区域。 延迟决定了网络服务性能，也即解决方案的性能。 因此，如果选择另一区域的存储帐户，则必须知道服务与存储帐户的关联区域之间的延迟是多少。

如果使用 StorSimple 云设备，则建议将服务和关联的存储帐户置于同一区域。 存储帐户在另一区域可能导致性能不佳。

## <a name="availability-of-storsimple-device"></a>StorSimple 设备的可用性

StorSimple 设备在不同的地理位置或国家/地区可用，具体取决于型号。

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple 物理设备（型号：8100/8600）

如果使用 StorSimple 8100 或 8600 物理设备，可在以下国家/地区购买。

| #  | 国家/地区        | #  | 国家/地区     | #  | 国家/地区      | #  | 国家/地区              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | 澳大利亚      | 16 | 香港特别行政区   | 31 | 新西兰  | 46 | 南非         |
| #N/A  | 奥地利        | 17 | 匈牙利     | 32 | 尼日利亚      | 47 | 韩国          |
| 3  | 巴林        | 18 | 冰岛     | 33 | 挪威       | 48 | 西班牙                |
| 4  | 比利时        | 19 | 印度       | 34 | 秘鲁         | 49 | 斯里兰卡            |
| 5  | 巴西         | 20 | 印度尼西亚   | 35 | 菲律宾  | 50 | 瑞典               |
| 6  | 加拿大         | 21 | 爱尔兰     | 36 | 波兰       | 51 | 瑞士          |
| 7  | 智利          | 22 | 以色列      | 37 | 葡萄牙     | 52 | 台湾               |
| 8  | 哥伦比亚       | 23 | 意大利       | 38 | 波多黎各  | 53 | 泰国             |
| 9  | 捷克共和国 | 24 | 日本       | 39 | 卡塔尔        | 54 | 土耳其               |
| 10 | 丹麦        | 25 | 肯尼亚       | 40 | 罗马尼亚      | 55 | 乌克兰              |
| 11 | 埃及          | 26 | 科威特      | 41 | 俄罗斯       | 56 | 阿拉伯联合酋长国 |
| 12 | 芬兰        | 27 | 澳门       | 42 | 沙特阿拉伯 | 57 | 英国       |
| 13 | 法国         | 28 | 马来西亚    | 43 | 新加坡    | 58 | 美国        |
| 14 | 德国        | 29 | 墨西哥      | 44 | 斯洛伐克     | 59 | 越南              |
| 15 | 希腊         | 30 | 荷兰 | 45 | 斯洛文尼亚     | 60 | 克罗地亚              |

此列表会变化，因为会不断添加更多的国家/地区。 如需地理位置的最新列表，请参阅[产品条款](https://www.microsoft.com/en-us/Licensing/product-licensing)中的“存储阵列条款附录”。

Microsoft 可以向前述列表中的地理位置寄送 StorSimple 物理硬件并提供更换用的硬件备件。

> [!IMPORTANT]
> 请勿将 StorSimple 物理设备置于不支持 StorSimple 的区域。 Microsoft 不会向 StorSimple 不受支持的国家/地区寄送任何更换部件。

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple 云设备（型号：8010/8020）

如果使用 StorSimple 云设备 8010 或 8020，则支持基础 VM 的所有区域都会支持并提供该设备。 8010 使用在所有 Azure 区域都受支持的 Standard_A3 VM。

8020 使用高级存储和 Standard_DS3 VM 来创建云设备。 8020 在支持高级存储和 Standard_DS3 Azure VM 的 Azure 区域受支持。 请使用[此列表](https://azure.microsoft.com/regions/services/)，看“虚拟机”>“DS 系列”和“存储”>“磁盘存储”在区域是否均可用。

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple 虚拟阵列（型号：1200）

如果使用 1200 系列 StorSimple 虚拟阵列，则所有 Azure 区域都支持虚拟磁盘映像。

## <a name="next-steps"></a>后续步骤

* 详细了解[各 StorSimple 型号的定价](https://azure.microsoft.com/pricing/calculator/#storsimple2)。
* 详细了解如何[管理 StorSimple 存储帐户](storsimple-8000-manage-storage-accounts.md)。
* 详细了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。
