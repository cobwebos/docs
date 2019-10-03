---
title: JavaScript 和页面布局版本-Azure Active Directory B2C |Microsoft Docs
description: 了解如何启用 JavaScript 并使用 Azure Active Directory B2C 中的页面布局版本。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0eb5c89387d8bdcf0e0b72c669c42f716ff5fbb3
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227105"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 JavaScript 和页面布局版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 提供一组打包的内容，其中包含的 HTML、CSS 和 JavaScript 适用于用户流中的用户界面元素和自定义策略。 若要为应用程序启用 JavaScript, 你必须将一个元素添加到你的[自定义策略](active-directory-b2c-overview-custom.md), 或在门户中为用户流启用该元素, 选择页面布局, 并在请求中使用[b2clogin.com](b2clogin.md) 。

如果你打算启用 [JavaScript](javascript-samples.md) 客户端代码，则需要确保 JavaScript 所基于的元素是不可变的。 否则，任何更改都可能会导致用户页上出现意外行为。 若要防止这些问题, 可以强制使用页面布局并指定页面布局版本。 这样做可以确保 JavaScript 所基于的所有内容定义不可变。 即使你不打算启用 JavaScript, 你也可以为页面指定页面布局版本。

## <a name="user-flows"></a>用户流

在用户流**属性**中, 可以启用 JavaScript, 这也会强制使用页面布局。 然后, 可以按下一部分所述设置用户流的页面布局版本。

![突出显示 "启用 JavaScript" 设置的 "用户流属性" 页](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>选择页面布局版本

无论是否在用户流属性中启用 JavaScript, 都可以为用户流页面指定页面布局版本。 打开用户流并选择**页面布局**。 在 "**布局名称**" 下, 选择 "用户流" 页, 然后选择**页面布局版本**。

有关不同页面布局版本的信息, 请参阅[版本更改日志](page-layout.md#version-change-log)。

![门户中的页面布局设置显示页面布局版本下拉列表](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>自定义策略

若要在自定义策略中启用 JavaScript，请将 **ScriptExecution** 元素添加到自定义策略文件中的 **RelyingParty** 元素。 有关详细信息，请参阅[在 Azure Active Directory B2C 中使用的 JavaScript 示例](javascript-samples.md)。

不管是否在自定义策略中启用 JavaScript, 都可以为页面指定页面布局版本。 有关指定页面布局的详细信息, 请参阅[使用自定义策略在 Azure Active Directory B2C 中选择页面布局](page-layout.md)。

## <a name="next-steps"></a>后续步骤

有关不同页面布局版本的信息, 请参阅[使用自定义策略在 Azure Active Directory B2C 中选择页面布局](page-layout.md#version-change-log)的**版本更改日志**部分。

可以在 Javascript 示例中找到 JavaScript 用法的示例[, 以便在 Azure Active Directory B2C 中使用](javascript-samples.md)。
