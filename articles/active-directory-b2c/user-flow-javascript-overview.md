---
title: JavaScript 和页面布局版本
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中启用 JavaScript 和使用页面布局版本。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 50644ad3be226648accba6a2f43d4ea068ff977c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258840"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 JavaScript 和页面布局版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 提供一组打包的内容，其中包含的 HTML、CSS 和 JavaScript 适用于用户流中的用户界面元素和自定义策略。

若要为应用程序启用 JavaScript，请执行以下操作：

* 使用 Azure 门户在用户流上启用它
* 选择一个[页面布局](page-layout.md)
* 在请求中使用[b2clogin.com](b2clogin.md)

如果打算启用 [JavaScript](javascript-samples.md) 客户端代码，则 JavaScript 所基于的元素必须是不可变的。 如果它们不是不可变的，则任何更改都可能会导致用户页上出现意外行为。 为了防止这些问题，请强制使用页面布局，并指定页面布局版本，确保 JavaScript 所基于的内容定义是不可变的。 即使不打算启用 JavaScript，也可为页面指定页面布局版本。

## <a name="enable-javascript"></a>启用 JavaScript

在用户流**属性**中，可以启用 JavaScript。 启用 JavaScript 也会强制使用页面布局。 然后就可以设置用户流的页面布局版本，如下一部分所述。

![用户流属性页面，突出显示了“启用 JavaScript”设置](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>选择页面布局版本

不管是否在用户流的属性中启用 JavaScript，都可以为用户流页面指定页面布局版本。 打开用户流，然后选择“页面布局”  。 在“布局名称”下选择一个用户流页面，然后选择“页面布局版本(预览版)”。 

若要了解不同的页面布局版本，请参阅[页面布局版本更改日志](page-layout.md)。

![门户中的页面布局设置，显示了页面布局版本下拉列表](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>后续步骤

有关 JavaScript 使用的示例，可参阅[在 Azure Active Directory B2C 中使用的 JavaScript 示例](javascript-samples.md)。
