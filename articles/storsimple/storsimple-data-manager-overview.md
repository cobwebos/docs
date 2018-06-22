---
title: Microsoft Azure StorSimple Data Manager 概述 | Microsoft 文档
description: 提供 StorSimple 数据管理器服务的概述
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: vidarmsft
ms.openlocfilehash: d57229ad79909aa0334cc623d727b733a1ec73f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652002"
---
# <a name="storsimple-data-manager-solution-overview"></a>StorSimple 数据管理器解决方案概述

## <a name="overview"></a>概述

Microsoft Azure StorSimple 使用云存储作为本地解决方案的扩展，可跨本地存储和云自动将数据分层。 为最大限度提高效率并降低成本，数据采用删除重复数据的压缩格式存储在云中。 由于数据采用 StorSimple 格式存储，因此用户要使用的其他云应用程序无法直接使用这些数据。

借助 StorSimple 数据管理器，可在云中无缝访问和使用 StorSimple 格式的数据。 通过将 StorSimple 格式转换为可与 Azure 媒体服务、Azure HDInsights 和 Azure 机器学习等其他服务结合使用的本机 blob 和文件，就可实现此目的。

本文概述了 StorSimple 数据管理器解决方案。 它还说明如何使用此服务编写使用 StorSimple 数据和云中的其他 Azure 服务的应用程序。

## <a name="how-it-works"></a>工作原理：

StorSimple 数据管理器服务从 StorSimple 8000 系列本地设备标识云中的 StorSimple 数据。 云中的 StorSimple 数据采用删除重复数据的压缩 StorSimple 格式。 数据管理器服务提供 API 来提取 StorSimple 格式数据，并将其转换为 Azure blob 和 Azure 文件等其他格式。 然后，Azure HDInsight 和 Azure 媒体服务就可轻松使用转换后的数据。 通过数据转换，这些服务便可从 StorSimple 8000 系列本地设备对转换后的 StorSimple 数据进行操作。 下图演示了此流程。

![概要关系图](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>数据管理器用例

可以将数据管理器与 Azure Functions、Azure 自动化和 Azure 数据工厂结合使用，在数据进入 StorSimple 时对其运行工作流。 可能需要通过 Azure 媒体服务处理存储在 StorSimple 上的媒体内容、对该数据运行机器学习算法，或者启动 Hadoop 群集来分析存储在 StorSimple 上的数据。 借助 Azure 提供的大量服务以及 StorSimple 上的数据，就可解锁数据的潜能。


## <a name="region-availability"></a>上市区域

StorSimple 数据管理器在以下 7 个区域中提供：

 - 东南亚
 - 美国东部
 - 美国西部
 - 美国西部 2
 - 美国中西部
 - 北欧
 - 欧洲西部

但是，可在以下区域使用 StorSimple 数据管理器来转换数据。 

![可用于转换数据的区域](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

此范围较大，因为上面任一区域中的资源部署都支持在下面的区域中使用转换流程。 因此，只要数据驻留在 19 个区域中的任何一个区域，就可以使用此服务转换数据。


## <a name="choosing-a-region"></a>选择一个区域

我们建议：
 - 源存储帐户（与 StorSimple 设备关联的帐户）和目标存储帐户（希望其中的数据采用本机格式）位于同一 Azure 区域中。
 - 在包含 StorSimple 存储帐户的区域中使用数据管理器和作业定义。 如果不可行，请在最近的 Azure 区域中使用数据管理器，然后在 StorSimple 存储帐户所在的同一区域中创建作业定义。 

    如果 StorSimple 存储帐户不在支持创建作业定义的 26 个区域中，建议不要运行 StorSimple 数据管理器，因为这样可能会产生较长的延迟和高昂的流出费用。

## <a name="security-considerations"></a>安全注意事项

StorSimple 数据管理器需要服务数据加密密钥才可将数据从 StorSimple 格式转换为本机格式。 在向 StorSimple 服务注册的第一台设备时，会生成服务数据加密密钥。 有关此密钥的详细信息，请参阅 [StorSimple 安全性](storsimple-8000-security.md)。

作为输入提供的服务数据加密密钥存储在密钥保管库中，后者在创建数据管理器时创建。 保管库与 StorSimple 数据管理器位于相同的 Azure 区域中。 删除数据管理器服务时，会同时删除此密钥。

计算资源会使用此密钥来执行转换操作。 这些计算资源与作业定义位于相同的 Azure 区域中。 该区域可能与使用数据管理器的区域相同，也可能不同。

如果数据管理器区域与作业定义区域不同，则必须了解每个区域中驻留的具体数据/元数据。 下图演示了数据管理器区域与作业定义区域不同时产生的效果。

![服务和作业定义位于不同的区域中](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>管理个人信息

StorSimple 数据管理器不收集，也不显示任何个人信息。 有关详细信息，请在[信任中心](https://www.microsoft.com/trustcenter)查看 Microsoft 隐私策略。

## <a name="next-steps"></a>后续步骤

[使用 StorSimple Data Manager UI 转换数据](storsimple-data-manager-ui.md)。
