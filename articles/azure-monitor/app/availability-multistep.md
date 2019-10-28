---
title: 通过多步骤 web 测试和 Azure 应用程序 Insights 监视 web 应用程序 |Microsoft Docs
description: 设置多步骤 web 测试，以便通过 Azure 应用程序 Insights 监视 web 应用程序
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 80a39151a3d40c9b9d7cb49c6ab41aab602c5991
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817386"
---
# <a name="multi-step-web-tests"></a>多步骤 Web 测试

可以通过多步骤 web 测试来监视记录的 Url 序列和与网站的交互。 本文将指导你完成使用 Visual Studio Enterprise 创建多步骤 web 测试的过程。

> [!NOTE]
> 多步骤 web 测试依赖于 Visual Studio webtest 文件。 已[公布](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)，Visual Studio 2019 将是具有 webtest 功能的最新版本。 必须了解的是，尽管不会添加任何新功能，但目前仍支持 Visual Studio 2019 中的 webtest 功能，并且在产品的支持生命周期内仍将继续提供支持。 Azure Monitor 产品团队已经解决了有关将来的多步骤[可用性测试的](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)问题。  

## <a name="pre-requisites"></a>必备组件

* Visual Studio 2017 企业版或更高版本。
* Visual Studio web 性能和负载测试工具。

查找测试工具必备组件。 启动**Visual Studio 安装程序** > **各个组件**， > **调试和测试** > **Web 性能和负载测试工具**。

