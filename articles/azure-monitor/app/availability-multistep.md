---
title: 监视多步骤 web 测试的 web 应用程序和 Azure Application Insights |Microsoft Docs
description: 设置多步骤 web 测试，以便监视 web 应用程序使用 Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305083"
---
# <a name="multi-step-web-tests"></a>多步骤 Web 测试

您可以监视 Url 的记录的序列和与通过多步骤 web 测试的网站之间的交互。 本文将引导你完成使用 Visual Studio Enterprise 中创建多步骤 web 测试的过程。

> [!NOTE]
> 多步骤 web 测试有与之关联的其他成本。 若要了解更多咨询[官方的定价指南](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="pre-requisites"></a>先决条件

* Visual Studio 2017 Enterprise 或更高版本。
* Visual Studio web 性能和负载测试工具。

若要查找测试工具先决条件。 启动**Visual Studio 安装程序** > **各个组件** > **调试和测试** >  **Web 性能和负载测试工具**。

![使用 Web 性能和负载测试工具的项旁边的复选框为所选的各个组件的 Visual Studio 安装程序 UI 的屏幕截图](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>记录多步骤 web 测试

要创建多步骤测试，可以使用 Visual Studio Enterprise 来录制方案，然后将录制内容上传到 Application Insights。 Application Insights 按设置的间隔重播该方案，并验证响应。

> [!IMPORTANT]
> * 不能在测试中使用编码的函数或循环。 测试必须完全包含在 .webtest 脚本中。 但是，可以使用标准插件。
> * 仅多步骤 Web 测试支持使用英文字符。 如果在其他语言中使用 Visual Studio，请更新 Web 测试定义文件以转换/排除非英文字符。

使用 Visual Studio Enterprise 录制 Web 会话。

1. 创建 Web 性能和负载测试项目。 **文件** > **新** > **项目** > **Visual C#**   > **测试**

    ![Visual Studio 新项目用户界面](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. 打开`.webtest`文件，并开始录制。

    ![Visual Studio 测试录制用户界面](./media/availability-multistep/open-web-test.png)

3. 单击要将测试以模拟作为记录的一部分的步骤。

    ![浏览器录制用户界面](./media/availability-multistep/record.png)

4. 编辑测试：

    * 添加验证，检查收到的文本和响应代码。
    * 删除任何 uneccesary 交互。 您还可以删除图片的从属请求或添加跟踪站点不是与您需要考虑你的测试成功完成。
    
    请注意，只能编辑测试脚本-可以添加自定义代码或调用其他 web 测试。 不要在测试中插入循环。 可以使用标准 Web 测试插件。

5. 若要验证并确保其工作原理的 Visual Studio 中运行测试。

    Web 测试运行器将打开 Web 浏览器，并重复录制的操作。 请确保所有内容行为与预期相同。

## <a name="upload-the-web-test"></a>上传 web 测试

1. 在 Application Insights 门户中的可用性窗格上选择**创建测试** > **测试类型** > **多步骤 web 测试**。

2. 设置测试位置、 频率和警报参数。

### <a name="frequency--location"></a>频率和位置

|设置| 说明
|----|----|----|
|**测试频率**| 设置从每个测试位置运行测试的频率。 如果有五个测试位置，且默认频率为五分钟，则平均每隔一分钟测试站点一次。|
|**测试位置**| 是从我们的服务器将 web 请求发送到你的 URL 的位置的位置。 **建议的测试位置我们最小数量为 5**以确保，你可以区分问题在你的网站与网络问题。 最多可以选择 16 个位置。

### <a name="success-criteria"></a>成功标准

|设置| 说明
|----|----|----|
| **测试超时** |减少此值可以接收有关响应变慢的警报。 如果未在这段时间内收到站点的响应，则将测试视为失败。 如果选择了“分析依赖请求”，则必须在这段时间内收到所有图像、样式文件、脚本和其他依赖资源  。|
| **HTTP 响应** | 视为成功的返回状态代码。 代码 200 指示返回了正常网页。|
| **内容匹配** | 一个字符串，类似于"欢迎 ！" 我们测试区分大小写的匹配项是否出现在每个响应中。 它必须是不带通配符的纯字符串。 别忘了，如果页面内容更改，可能需要更新。 **只能使用英文字符支持与内容匹配** |

### <a name="alerts"></a>警报

|设置| 说明
|----|----|----|
|**准实时 （预览）** | 我们建议使用准实时警报。 创建可用性测试后，完成配置此类警报。  |
|**经典** | 我们不建议使用经典警报的新的可用性测试。|
|**警报位置阈值**|建议最少 3/5 个位置。 警报位置阈值和测试位置数之间的最佳关系非常**警报位置阈值** = **数最少的五个测试位置测试位置-2。**|

## <a name="advanced-configuration"></a>高级配置

### <a name="plugging-time-and-random-numbers-into-your-test"></a>时间和随机数插入你的测试

假设要测试的工具从外部源获取与时间相关的数据（例如股票）。 录制 Web 测试时，必须使用具体的时间，但要将它们设置为测试参数：StartTime 和 EndTime。

![我很棒的股票应用屏幕截图](./media/availability-multistep/app-insights-72webtest-parameters.png)

运行测试时，EndTime 应该始终为当前时间，StartTime 在 15 分钟前。

Web 测试日期时间插件提供的方式来处理时间参数化。

1. 针对所需的每个变量参数值添加一个 Web 测试插件。 在 Web 测试工具栏中，选择“添加 Web 测试插件”  。
    
    ![添加 Web 测试插件](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    本示例使用两个日期时间插件实例。 一个实例设置为“15 分钟前”，另一个实例设置为“现在”。

2. 打开每个插件的属性。 为插件命名，然后将它设置为使用当前时间。 对于其中一个插件，将“添加分钟”设置为 -15。

    ![上下文参数](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. 在 Web 测试参数中，使用 {{plug-in name}} 来引用插件名称。

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

现在，将测试上传到门户。 在每次运行测试，它将使用动态值。

### <a name="dealing-with-sign-in"></a>处理登录

如果用户登录应用，可以使用许多选项来模拟登录，以便可以在登录后测试页面。 使用的方法取决于应用提供的安全性类型。

在所有情况下，应该只针对测试目的在应用程序中创建帐户。 如果可能，请限制此测试帐户的权限，以便 Web 测试不会影响实际用户。

**简单的用户名和密码**按常规方式录制 web 测试。 先删除 Cookie。

**SAML 身份验证**使用适用于 web 测试的 SAML 插件。 访问通过插件...

**客户端机密**如果应用具有单一登录路由涉及到客户端机密，使用该路由。 例如，Azure Active Directory (AAD) 就是提供客户端机密登录的服务。 在 AAD 中，客户端机密是应用密钥。

下面是使用应用密钥的 Azure Web 应用的 Web 测试示例：

![示例屏幕截图](./media/availability-multistep/client-secret.png)

使用客户端机密 (AppKey) 从 AAD 获取令牌。
从响应中提取持有者令牌。
使用授权标头中的持有者令牌调用 API。
请确保 web 测试是实际的客户端-即，它在 AAD-中具有自己的应用并使用其 clientId 和应用密钥。 测试中的服务在 AAD 中也有自身的应用：此应用的 appID URI 反映在“resource”字段中的 Web 测试内。

### <a name="open-authentication"></a>开放身份验证
开放身份验证的示例包括使用 Microsoft 或 Google 帐户登录。 许多使用 OAuth 的应用提供替代的客户端机密，因此第一个技巧就是调查这种可能性。

如果测试必须使用 OAuth 登录，则常规方法是：

使用 Fiddler 等工具检查 Web 浏览器、身份验证站点与应用之间的流量。
使用不同的计算机或浏览器或者以较长的间隔执行两次以上的登录（使令牌过期）。
通过比较不同的会话，识别从身份验证站点返回的令牌，然后在登录后将此令牌传递给应用服务器。
使用 Visual Studio 录制 Web 测试。
参数化令牌，设置参数来指定从验证器返回令牌的时间，并在站点查询中使用该参数。 （Visual Studio 会尝试参数化测试，但无法正确参数化令牌。）

## <a name="troubleshooting"></a>故障排除

专用[疑难解答文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>后续步骤

* [可用性警报](availability-alerts.md)
* [Url ping web 测试](monitor-web-app-availability.md)
