---
title: "Microsoft Azure StorSimple Data Manager 概述 | Microsoft 文档"
description: "提供 StorSimple Data Manager 服务（个人预览版）的概述"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 32fcca540c1db317c5f11e94b2ee3359cadcc806
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73

---

# <a name="storsimple-data-manager-overview-private-preview"></a>StorSimple Data Manager 概述（个人预览版）

## <a name="overview"></a>概述

Microsoft Azure StorSimple 是一种混合型云存储解决方案，可解决通常与文件共享关联的非结构化数据的复杂性。 StorSimple 使用云存储作为本地解决方案的扩展，可跨本地存储和云存储自动将数据分层。 使用本地快照和云快照的集成式数据保护不需要扩建存储基础结构。 存档和灾难恢复也与充当场外位置的云无缝集成。

使用此文档中介绍的数据转换服务可以无缝地访问云中的 StorSimple 数据。 此服务提供的 API 从 StorSimple 提取数据并以可以随时使用的格式将数据提交给其他 Azure 服务。 此预览版支持的格式是 Azure blob 和 Azure 媒体服务资产。 通过此转换，可轻松地连接 Azure 媒体服务、Azure HDInsight、Azure 机器学习和 Azure 搜索等服务以操作 StorSimple 8000 系列本地设备上的数据。

下面显示了说明此内容的概要框图。

![概要关系图](./media//storsimple-data-manager-overview/high-level-diagram.png)

本文档说明如何注册此服务的个人预览版。 它还说明如何使用此服务编写使用 StorSimple 数据和云中的其他 Azure 服务的应用程序。

## <a name="sign-up-for-data-manager-preview"></a>注册 Data Manager 预览版
在注册 Data Manager 服务之前，请查看以下先决条件。

### <a name="prerequisites"></a>先决条件

此练习假定你具备以下条件：
* 一个有效的 Azure 订阅。
* 可以访问已注册的 StorSimple 8000 系列设备
* 与 StorSimple 8000 系列设备关联的所有密钥。

### <a name="sign-up"></a>注册

StorSimple Data Manager 处于个人预览状态。 执行以下步骤可注册此服务的个人预览版：

1.  使用 StorSimple Data Manager 扩展（位于 [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)） 登录到 Azure 门户。 使用 Azure 凭据登录。

2.  单击 **+** 图标以创建服务。 单击“存储”，然后在打开的边栏选项卡中单击“查看全部”。

    ![搜索 StorSimple Data Manager 图标](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. 看到 StorSimple Data Manager 图标。

    ![选择 StorSimple Data Manager 图标](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. 单击 StorSimple Data Manager 图标，然后单击“创建”。 选择要为个人预览版启用的订阅，然后单击“我注册!”

    ![我注册](./media/storsimple-data-manager-overview/sign-me-up.png)

5. 这将发送加入请求。 我们将尽可能快地将你加入。 启用订阅后，便可以创建 StorSimple Data Manager 服务了。

6. 若要轻松访问 StorSimple Data Manager 服务，请单击星形图标将其固定到收藏夹。

    ![访问 StorSimple Data Manager](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>后续步骤

[使用 StorSimple Data Manager UI 转换数据](storsimple-data-manager-ui.md)。


<!--HONumber=Nov16_HO4-->


