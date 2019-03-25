---
title: 适用于 Azure Active Directory B2C 中的用户流的 JavaScript 和页面协定版本 | Microsoft Docs
description: 了解如何启用 JavaScript 并使用页面协定版本来自定义 Azure Active Directory B2C 中的用户流。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401561"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>关于如何在用户流中使用 JavaScript 和页面协定版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 提供一组打包的内容，其中包含的 HTML、CSS 和 JavaScript 适用于用户流中的用户界面元素。 若要在用户流中启用 [JavaScript](javascript-samples.md) 客户端代码，需确保 JavaScript 所基于的元素是不可变的。 否则，任何更改都可能会导致用户流页面上出现意外行为。 为了防止这些问题，可以将页面协定强制用于用户流，并指定页面协定版本。 这样做可以确保 JavaScript 所基于的所有内容定义不可变。 即使不打算为用户流启用 JavaScript，也可为用户流页面指定页面协定版本。

> [!NOTE]
> 本文讨论适用于用户流的 JavaScript，但你也可以在使用 JavaScript 和[自定义策略](page-contract.md)时选择页面协定版本。

## <a name="enable-javascript"></a>启用 JavaScript

在用户流属性中，可以启用 JavaScript，这样也会强制使用页面协定。 然后就可以设置页面协定版本，如下一部分所述。

![启用 JavaScript 设置](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>指定页面协定版本

不管是否在用户流的属性中启用 JavaScript，都可以为用户流页面指定页面协定版本。 打开用户流，然后选择“页面布局”。 在“布局名称”下选择一个用户流页面，然后选择“页面协定版本”。

![启用 JavaScript 设置](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>后续步骤
请参阅[用于 Azure Active Directory B2C 中的 JavaScript 示例](javascript-samples.md)。
