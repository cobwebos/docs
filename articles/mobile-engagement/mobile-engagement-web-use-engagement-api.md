---
title: Azure Mobile Engagement Web SDK API | Microsoft Docs
description: "Azure Mobile Engagement Web SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 54c22ce6a03e382b1bbde102bccc97deec249b30


---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>在 Web 应用程序中使用 Azure Mobile Engagement API
本文档是介绍如何[在 Web 应用程序中集成 Mobile Engagement](mobile-engagement-web-integrate-engagement.md) 的文档的补充。 它提供了有关如何使用 Azure Mobile Engagement API 报告应用程序统计信息的全部详细信息。

Mobile Engagement API 由 `engagement.agent` 对象提供。 默认的 Azure Mobile Engagement Web SDK 别名是 `engagement`。 可以在 SDK 配置中重新定义此别名。

## <a name="mobile-engagement-concepts"></a>Mobile Engagement 概念
以下部分针对 Web 平台优化常见的 [Mobile Engagement 概念](mobile-engagement-concepts.md)。

### <a name="session-and-activity"></a>`Session` 和 `Activity`
如果用户在两个活动之间保持空闲超过几秒钟，则用户的活动序列被分成两个不同的会话。 这几秒钟被称为会话超时。

如果 Web 应用程序没有自行声明用户活动的结束（通过调用 `engagement.agent.endActivity` 函数），则 Mobile Engagement 服务器会在应用程序页面关闭后三分钟内让用户会话自动过期。 这称为服务器会话超时。

### `Crash`
默认情况下，不会创建未捕获的 JavaScript 异常的自动报告。 但是，可以使用 `sendCrash` 函数手动报告崩溃（请参阅报告崩溃部分）。

## <a name="reporting-activities"></a>报告活动
用户活动报告包括用户何时启动新活动，以及用户何时结束当前活动。

### <a name="user-starts-a-new-activity"></a>用户启动新活动
    engagement.agent.startActivity("MyUserActivity");

需在每次用户活动更改时调用 `startActivity()`。 对此函数的第一次调用会启动一个新的用户会话。

### <a name="user-ends-the-current-activity"></a>用户结束当前活动
    engagement.agent.endActivity();

在用户完成其最后一个活动时，需要至少调用 `endActivity()` 一次。 这将通知 Mobile Engagement Web SDK，用户当前处于空闲状态，并且在会话超时过期后需要关闭用户会话。 如果在会话超时到期之前调用 `startActivity()` 则仅恢复会话。

因为当导航器窗口关闭时没有可靠的调用，所以通常很难或者无法在 Web 环境中捕获用户活动的结束。 这就是为什么 Mobile Engagement 服务器会在应用程序页面关闭后的三分钟内自动过期用户会话的原因。

## <a name="reporting-events"></a>报告事件
事件报告包括会话事件和独立事件。

### <a name="session-events"></a>会话事件
会话事件通常用于报告用户在用户会话期间所执行的操作。

**无其他数据的示例：**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**具有其他数据的示例：**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>独立事件
与会话事件不同，独立事件可在会话的上下文的外部出现。

为此，请使用 ``engagement.agent.sendEvent`` 而不使用 ``engagement.agent.sendSessionEvent``。

## <a name="reporting-errors"></a>报告错误
错误报告包括会话错误和独立错误。

### <a name="session-errors"></a>会话错误
会话错误通常用于报告在用户会话期间对用户产生影响的错误。

**无其他数据的示例：**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**具有其他数据的示例：**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>独立错误
与会话错误不同，独立错误可在会话的上下文的外部出现。

为此，请使用 `engagement.agent.sendError` 而不使用 `engagement.agent.sendSessionError`。

## <a name="reporting-jobs"></a>报告作业
作业报告包括在作业期间发生的报告错误和事件，以及报告崩溃。

**示例：**

如果要监视 AJAX 请求，则可以使用以下方法：

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>在作业期间报告错误
错误可与运行作业相关，而不是与当前用户会话相关。

**示例：**

如果要在 AJAX 请求失败时报告错误，请使用以下方法：

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>在作业期间报告事件
由于 `engagement.agent.sendJobEvent` 函数，事件可与运行作业相关，而不是与当前用户会话相关。

此函数的工作方式与 `engagement.agent.sendJobError` 完全相同。

### <a name="reporting-crashes"></a>报告崩溃
使用 `sendCrash` 函数手动报告崩溃。

`crashid` 参数是一个字符串，用于标识崩溃的类型。
作为字符串，`crash` 参数通常是崩溃的堆栈跟踪。

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>其他参数
可以将任意数据附加到事件、错误、活动或作业。

数据可以是任意 JSON 对象（但不可是数组或基元类型）。

**示例：**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>限制
适用于其他参数的限制位于密钥、值类型和大小的正则表达式区域中。

#### <a name="keys"></a>密钥
对象中的每个密钥必须与以下正则表达式匹配：

    ^[a-zA-Z][a-zA-Z_0-9]*

这意味着，密钥必须以至少一个字母开头，后跟字母、数字或下划线（\_）。

#### <a name="values"></a>值
值仅限于字符串、数字和布尔值类型。

#### <a name="size"></a>大小
额外信息在每次调用时的字符数限制为 1,024 个字符（在 Mobile Engagement Web SDK 以 JSON 编码后）。

## <a name="reporting-application-information"></a>报告应用程序信息
可使用 `sendAppInfo()` 函数手动报告跟踪信息（或任何其他应用程序特定的信息）。

请注意，可以以增量方式发送此信息。 对于特定设备，将只保留特定密钥的最新值。

与事件额外信息一样，可以使用任何 JSON 对象提取应用程序信息。 请注意，数组或子对象被视为平面字符串（使用 JSON 序列化）。

**示例：**

以下是用于发送用户的性别和出生日期的代码示例：

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>限制
适用于应用程序信息的限制位于密钥和大小的正则表达式区域中。

#### <a name="keys"></a>密钥
对象中的每个密钥必须与以下正则表达式匹配：

    ^[a-zA-Z][a-zA-Z_0-9]*

这意味着，密钥必须以至少一个字母开头，后跟字母、数字或下划线（\_）。

#### <a name="size"></a>大小
应用程序信息在每次调用时的字符数限制为 1,024 个字符（在 Mobile Engagement Web SDK 以 JSON 编码后）。

在前面的示例中，发送到服务器的 JSON 为 44 个字符的长度：

    {"birthdate":"1983-12-07","gender":"female"}



<!--HONumber=Nov16_HO3-->


