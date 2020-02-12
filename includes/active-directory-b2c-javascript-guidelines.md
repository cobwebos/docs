---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: marsma
ms.openlocfilehash: 687853720e8f963d5a58093b824bb36e8063bcf7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149060"
---
## <a name="guidelines-for-using-javascript"></a>JavaScript 的使用准则

按照以下准则，使用 JavaScript 自定义应用程序的界面：

- 不对 `<a>` HTML 元素绑定单击事件。
- 不依赖 Azure AD B2C 代码或注释。
- 不更改 Azure AD B2C HTML 元素的顺序或层次结构。 使用 Azure AD B2C 策略控制 UI 元素的顺序。
- 在调用 RESTful 服务时考虑到以下事项：
    - 可能需要设置 RESTful 服务 CORS 才能实现客户端 HTTP 调用。
    - 确保 RESTful 服务安全，且它仅使用 HTTPS 协议。
    - 不直接使用 JavaScript 来调用Azure AD B2C 终结点。
- 可以嵌入 JavaScript，也可以链接到外部 JavaScript 文件。 如果是使用外部 JavaScript 文件，请确保使用 绝对 URL，而不是相对 URL。
- JavaScript 框架：
    - Azure AD B2C 使用 jQuery 的特定版本。 不包括 jQuery 的其他版本。 在同一页面上使用多个版本会造成问题。
    - 不支持使用 RequireJS。
    - Azure AD B2C 不支持大多数 JavaScript 框架。
- 可通过调用 `window.SETTINGS`、`window.CONTENT` 对象读取 Azure AD B2C 设置，例如当前的 UI 语言。 勿更改这些对象的值。
- 若要自定义 Azure AD B2C 错误消息，请在策略中使用本地化。
- 如果可以通过策略实现一切操作，建议这样做。
