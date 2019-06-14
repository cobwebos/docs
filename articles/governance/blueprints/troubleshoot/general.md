---
title: 排查常见错误
description: 了解如何排查创建、分配和删除蓝图时出现的问题。
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 42fdd6645a7a0e7cd9a2f0a7bc969e8eee62758c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60874954"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>排查使用 Azure 蓝图时出现的错误

创建或分配蓝图时可能会出现问题。 本文描述可能会发生的各种错误及其解决方法。

## <a name="finding-error-details"></a>查找错误详细信息

将蓝图分配到作用域是许多错误产生的原因。 分配失败时，蓝图会提供失败部署的详细信息。 此信息会指出存在的问题，以便可以修复问题并确保后续部署成功进行。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 选择**分配蓝图**的页面上的左侧和使用的搜索框进行筛选以查找失败的分配的蓝图分配。 还可以按“预配状态”列对分配表进行排序，集中查看失败的分配项  。

1. 左键单击与蓝图_失败_状态或右键单击并选择**查看分配的详细信息**。

1. 蓝图分配页面顶部有一个红色横幅警告，指出此分配已失败。 单击横幅任意位置可获取更多详细信息。

错误通常是由某个项目导致的，而不是由蓝图整体导致的。 如果某个项目创建密钥保管库但是 Azure 策略阻止密钥保管库创建，则整个分配将失败。

## <a name="general-errors"></a>常规错误

### <a name="policy-violation"></a>场景：策略冲突

#### <a name="issue"></a>问题

由于策略冲突而导致模板部署失败。

#### <a name="cause"></a>原因

如下多个原因可能会导致策略与部署相冲突：

- 正在创建的资源受到策略的限制（通常为 SKU 或位置限制）
- 部署是由策略配置的设置字段（通常带有标记）

#### <a name="resolution"></a>解决方法

更改蓝图，使其不与错误详细信息中的策略冲突。 如果无法进行此更改，替代方法是更改策略分配的作用域，以使蓝图不再与策略冲突。

### <a name="escape-function-parameter"></a>场景：蓝图参数是一个函数

#### <a name="issue"></a>问题

作为函数的蓝图参数在传递到项目之前处理。

#### <a name="cause"></a>原因

将使用函数的蓝图参数（例如 `[resourceGroup().tags.myTag]`）传递到项目会导致在项目上设置的函数的处理结果而不是动态函数。

#### <a name="resolution"></a>解决方法

若要将函数作为参数传递，请使用 `[` 转义整个字符串，使蓝图参数如 `[[resourceGroup().tags.myTag]`。 转义字符会导致蓝图在处理蓝图时将值视为字符串。 然后，蓝图将该函数放置在项目中，使其按预期动态化。 有关详细信息，请参阅[模板文件结构-语法](../../../azure-resource-manager/resource-group-authoring-templates.md#syntax)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 从通过 Azure 专家那里获得答案[Azure 论坛](https://azure.microsoft.com/support/forums/)。
- 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
- 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 