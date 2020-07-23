---
title: 在 Azure 防火墙中创建 IP 组
description: IP 组允许对 Azure 防火墙规则的 IP 地址进行分组和管理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85602527"
---
# <a name="create-ip-groups"></a>创建 IP 组

IP 组允许对 Azure 防火墙规则的 IP 地址进行分组和管理。 它们可以有单个 IP 地址、多个 IP 地址或一个或多个 IP 地址范围。

## <a name="create-an-ip-group"></a>创建 IP 组

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在 "搜索" 文本框中键入**Ip 组**，然后选择 " **ip 组**"。
3. 选择“创建”。
4. 选择订阅。
5. 选择一个资源组或新建一个资源组。
6. 为 "IP 组" 键入一个唯一的名称，然后选择一个区域。

6. 选择 "**下一步： IP 地址**"。
7. 键入 IP 地址、多个 IP 地址或 IP 地址范围。

   可以通过两种方式输入 IP 地址：
   - 你可以手动输入它们
   - 你可以从文件导入它们

   若要从文件导入，请选择 "**从文件导入**"。 您可以将文件拖到 "选择**文件**" 框中。 如有必要，你可以查看和编辑已上传的 IP 地址。

   键入 IP 地址时，门户会对其进行验证，以检查重叠、重复和格式设置问题。

5. 完成后，选择 "**审核 + 创建**"。
6. 选择“创建”。


## <a name="next-steps"></a>后续步骤

- [了解有关 IP 组的详细信息](ip-groups.md)