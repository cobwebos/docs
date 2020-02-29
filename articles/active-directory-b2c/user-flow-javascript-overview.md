---
title: JavaScript 和页面布局版本
titleSuffix: Azure AD B2C
description: 了解如何启用 JavaScript 并使用 Azure Active Directory B2C 中的页面布局版本。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185824"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 JavaScript 和页面布局版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 提供了一组打包内容，其中包含用户流中的用户界面元素和自定义策略中的 HTML、CSS 和 JavaScript。

为应用程序启用 JavaScript：

* 使用 Azure 门户在用户流上启用
* 选择[页面布局](page-layout.md)
* 在请求中使用[b2clogin.com](b2clogin.md)

如果你打算启用[javascript](javascript-samples.md)客户端代码，则你基于 javascript 的元素必须是不可变的。 如果它们不是不可变的，则任何更改都可能导致用户页面出现意外行为。 若要防止这些问题，请强制使用页面布局并指定页面布局版本，以确保基于 JavaScript 的内容定义是不可变的。 即使你不打算启用 JavaScript，你也可以为页面指定页面布局版本。

## <a name="enable-javascript"></a>启用 JavaScript

在用户流**属性**中，可以启用 JavaScript。 启用 JavaScript 还会强制使用页面布局。 然后，可以按下一部分所述设置用户流的页面布局版本。

![突出显示 "启用 JavaScript" 设置的 "用户流属性" 页](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>选择页面布局版本

无论是否在用户流属性中启用 JavaScript，都可以为用户流页面指定页面布局版本。 打开用户流并选择**页面布局**。 在 "**布局名称**" 下，选择 "用户流" 页，然后选择**页面布局版本**。

有关不同页面布局版本的信息，请参阅[页面布局版本更改日志](page-layout.md)。

![门户中的页面布局设置显示页面布局版本下拉列表](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>后续步骤

可以在 Javascript 示例中找到 JavaScript 用法的示例[，以便在 Azure Active Directory B2C 中使用](javascript-samples.md)。
