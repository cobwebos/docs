---
title: 页面布局版本
titleSuffix: Azure AD B2C
description: 自定义策略中的 UI 自定义的页面布局版本历史记录。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b24e8cc5f6bb6213bf021362c5654afd4d5266fb
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620611"
---
# <a name="page-layout-versions"></a>页面布局版本

页面布局包会定期更新，以包括其页面元素中的修复和改进。 以下更改日志指定了每个版本中引入的更改。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- 自断言页面（`selfasserted`）
  - 添加了对自定义策略中的[显示控件](display-controls.md)的支持。

## <a name="120"></a>1.2.0

- 所有页面
  - 辅助功能修补程序
  - 你现在可以[在 HTML 标记中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content)添加 `data-preload="true"` 特性来控制 CSS 和 JavaScript 的加载顺序。
    - 在 HTML 模板的同时加载链接的 CSS 文件，使其不会在加载文件之间 "闪烁"。
    - 控制在页面加载前提取和执行 `script` 标记的顺序。
  - 现在 `type=email` 电子邮件字段，移动键盘将提供正确的建议
  - 对 Chrome 转换的支持
- 统一和自断言页面
  - 用户名/电子邮件和密码字段现在使用 `form` HTML 元素，以允许边缘和 Internet Explorer （IE）正确保存此信息。

## <a name="110"></a>1.1.0

- 异常页面（globalexception）
  - 辅助功能修复
  - 当策略中没有联系人时删除默认消息
  - 已删除默认 CSS
- MFA 页面（多因素）
  - 已删除 "确认代码" 按钮
  - 代码的输入字段现在只能输入最多六个字符
  - 当输入6位代码时，此页将自动尝试验证输入的代码，而无需单击任何按钮
  - 如果代码错误，将自动清除输入字段
  - 三（3）次尝试错误代码后，B2C 会将错误发送回信赖方
  - 辅助功能修补程序
  - 已删除默认 CSS
- 自断言页面（selfasserted）
  - 删除取消警报
  - Error 元素的 CSS 类
  - 显示/隐藏错误逻辑改进
  - 已删除默认 CSS
- 统一 SSP （unifiedssp）
  - 添加了 "使我保持登录（KMSI）" 控件

## <a name="100"></a>1.0.0

- 初始版本

## <a name="next-steps"></a>后续步骤

有关如何在自定义策略中自定义应用程序的用户界面的详细信息，请参阅[使用自定义策略自定义应用程序的用户界面](custom-policy-ui-customization.md)。
