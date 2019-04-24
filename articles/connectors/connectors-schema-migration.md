---
title: 将应用迁移到最新架构 - Azure 逻辑应用 | Microsoft Docs
description: 如何将逻辑应用迁移到最新架构版本
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: bf27739bd42106550c18e3bbc27a1ff8b3770747
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60447141"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>将逻辑应用迁移到最新架构版本

若要将现有逻辑应用移到最新架构，请按照下列步骤操作： 

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

2. 在逻辑应用的菜单中，选择“概述”。 在工具栏上，选择“更新架构”。

   > [!NOTE]
   > 当你选择“更新架构”时，Azure 逻辑应用会自动运行迁移步骤并为你提供代码输出。 可以使用此输出更新逻辑应用定义。 但是，请确保遵循以下**最佳做法**部分中所述的最佳做法。

   ![更新架构](./media/connectors-schema-migration/update-schema.png)

   此时将显示“更新架构”页，并显示指向描述新架构中的改进的文档的链接。

## <a name="best-practices"></a>最佳做法

下面是将逻辑应用迁移到最新架构版本的一些最佳做法：

* 将已迁移的脚本复制到新的逻辑应用。 在完成测试并确认迁移的应用按预期工作之前，请勿覆盖旧版本。

* 在投入生产**之前**，测试逻辑应用。

* 完成迁移后，开始更新逻辑应用，以便在可能的情况下使用[托管 API](../connectors/apis-list.md)。 例如，在使用 DropBox v1 的任何地方开始使用 Dropbox v2。

## <a name="next-steps"></a>后续步骤

* 了解如何[手动迁移逻辑应用](../logic-apps/logic-apps-schema-2015-08-01.md)
