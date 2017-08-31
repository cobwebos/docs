---
title: "审核与日志记录 - Microsoft 威胁建模工具 - Azure | Microsoft 文档"
description: "针对威胁建模工具中暴露的威胁采取的缓解措施"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 3f1933fc59862eca7ae6ee40bbd5136e449e5cf1
ms.contentlocale: zh-cn
ms.lasthandoff: 08/23/2017

---

# <a name="security-frame-auditing-and-logging--mitigations"></a>安全框架：审核与日志记录 | 缓解措施 
| 产品/服务 | 文章 |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[识别解决方案中的敏感实体并实现更改审核](#sensitive-entities)</li></ul> |
| **Web 应用程序** | <ul><li>[确保在应用程序中强制实施审核与日志记录](#auditing)</li><li>[确保实施日志轮转和分离](#log-rotation)</li><li>[确保应用程序不会记录敏感的用户数据](#log-sensitive-data)</li><li>[确保对审核与日志文件限制访问](#log-restricted-access)</li><li>[确保记录用户管理事件](#user-management)</li><li>[确保在系统中针对滥用提供内置防御机制](#inbuilt-defenses)</li><li>[在 Azure 应用服务中启用 Web 应用的诊断日志记录](#diagnostics-logging)</li></ul> |
| **数据库** | <ul><li>[确保在 SQL Server 中启用登录审核](#identify-sensitive-entities)</li><li>[在 Azure SQL 中启用威胁检测](#threat-detection)</li></ul> |
| **Azure 存储** | <ul><li>[使用 Azure 存储分析来审核对 Azure 存储的访问](#analytics)</li></ul> |
| **WCF** | <ul><li>[实现充分的日志记录](#sufficient-logging)</li><li>[实现充分的审核失败处理](#audit-failure-handling)</li></ul> |
| **Web API** | <ul><li>[确保在 Web API 中强制实施审核与日志记录](#logging-web-api)</li></ul> |
| **IoT 现场网关** | <ul><li>[确保在现场网关中强制实施适当的审核与日志记录](#logging-field-gateway)</li></ul> |
| **IoT 云网关** | <ul><li>[确保在云网关中强制实施适当的审核与日志记录](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>识别解决方案中的敏感实体并实现更改审核

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤**                   | 识别应用程序中包含敏感数据的实体，并针对这些实体和字段实现更改审核 |

## <a id="auditing"></a>确保在应用程序中强制实施审核与日志记录

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤**                   | 在所有组件中启用审核与日志记录。 审核日志应捕获用户上下文。 识别所有重要事件并记录这些事件。 实现集中式日志记录 |

## <a id="log-rotation"></a>确保实施日志轮转和分离

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤**                   | <p>日志轮转是系统管理中使用的一种自动化过程，在此过程中，陈旧的日志文件会存档。 运行大型应用程序的通常服务器通常会记录每个请求：日志变得庞大后，可以借助日志轮转来限制日志的总大小，同时仍可允许分析最近的事件。 </p><p>简单而言，日志分离是指将日志文件存储在运行 OS/应用程序的不同分区中，避免出现拒绝服务攻击或应用程序性能降级</p>|

## <a id="log-sensitive-data"></a>确保应用程序不会记录敏感的用户数据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤**                   | <p>检查是否未记录用户在站点中提交的敏感数据。 检查设计问题造成的有意而为的日志记录及其负面影响。 敏感数据的示例包括：</p><ul><li>用户凭据</li><li>身份证号或其他身份信息</li><li>信用卡号或其他财务信息</li><li>运行状况信息</li><li>私钥，或者其他可用于解密已加密信息的数据</li><li>可以用来增强应用程序攻击效果的系统信息或应用程序信息</li></ul>|

## <a id="log-restricted-access"></a>确保对审核与日志文件限制访问

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤**                   | <p>确保正确设置对日志文件的访问权限。 应用程序帐户应该提供只写访问权限，根据需要为操作员与支持人员提供只读访问权限。</p><p>应该只有管理员帐户才拥有完全访问权限。 检查日志文件中的 Windows ACL，确保 ACL 得到适当的限制：</p><ul><li>应用程序帐户应该拥有只写访问权限</li><li>应该根据需要为操作员和支持人员提供只读访问权限</li><li>应该只有管理员帐户才拥有完全访问权限</li></ul>|

## <a id="user-management"></a>确保记录用户管理事件

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤**                   | <p>确保应用程序监视用户管理事件，例如用户登录成功与失败状态、密码重置、密码更改、帐户锁定和用户注册。 这些措施可帮助检测潜在的可疑行为并对其做出反应。 此外，还能实现操作数据的收集；例如，跟踪谁正在访问应用程序</p>|

## <a id="inbuilt-defenses"></a>确保在系统中针对滥用提供内置防御机制

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤**                   | <p>应该实施相应的控制，以便在发生应用程序滥用时引发安全异常。 例如，如果已实施输入验证，而攻击者尝试注入与正则表达式不匹配的恶意代码，则可以引发异常，指明可能出现了系统滥用</p><p>例如，建议针对以下问题记录安全异常并采取措施：</p><ul><li>输入验证</li><li>CSRF 冲突</li><li>暴力破解（每个资源的每个用户请求数上限）</li><li>文件上传冲突</li><ul>|

## <a id="diagnostics-logging"></a>在 Azure 应用服务中启用 Web 应用的诊断日志记录

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | EnvironmentType - Azure |
| **参考**              | 不适用  |
| **步骤** | <p>Azure 提供内置诊断功能，可帮助调试应用服务 Web 应用。 该功能也适用于 API 应用和移动应用。 应用服务 Web 应用为 Web 服务器和 Web 应用程序中的日志记录信息提供诊断功能。</p><p>这些诊断功能按逻辑分为 Web 服务器诊断和应用程序诊断。</p>|

## <a id="identify-sensitive-entities"></a>确保在 SQL Server 中启用登录审核

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [配置登录审核](https://msdn.microsoft.com/library/ms175850.aspx) |
| **步骤** | <p>必须启用数据库服务器登录审核，以检测/确认密码猜测攻击。 必须捕获失败的登录尝试。 捕获成功和失败的登录尝试可在取证调查期间提供额外的优势</p>|

## <a id="threat-detection"></a>在 Azure SQL 中启用威胁检测

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | SQL Azure |
| **属性**              | SQL 版本 - V12 |
| **参考**              | [SQL 数据库威胁检测入门](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **步骤** |<p>威胁检测会检测异常的数据库活动，指出数据库有潜在的安全威胁。 威胁检测提供新的安全层，在发生异常活动时提供安全警报，让客户检测潜在威胁并做出响应。</p><p>用户可以使用 Azure SQL 数据库审核来探查可疑事件，判断这些可疑事件是否是因为有人尝试访问、破坏或利用数据库中的数据而生成的。</p><p>不必是安全专家，也不需要管理先进的安全监视系统，就能使用威胁检测轻松解决数据库的潜在威胁。</p>|

## <a id="analytics"></a>使用 Azure 存储分析来审核对 Azure 存储的访问

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 存储 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用 |
| **参考**              | [使用存储分析来监视授权类型](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **步骤** | <p>对于每个存储帐户，可以启用 Azure 存储分析来执行日志记录和存储指标数据。 存储分析日志提供重要的信息，例如，某人在访问存储时使用的身份验证方法。</p><p>如果要严密监视存储的访问，这真的很有用。 例如，在 Blob 存储中，可以将所有容器设置为专用，并通过应用程序实现 SAS 服务的用法。 然后可以定期检查日志，以了解 Blob 是否是使用存储帐户密钥访问的（这可能表示出现安全违规），或者 Blob 是公共的但它们不应该是公共的。</p>|

## <a id="sufficient-logging"></a>实现充分的日志记录

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | .NET framework |
| **属性**              | 不适用  |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[巩固王国](https://vulncat.fortify.com/en/vulncat/index.html) |
| **步骤** | <p>发生安全事件后缺少适当的审核线索可能会给取证工作造成阻碍。 Windows Communication Foundation (WCF) 提供记录成功和/或失败身份验证尝试的功能。</p><p>记录失败的身份验证尝试可以警示管理员发生了潜在的暴力破解攻击。 同样，记录成功身份验证事件可在合法帐户遭到入侵时提供有用的审核线索。 启用 WCF 的服务安全审核功能 |

### <a name="example"></a>示例
下面是启用了审核的示例配置
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>实现充分的审核失败处理

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | .NET framework |
| **属性**              | 不适用  |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[巩固王国](https://vulncat.fortify.com/en/vulncat/index.html) |
| **步骤** | <p>开发的解决方案配置为在无法写入审核日志时不生成异常。 如果 WCF 配置为在无法写入审核日志时不引发异常，则程序将不知道发生了失败，并且无法对关键的安全事件进行审核。</p>|

### <a name="example"></a>示例
以下 WCF 配置文件中的 `<behavior/>` 元素指示在 WCF 无法写入审核日志时，WCF 不要通知应用程序。
````
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
````
请将 WCF 配置为每当无法写入审核日志时，就会通知程序。 应在程序中实施替代的通知方案，告知组织没有维护审核线索。 

## <a id="logging-web-api"></a>确保在 Web API 中强制实施审核与日志记录

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web API | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 在 Web API 中启用审核与日志记录。 审核日志应捕获用户上下文。 识别所有重要事件并记录这些事件。 实现集中式日志记录 |

## <a id="logging-field-gateway"></a>确保在现场网关中强制实施适当的审核与日志记录

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 现场网关 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | <p>当多个设备连接到现场网关时，请确保记录各个设备的连接尝试和身份验证状态（成功或失败），并在现场网关中维护这些信息。</p><p>此外，如果现场网关维护各个设备的 IoT 中心凭据，请确保在检索这些凭据时执行审核。开发一个过程用于定期将日志上传到 Azure IoT 中心/存储供长期保留。</p> |

## <a id="logging-cloud-gateway"></a>确保在云网关中强制实施适当的审核与日志记录

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 云网关 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [IoT 中心操作监视简介](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **步骤** | <p>规划如何通过 IoT 中心操作监视收集和存储审核数据。 启用以下监视类别：</p><ul><li>设备标识操作</li><li>设备到云的通信</li><li>云到设备的通信</li><li>连接</li><li>文件上传</li></ul>|