![显示单个组件的 Visual Studio 安装程序 UI 的屏幕截图，其中选中了 Web 性能和负载测试工具项旁边的复选框。](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> 多步骤 web 测试有与之相关的额外成本。 若要了解详细信息，请参阅[官方定价指南](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="record-a-multi-step-web-test"></a>记录多步骤 web 测试 

> [!WARNING]
> 我们不再建议使用多步骤记录器。 记录器是为具有基本交互的静态 HTML 页面开发的，不提供新式网页的功能体验。

有关创建 Visual Studio web 测试的指南，请参阅[官方 Visual studio 2019 文档](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019)。

## <a name="upload-the-web-test"></a>上传 web 测试

1. 在 Application Insights 门户中的 "可用性" 窗格上，选择 "**创建测试** > **测试类型** > **多步骤 web 测试**"。

2. 设置测试位置、频率和警报参数。

### <a name="frequency--location"></a>频率 & 位置

|设置| 说明
|----|----|----|
|**测试频率**| 设置从每个测试位置运行测试的频率。 如果有五个测试位置，且默认频率为五分钟，则平均每隔一分钟测试站点一次。|
|**测试位置**| 是指服务器将 web 请求发送到 URL 的位置。 **建议的最小测试位置数为 5** ，以确保可以将网站中的问题与网络问题区分开来。 最多可以选择 16 个位置。

### <a name="success-criteria"></a>成功条件

|设置| 说明
|----|----|----|
| **测试超时** |减少此值可以接收有关响应变慢的警报。 如果未在这段时间内收到站点的响应，则将测试视为失败。 如果选择了“分析依赖请求”，则必须在这段时间内收到所有图像、样式文件、脚本和其他依赖资源。|
| **HTTP 响应** | 视为成功的返回状态代码。 代码 200 指示返回了正常网页。|
| **内容匹配** | 字符串，如 "欢迎！" 我们测试区分大小写的匹配项是否出现在每个响应中。 它必须是不带通配符的纯字符串。 别忘了，如果页面内容更改，可能需要更新。 **内容匹配仅支持英文字符** |

### <a name="alerts"></a>警报

|设置| 说明
|----|----|----|
|**近乎实时（预览）** | 建议使用近乎实时的警报。 创建可用性测试后，配置此类型的警报。  |
|**经典** | 我们不再建议使用经典警报来执行新的可用性测试。|
|**警报位置阈值**|建议最少 3/5 个位置。 警报位置阈值和测试位置数量之间的最佳关系是**警报位置阈值** = **测试位置数-2，最少有5个测试位置。**|

## <a name="configuration"></a>配置

### <a name="plugging-time-and-random-numbers-into-your-test"></a>在测试中插入时间和随机数字

假设要测试的工具从外部源获取与时间相关的数据（例如股票）。 录制 Web 测试时，必须使用具体的时间，但要将它们设置为测试参数：StartTime 和 EndTime。

![我的卓越的股票应用屏幕截图](./media/availability-multistep/app-insights-72webtest-parameters.png)

运行测试时，EndTime 应该始终为当前时间，StartTime 在 15 分钟前。

Web 测试日期时间插件提供处理参数化时间的方式。

1. 针对所需的每个变量参数值添加一个 Web 测试插件。 在 Web 测试工具栏中，选择“添加 Web 测试插件”。
    
    ![添加 Web 测试插件](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    本示例使用两个日期时间插件实例。 一个实例设置为“15 分钟前”，另一个实例设置为“现在”。

2. 打开每个插件的属性。 为插件命名，然后将它设置为使用当前时间。 对于其中一个插件，将“添加分钟”设置为 -15。

    ![上下文参数](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. 在 Web 测试参数中，使用 {{plug-in name}} 来引用插件名称。

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

现在，将测试上传到门户。 它将在每次运行测试时使用动态值。

### <a name="dealing-with-sign-in"></a>处理登录

如果用户登录应用，可以使用许多选项来模拟登录，以便可以在登录后测试页面。 使用的方法取决于应用提供的安全性类型。

在所有情况下，应该只针对测试目的在应用程序中创建帐户。 如果可能，请限制此测试帐户的权限，以便 Web 测试不会影响实际用户。

**简单的用户名和密码**以常规方式记录 web 测试。 先删除 Cookie。

**SAML 身份验证**

|属性名称| 描述|
|----|-----|
| 受众 Uri | SAML 标记的受众 URI。  这是访问控制服务（ACS）的 URI，包括 ACS 命名空间和主机名。 |
| 证书密码 | 将授予对嵌入私钥的访问权限的客户端证书的密码。 |
| 客户端证书  | 具有 Base64 编码格式的私钥的客户端证书值。 |
| 名称标识符 | 标记的名称标识符 |
| 不晚 | 标记有效的时间跨度。  默认值为5分钟。 |
| 不早于 | 在过去创建的标记将有效的时间跨度（用于处理时间偏差）。  默认值为（负）5分钟。 |
| 目标上下文参数名称 | 将接收生成的断言的上下文参数。 |


**客户端密码**如果你的应用有涉及客户端机密的登录路由，请使用该路由。 例如，Azure Active Directory (AAD) 就是提供客户端机密登录的服务。 在 AAD 中，客户端机密是应用密钥。

下面是使用应用密钥的 Azure Web 应用的 Web 测试示例：

![示例屏幕快照](./media/availability-multistep/client-secret.png)

使用客户端机密 (AppKey) 从 AAD 获取令牌。
从响应中提取持有者令牌。
使用授权标头中的持有者令牌调用 API。
确保 web 测试是实际的客户端-也就是说，它在 AAD 中具有自己的应用程序，并使用其 clientId + 应用密钥。 测试中的服务在 AAD 中也有自身的应用：此应用的 appID URI 反映在“resource”字段中的 Web 测试内。

### <a name="open-authentication"></a>开放身份验证
开放身份验证的示例包括使用 Microsoft 或 Google 帐户登录。 许多使用 OAuth 的应用提供替代的客户端机密，因此第一个技巧就是调查这种可能性。

如果测试必须使用 OAuth 登录，则常规方法是：

使用 Fiddler 等工具检查 Web 浏览器、身份验证站点与应用之间的流量。
使用不同的计算机或浏览器或者以较长的间隔执行两次以上的登录（使令牌过期）。
通过比较不同的会话，识别从身份验证站点返回的令牌，然后在登录后将此令牌传递给应用服务器。
使用 Visual Studio 录制 Web 测试。
参数化令牌，设置参数来指定从验证器返回令牌的时间，并在站点查询中使用该参数。 （Visual Studio 会尝试参数化测试，但无法正确参数化令牌。）

## <a name="troubleshooting"></a>故障排除

专用[故障排除文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>后续步骤

* [可用性警报](availability-alerts.md)
* [Url ping web 测试](monitor-web-app-availability.md)
