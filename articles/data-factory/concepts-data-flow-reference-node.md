---
title: Azure 数据工厂映射数据流引用节点
description: 数据工厂数据流将添加用于联接、查找和联合的引用节点
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 4ed17114cc0ce586c68c5b3e087acffdb82ea96c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030496"
---
# <a name="mapping-data-flow-reference-node"></a>映射数据流引用节点



![引用节点](media/data-flow/referencenode.png "引用节点")

引用节点将自动添加到画布中，以表示它附加到的节点引用了画布上的另一个现有节点。 可以将引用节点视为指向另一个数据流转换的指针或引用。

例如：当联接或联合多个数据流时，数据流画布可能会添加一个反映非主要传入流的名称和设置的引用节点。

引用节点无法移动或删除。 不过，你可以单击节点来修改源转换设置。

用于控制数据流何时添加引用节点的 UI 规则基于可用空间以及行之间的垂直间距。
