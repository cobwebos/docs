---
title: 页面布局版本
titleSuffix: Azure AD B2C
description: 自定义策略中的 UI 自定义的页面布局版本历史记录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852076"
---
# <a name="page-layout-versions"></a>页面布局版本

页面布局包会定期更新，其中包括页面元素的修复和改进。 以下更改日志详述了在每个版本中引入的更改。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>自断言页面 (selfasserted)

**2.1.0**

- 本地化和辅助功能修补程序。

**2.0.0**

- 在自定义策略中添加了对[显示控件](display-controls.md)的支持。

**1.2.0**

- "用户名/电子邮件" 和 "密码" 字段现在使用 `form` HTML 元素，以允许 Edge 和 Internet Explorer (IE) 正确保存此信息。
- 添加了可配置的用户输入验证延迟，以改善用户体验。
- 辅助功能修复
- 你现在可以 `data-preload="true"` [在 HTML 标记中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 添加特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在 HTML 模板的同时加载链接的 CSS 文件，使其不会在加载文件之间 "闪烁"。
  - 控制在 `script` 页面加载前提取和执行标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 对 Chrome 转换的支持

**1.1.0**

- 删除了取消警报
- 错误元素的 CSS 类
- 显示/隐藏错误逻辑得到了改进
- 删除了默认的 CSS

**1.0.0**

- 初始版本

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>具有密码重置链接 (unifiedssp) 的统一登录注册页

**2.1.0**

- 添加了对多个注册链接的支持。
- 根据策略中定义的谓词规则增加了对用户输入验证的支持。

**1.2.0**

- "用户名/电子邮件" 和 "密码" 字段现在使用 `form` HTML 元素，以允许 Edge 和 Internet Explorer (IE) 正确保存此信息。
- 辅助功能修复
- 你现在可以 `data-preload="true"` [在 HTML 标记中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 添加特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在 HTML 模板的同时加载链接的 CSS 文件，使其不会在加载文件之间 "闪烁"。
  - 控制在 `script` 页面加载前提取和执行标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 对 Chrome 转换的支持

**1.1.0**

- 添加了“使我保持登录状态”(KMSI) 控件

**1.0.0**

- 初始版本

## <a name="mfa-page-multifactor"></a>MFA 页面 (multifactor)

**1.2.1**

- 默认模板的辅助功能修补程序

**1.2.0**

- 辅助功能修复
- 你现在可以 `data-preload="true"` [在 HTML 标记中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 添加特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在 HTML 模板的同时加载链接的 CSS 文件，使其不会在加载文件之间 "闪烁"。
  - 控制在 `script` 页面加载前提取和执行标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 对 Chrome 转换的支持

**1.1.0**

- 删除了“确认代码”按钮
- 代码的输入字段现在只接受最多六 (6) 个字符的输入
- 当用户输入 6 位代码时，该页面会自动尝试验证输入的代码，不需用户单击任何按钮
- 如果代码错误，将自动清除输入字段
- 如果三 (3) 次尝试都输入了不正确的代码，B2C 会将一个错误发送回信赖方
- 辅助功能修复
- 删除了默认的 CSS

**1.0.0**

- 初始版本

## <a name="exception-page-globalexception"></a>异常页 (globalexception) 

**1.2.0**

- 辅助功能修复
- 你现在可以 `data-preload="true"` [在 HTML 标记中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 添加特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在 HTML 模板的同时加载链接的 CSS 文件，使其不会在加载文件之间 "闪烁"。
  - 控制在 `script` 页面加载前提取和执行标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 对 Chrome 转换的支持

**1.1.0**

- 辅助功能修复
- 删除了当策略中没有协定时会出现的默认消息
- 删除了默认的 CSS

**1.0.0**

- 初始版本

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>其他页 (ProviderSelection，ClaimsConsent，UnifiedSSD) 

**1.2.0**

- 辅助功能修复
- 你现在可以 `data-preload="true"` [在 HTML 标记中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 添加特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在 HTML 模板的同时加载链接的 CSS 文件，使其不会在加载文件之间 "闪烁"。
  - 控制在 `script` 页面加载前提取和执行标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 对 Chrome 转换的支持

**1.0.0**

- 初始版本

## <a name="next-steps"></a>后续步骤

有关如何在自定义策略中自定义应用程序的用户界面的详细信息，请参阅 [使用自定义策略自定义应用程序的用户界面](custom-policy-ui-customization.md)。
