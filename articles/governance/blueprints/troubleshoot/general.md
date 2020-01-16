---
title: 排查常见错误
description: 了解如何排查创建、分配和删除蓝图等问题，例如，策略违规和蓝图参数函数。
ms.date: 11/22/2019
ms.topic: troubleshooting
ms.openlocfilehash: 5b8a20b0757934bbd356ab037a22521a248a7eb2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982486"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>排查使用 Azure 蓝图时出现的错误

创建或分配蓝图时可能会出现问题。 本文描述可能会发生的各种错误及其解决方法。

## <a name="finding-error-details"></a>查找错误详细信息

将蓝图分配到作用域是许多错误产生的原因。 分配失败时，蓝图会提供失败部署的详细信息。 此信息会指出存在的问题，以便可以修复问题并确保后续部署成功进行。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 从左侧页面中选择 "**分配的蓝图**"，然后使用 "搜索" 框筛选蓝图分配，查找失败的分配。 还可以按“预配状态”列对分配表进行排序，集中查看失败的分配项。

1. 左键单击状态为 "_失败_" 的蓝图，或右键单击并选择 "**查看分配详细信息**"。

1. 蓝图分配页面顶部有一个红色横幅警告，指出此分配已失败。 单击横幅任意位置可获取更多详细信息。

错误通常是由某个项目导致的，而不是由蓝图整体导致的。 如果某个项目创建密钥保管库但是 Azure Policy 阻止密钥保管库创建，则整个分配将失败。

## <a name="general-errors"></a>常规错误

### <a name="policy-violation"></a>场景：策略冲突

#### <a name="issue"></a>问题

由于策略冲突而导致模板部署失败。

#### <a name="cause"></a>原因

如下多个原因可能会导致策略与部署相冲突：

- 正在创建的资源受到策略的限制（通常为 SKU 或位置限制）
- 部署是由策略配置的设置字段（通常带有标记）

#### <a name="resolution"></a>分辨率

更改蓝图，使其不与错误详细信息中的策略冲突。 如果无法进行此更改，替代方法是更改策略分配的作用域，以使蓝图不再与策略冲突。

### <a name="escape-function-parameter"></a>方案：蓝图参数是一个函数

#### <a name="issue"></a>问题

作为函数的蓝图参数在传递到项目之前处理。

#### <a name="cause"></a>原因

将使用函数的蓝图参数（例如 `[resourceGroup().tags.myTag]`）传递到项目会导致在项目上设置的函数的处理结果而不是动态函数。

#### <a name="resolution"></a>分辨率

若要将函数作为参数传递，请使用 `[` 转义整个字符串，使蓝图参数如 `[[resourceGroup().tags.myTag]`。 转义字符会导致蓝图在处理蓝图时将值视为字符串。 然后，蓝图将该函数放置在项目中，使其按预期动态化。 有关详细信息，请参阅[Azure 资源管理器模板中的语法和表达式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
- 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
- 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。