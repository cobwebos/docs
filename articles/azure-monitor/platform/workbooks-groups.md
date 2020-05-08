---
title: Azure Monitor 工作簿组
description: 如何使用 Azure Monitor 工作簿中的组。
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: b6377cdcdb5816426eba62fdbef79eeb42659dcc
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82892077"
---
# <a name="how-to-use-groups-in-workbooks"></a>如何在工作簿中使用组

使用工作簿中的组项，可以对工作簿中的一组步骤进行逻辑分组。

工作簿中的组适用于以下几个方面：

- 布局
  - 在希望垂直组织项的情况下，您可以创建一组项，这些项将全部堆积，并将该组的样式设置为百分比宽度，而不是设置所有单个项的百分比宽度。
- 可见性
  - 如果希望多个项同时隐藏/显示，可以设置整个项组的可见性，而不是在每个项上设置可见性设置。 这对于使用制表符的模板非常有用，因为您可以使用组作为选项卡的内容，并且可以根据所选选项卡设置的参数隐藏/显示整个组。
- 性能
  - 如果你有一个非常大的模板和多个节或选项卡，则可以将每个部分转换为其自己的子模板，并使用组来加载顶级模板内的所有子模板。 子模板的内容不会加载或运行，直到用户使这些组可见。 详细了解[如何将大型模板拆分为多个模板](#how-to-split-a-large-template-into-many-templates)。

## <a name="using-groups"></a>使用组

若要向工作簿添加组，请选择 "*添加*"，然后选择 "*添加组*"。

![依次选择 "添加" 和 "组"。](./media/workbooks-groups/add-group.png)

下面是处于读取模式的组的屏幕截图，其中包含两个项：文本项和查询项。  

![在读取模式下的组。](./media/workbooks-groups/groups-view.png)

编辑工作簿时，可以看到这两项实际位于组项中：

![在编辑模式下的组。 ](./media/workbooks-groups/groups-edit.png)

在上面的屏幕截图中，组处于编辑模式，其中显示了两个项（位于虚线区域内）。 每个步骤都可以处于 "编辑" 或 "读取" 模式，相互独立。 例如，在查询步骤处于读取模式时，文本步骤处于编辑模式。

## <a name="scoping"></a>Scoping

当前，组被视为工作簿中的新作用域。 在组中创建的所有参数仅在组内可见。 这也适用于合并，只能查看组内或父级别的数据。

## <a name="group-types"></a>组类型

工作簿组项允许您向工作簿添加一组项。 作为工作簿的作者，你可以指定将属于哪种类型的组。 有两种类型的组：

- Editable
  - 工作簿中的组允许您添加、删除或编辑组中项的内容。 这通常用于布局和可见性。
- 从模板
  - 工作簿中的组按其 ID 从另一个模板的内容中加载。 在运行时，将加载该模板的内容并将其合并到工作簿中。 在编辑模式下，你不能修改组中的任何内容，因为它们将在下一次加载项时直接从模板中加载。  

## <a name="load-types"></a>加载类型

可以通过几种不同的方式加载组的内容。 作为工作簿的作者，你可以指定将加载组内容的时间和方式。

### <a name="lazy-the-default"></a>懒惰（默认值）

只有当项可见时，组才会加载。 这允许选项卡项使用组。 如果从未选择该选项卡，则组永远不会变得可见，因此不会加载内容。

对于从模板创建的组，不会检索模板的内容，并且不会创建组中的项，直到该组变为可见。 检索内容时，用户将看到整个组的进度 spinners。

### <a name="explicit"></a>显式

在此模式下，会在组将显示的位置显示一个按钮，并且在用户显式单击此按钮以加载内容之前，不会检索或创建任何内容。 这在内容可能需要计算或很少使用的情况下很有用。 作者可以指定要在按钮上显示的文本。

下面是显示已配置的 "加载更多" 按钮的显式加载设置的屏幕截图。

![显式加载设置](./media/workbooks-groups/groups-explicitly-loaded.png)

在工作簿中加载之前的组：

![在工作簿中加载前显式组](./media/workbooks-groups/groups-explicitly-loaded-before.png)

在工作簿中加载后的组：

![在工作簿中加载后的显式组](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Always

在此模式下，将始终加载并在加载工作簿后创建组的内容。 仅当使用组进行布局时，这是最常用的，其中内容将始终可见。

## <a name="using-templates-inside-a-group"></a>使用组内的模板

如果将某个组配置为从模板加载，则默认情况下会加载该内容，它只会在组可见时加载。

将模板加载到组中时，工作簿会尝试合并模板中声明的任何参数，这些参数是使用组中已有的参数加载的。 工作簿中已存在具有相同名称的任何参数将合并到正在加载的模板。 如果参数步骤中的所有参数都已合并，则整个参数步骤将会消失。

### <a name="example-1-all-parameters-have-identical-names"></a>示例1：所有参数都具有相同的名称

请考虑在顶部有两个参数的模板。

- `TimeRange`-时间范围参数。
- `Filter`-一个文本参数。

![编辑参数项： "顶级参数"](./media/workbooks-groups/groups-top-level-params.png)

然后，组项加载另一个模板，该模板具有其自己的两个参数和一个文本步骤，其中的参数命名为：

![编辑第二个模板的参数项](./media/workbooks-groups/groups-merged-away.png)

当第二个模板加载到组中时，重复的参数将被合并。由于所有参数都已合并，因此内部参数步骤还会合并，从而导致组仅包含文本步骤。

### <a name="example-2-one-parameter-has-an-identical-name"></a>示例2：一个参数具有相同的名称

请考虑在顶部有两个参数的组模板。

- `TimeRange`-时间范围参数。
- `FilterB`-一个文本参数，请注意，它`Filter`与顶层模板的使用方式不同。

![编辑已合并的参数结果的组项](./media/workbooks-groups/groups-wont-merge-away.png)

加载组的 item's 模板时，会将`TimeRange`参数合并到组中。 然后，该工作簿将具有带有`TimeRange`和`Filter`的初始参数步骤，并且组的参数步骤将仅包括`FilterB`

![不会合并的参数的结果](./media/workbooks-groups/groups-wont-merge-away-result.png)

如果加载的模板包含`TimeRange`和`Filter` （而不是`FilterB`），则生成的工作簿会有一个参数步骤和一个仅包含文本步骤的组。

## <a name="how-to-split-a-large-template-into-many-templates"></a>如何将大型模板拆分为多个模板

若要提高性能，将大型模板分解为多个较小的模板，以便用户可以按需加载某些内容延迟或按需。 这使得初始加载速度更快，因为顶层模板会小得多。

将模板拆分为多个部件时，实际上需要将模板拆分为多个单独工作的模板（子模板）。 因此，如果顶层模板具有其他步骤使用`TimeRange`的参数，则子模板还需要具有一个参数步骤来定义具有确切名称的参数。 这允许子模板独立工作，并使其在组中的更大模板内加载。

若要将较大的模板转换为多个子模板：

1.  在工作簿顶部附近的共享参数之后创建一个新的空组。 此新组最终将成为子模板。
2. 创建共享参数步骤的副本，然后使用 "*移入组*" 将副本移到步骤1中创建的组。 此参数步骤将允许子模板独立于外部模板工作，并在外部模板内加载时合并。
    > [!NOTE]
    > 从技术上来讲，子模板不需要将这些参数合并在一起，如果你从来不打算单独显示子模板。 不过，如果以后需要这样做，则会很难编辑或调试。

3. 将所需的工作簿中的每个项移动到步骤1中创建的组。
4. 如果步骤3中移动的单个步骤具有条件可见性，则会成为外部组的可见性（如在选项卡中使用）。 将其从组内的项中删除，并将该可见性设置添加到组本身。 保存此处以避免丢失更改和/或导出并保存 json 内容的副本。
5. 如果希望从模板加载该组，可以使用组中的 "*编辑*" 工具栏按钮。 这将只在新窗口中打开该组的内容作为工作簿。 然后，您可以根据需要保存该工作簿，并关闭此工作簿视图（不要关闭浏览器，而只是将该视图返回到您正在编辑的工作簿）。
6. 然后，可以将 "组步骤" 更改为 "从模板加载"，并将 "模板 ID" 字段设置为在步骤5中创建的工作簿/模板。 若要使用工作簿 Id，源需要是共享工作簿资源 ID。 按 "*加载*"，此时将从该子模板加载该组的内容，而不是保存在此外部工作簿中。

## <a name="next-steps"></a>后续步骤
- [工作簿概述](workbooks-overview.md)
- [将 JSONPath 与工作簿一起使用](workbooks-jsonpath.md)