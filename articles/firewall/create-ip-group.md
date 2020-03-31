---
title: 在 Azure 防火墙中创建 IP 组
description: IP 组允许您对 Azure 防火墙规则的 IP 地址进行分组和管理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444532"
---
# <a name="create-ip-groups-preview"></a>创建 IP 组（预览版）

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IP 组允许您对 Azure 防火墙规则的 IP 地址进行分组和管理。 它们可以具有单个 IP 地址、多个 IP 地址或一个或多个 IP 地址范围。

## <a name="create-an-ip-group"></a>创建 IP 组

1. 在 Azure 门户主页上，选择“创建资源”。****
2. 在搜索文本框中键入**IP 组**，然后选择**IP 组**。
3. 选择 **“创建”**。
4. 选择订阅。
5. 选择一个资源组或新建一个资源组。
6. 键入 IP 组的唯一名称，然后选择区域。

6. 选择**下一个：IP 地址**。
7. 键入 IP 地址、多个 IP 地址或 IP 地址范围。

   有两种方法可以输入 IP 地址：
   - 您可以手动输入它们
   - 可以从文件导入它们

   要从文件导入，请选择**从文件导入**。 您可以将文件拖动到框中，也可以选择 **"浏览文件**"。 如有必要，您可以查看和编辑上传的 IP 地址。

   键入 IP 地址时，门户会对其进行验证，以检查其重叠、重复和格式设置问题。

5. 完成后，选择 **"审阅 + 创建**"。
6. 选择 **“创建”**。


## <a name="next-steps"></a>后续步骤

- [了解有关 IP 组的更多](ip-groups.md)