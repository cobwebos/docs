---
title: 将应用迁移到最新架构
description: 如何将逻辑应用迁移到最新架构版本
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: dc967c90f28b29a63a1f26d0aae86b6d769c1ccf
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786922"
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
