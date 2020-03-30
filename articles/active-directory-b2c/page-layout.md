---
title: 页面布局版本
titleSuffix: Azure AD B2C
description: 用于自定义策略中的 UI 自定义的页面布局版本历史记录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183969"
---
# <a name="page-layout-versions"></a>页面布局版本

页面布局包会定期更新，其中包括页面元素的修复和改进。 以下更改日志详述了在每个版本中引入的更改。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- 自断言页面 (`selfasserted`)
  - 在自定义策略中添加了对[显示控件](display-controls.md)的支持。

## <a name="120"></a>1.2.0

- 所有页面
  - 辅助功能修复
  - 现在，`data-preload="true"`您可以在 HTML[标记中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content)添加该属性，以控制 CSS 和 JavaScript 的加载顺序。
    - 与 HTML 模板同时加载链接的 CSS 文件，这样不会在加载文件之间"闪烁"。
    - 控制标记在`script`页面加载之前提取和执行的顺序。
  - 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
  - 对 Chrome 转换的支持
- 统一和自断言页面
  - 用户名/电子邮件和密码字段现在使用`form`HTML 元素允许边缘和 Internet 资源管理器 （IE） 正确保存此信息。

## <a name="110"></a>1.1.0

- 异常页面 (globalexception)
  - 辅助功能修复
  - 删除了当策略中没有协定时会出现的默认消息
  - 删除了默认的 CSS
- MFA 页面 (multifactor)
  - 删除了“确认代码”按钮
  - 代码的输入字段现在只接受最多六 (6) 个字符的输入
  - 当用户输入 6 位代码时，该页面会自动尝试验证输入的代码，不需用户单击任何按钮
  - 如果代码错误，将自动清除输入字段
  - 如果三 (3) 次尝试都输入了不正确的代码，B2C 会将一个错误发送回信赖方
  - 辅助功能修复
  - 删除了默认的 CSS
- 自断言页面 (selfasserted)
  - 删除了取消警报
  - 错误元素的 CSS 类
  - 显示/隐藏错误逻辑得到了改进
  - 删除了默认的 CSS
- 统一 SSP (unifiedssp)
  - 添加了“使我保持登录状态”(KMSI) 控件

## <a name="100"></a>1.0.0

- 初始版本

## <a name="next-steps"></a>后续步骤

有关如何在自定义策略中自定义应用程序的用户界面的详细信息，请参阅[使用自定义策略自定义应用程序的用户界面](custom-policy-ui-customization.md)。
