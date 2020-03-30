---
title: 部署 Windows 虚拟桌面的诊断工具 - Azure
description: 如何为 Windows 虚拟桌面部署诊断 UX 工具。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123411"
---
# <a name="deploy-the-diagnostics-tool"></a>部署诊断工具

>[!IMPORTANT]
>截至 2020 年 3 月 16 日，我们暂时禁用了由于服务需求增加而影响用户体验的诊断查询。 这将导致该工具停止工作，因为它依赖于这些查询来运行。 当诊断查询再次可用时，我们将更新本文。
>
>在此之前，我们强烈建议您[使用日志分析](diagnostics-log-analytics.md)进行持续监控。

以下是 Windows 虚拟桌面的诊断工具可以为您执行以下操作：

- 在一周内查找单个用户的诊断活动（管理、连接或源）。
- 从日志分析工作区收集连接活动的会话主机信息。
- 查看特定主机的虚拟机 （VM） 性能详细信息。
- 查看哪些用户登录到会话主机。
- 向特定会话主机上的活动用户发送消息。
- 将用户从会话主机注销。

## <a name="prerequisites"></a>先决条件

您需要创建 Azure 活动目录应用注册和日志分析工作区，然后才能为该工具部署 Azure 资源管理器模板。 您或管理员需要这些权限才能执行此操作：

- Azure 订阅的所有者
- 在 Azure 订阅中创建资源的权限
- 创建 Azure AD 应用的权限
- RDS 所有者或参与者权限

在开始之前，您还需要安装这两个 PowerShell 模块：

- [Azure PowerShell 模块](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Azure AD 模块](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

请确保在登录时已准备好订阅 ID。

按顺序排列所有内容后，可以创建 Azure AD 应用注册。

## <a name="create-an-azure-active-directory-app-registration"></a>创建 Azure 活动目录应用注册

本节将介绍如何使用 PowerShell 使用服务主体创建 Azure 活动目录应用并获取其 API 权限。

>[!NOTE]
>API 权限是 Windows 虚拟桌面、日志分析和 Microsoft 图形 API 权限添加到 Azure 活动目录应用程序。

1. 以管理员身份打开 PowerShell。
2. 使用具有要用于诊断工具的 Azure 订阅的所有者或参与者权限的帐户登录到 Azure：
   ```powershell
   Login-AzAccount
   ```
3. 使用相同的帐户登录到 Azure AD：
   ```powershell
   Connect-AzureAD
   ```
4. 转到[RDS 模板 GitHub 存储库](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)，并在 PowerShell 中运行**CreateAdApp 注册诊断.ps1**脚本。
5.  当脚本要求您命名应用时，请输入唯一的应用名称。


脚本成功运行后，应在其输出中显示以下内容：

-  确认应用的消息现在具有服务主体角色分配。
-  部署诊断工具时需要的客户端 ID 和客户端密钥。

现在，您已经注册了应用，是时候配置日志分析工作区了。

## <a name="configure-your-log-analytics-workspace"></a>配置日志分析工作区

为了获得最佳体验，我们建议您使用以下性能计数器配置日志分析工作区，这些计数器允许您在远程会话中派生用户体验的语句。 有关建议具有建议的阈值的计数器的列表，请参阅[Windows 性能计数器阈值](deploy-diagnostics.md#windows-performance-counter-thresholds)。

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>使用 PowerShell 创建 Azure 日志分析工作区

您可以运行 PowerShell 脚本来创建日志分析工作区，并配置建议的 Windows 性能计数器以监视用户体验和应用性能。

>[!NOTE]
>如果已有一个现有日志分析工作区，您没有要使用的 PowerShell 脚本，请先跳一跳以验证[Azure 门户中的结果脚本](#validate-the-script-results-in-the-azure-portal)。

要运行 PowerShell 脚本，

1.  以管理员身份打开 PowerShell。
2.  转到[RDS 模板 GitHub 存储库](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)，并在 PowerShell 中运行**CreateLogAnalyticsFor诊断.ps1**脚本。
3. 输入以下参数值：

    - 对于**资源组名称**，请输入资源组的名称。
    - 对于**日志分析工作区名称**，请为日志分析工作区输入唯一名称。
    - 对于**位置**，输入正在使用的 Azure 区域。
    - 输入**Azure 订阅 ID**，您可以在 **"订阅**"下的 Azure 门户中找到该 ID。

4. 输入具有委派管理员访问权限的用户的凭据。
5. 使用同一用户的凭据登录到 Azure 门户。
6. 记下或记住日志分析工作区 ID，以便以后使用。
7. 如果使用 PowerShell 脚本设置日志分析工作区，则应已配置性能计数器，并且可以跳过以[在 Azure 门户中验证脚本结果](#validate-the-script-results-in-the-azure-portal)。 否则，请继续下一节。

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>在现有日志分析工作区中配置 Windows 性能计数器

本节适用于希望使用上一节中没有 PowerShell 脚本创建的现有 Azure 日志分析工作区的用户。 如果您尚未使用该脚本，则必须手动配置建议的 Windows 性能计数器。

以下是手动配置推荐的性能计数器：

1. 打开 Internet 浏览器并使用管理帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 接下来，转到**日志分析工作区**，查看配置的 Windows 性能计数器。
3. 在 **"设置"** 部分中，选择 **"高级设置**"。
4. 之后，导航到**数据** > **Windows 性能计数器**并添加以下计数器：

    -   逻辑磁盘（\*\\） %可用空间
    -   逻辑磁盘（C：）\\平均磁盘队列长度
    -   内存（\*\\） 可用 MB
    -   处理器信息（\*\\） 处理器时间
    -   每个会话的用户输入延迟（\*\\） 最大输入延迟

详细了解 Azure 监视器 中的[Windows 和 Linux 性能数据源](/azure/azure-monitor/platform/data-sources-performance-counters)的性能计数器。

>[!NOTE]
>您配置的任何其他计数器不会显示在诊断工具本身中。 要使其显示在诊断工具中，您需要配置工具的配置文件。 有关如何使用高级管理执行此操作的说明将在以后在 GitHub 中提供。

## <a name="validate-the-script-results-in-the-azure-portal"></a>在 Azure 门户中验证脚本结果

在继续部署诊断工具之前，我们建议您验证 Azure 活动目录应用程序是否具有 API 权限，并且日志分析工作区具有预配置的 Windows 性能计数器。

### <a name="review-your-app-registration"></a>查看应用注册

要确保应用注册具有 API 权限，请进行：

1. 打开浏览器并使用管理帐户连接到[Azure 门户](https://portal.azure.com/)。
2. 转到 Azure Active Directory****。
3. 转到**应用程序注册**并选择 **"所有应用程序**"。
4. 查找 Azure AD 应用注册，其应用名称与[创建 Azure 活动目录应用注册](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)的步骤 5 中输入的应用名称相同。

### <a name="review-your-log-analytics-workspace"></a>查看日志分析工作区

要确保日志分析工作区具有预配置的 Windows 性能计数器，请执行以下操作：

1. 在[Azure 门户](https://portal.azure.com/)中，转到**日志分析工作区**以查看配置的 Windows 性能计数器。
2. 在 **"设置"** 下，选择 **"高级设置**"。
3. 之后，转到**数据** > **Windows 性能计数器**。
4. 确保预先配置了以下计数器：

   - 逻辑磁盘（\*\\） %可用空间：以百分比形式显示磁盘上总可用空间的可用空间量。
   - 逻辑磁盘（C：）\\平均磁盘队列长度：C 驱动器的磁盘传输请求的长度。 该值不应超过 2，超过较短的时间。
   - 内存（\*\\） 可用 MB：系统的可用内存（以兆字节为单位）。
   - 处理器信息（\*\\） 处理器时间：处理器执行非空闲线程所花费的已用时间的百分比。
   - 每个会话的用户输入延迟（\*\\） 最大输入延迟

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>连接到日志分析工作区中的 VM

为了能够查看 VM 的运行状况，您需要启用日志分析连接。 按照以下步骤连接 VM：

1. 打开浏览器并使用管理帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 转到日志分析工作区。
3. 在左侧面板中，在工作区数据源下，选择**虚拟机**。
4. 选择要连接到的 VM 的名称。
5. 选择“连接”****。

## <a name="deploy-the-diagnostics-tool"></a>部署诊断工具

要为诊断工具部署 Azure 资源管理模板，请将模板部署：

1.  转到 [GitHub Azure RDS 模板页面](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy)。
2.  将模板部署到 Azure 并按照模板中的说明进行操作。 请确保您有以下信息可用：

    -   客户端 Id
    -   客户端-机密
    -   Log Analytics 工作区 ID

3.  提供输入参数后，接受条款和条件，然后选择 **"购买**"。

部署需要 2-3 分钟。 成功部署后，转到资源组并确保 Web 应用和应用服务计划资源在那里。

之后，您需要设置重定向 URI。

### <a name="set-the-redirect-uri"></a>设置重定向 URI

要设置重定向 URI：

1.  在[Azure 门户](https://portal.azure.com/)中，转到**应用服务**并找到您创建的应用程序。
2.  转到概述页面并复制您在那里找到的 URL。
3.  导航到**应用注册**并选择要部署的应用。
4.  在左侧面板中，在"管理"部分下，选择 **"身份验证**"。
5.  将所需的重定向 URI 输入重定向 URI 到 **"重定向 URI"** 文本框中，然后在菜单的左上角选择 **"保存**"。
6. 在"类型"下的下拉菜单中选择**Web。**
7. 从应用概述页输入 URL，并在其末尾添加 **/安全/登录回调**。 例如：`https://<yourappname>.azurewebsites.net/security/signin-callback`。

   ![重定向 URI 页面](media/redirect-uri-page.png)

8. 现在，转到 Azure 资源，选择具有模板中提供的名称的 Azure 应用服务资源，然后导航到与其关联的 URL。 （例如，如果您在模板中使用的应用名称为`contosoapp45`，则关联的 URL 为<https://contosoapp45.azurewebsites.net>。
9. 使用相应的 Azure Active Directory 用户帐户登录。
10.   选择“接受”****。

## <a name="distribute-the-diagnostics-tool"></a>分发诊断工具

在使诊断工具可供用户使用之前，请确保他们具有以下权限：

- 用户需要读取访问权限进行日志分析。 有关详细信息，请参阅使用[Azure 监视器 开始使用角色、权限和安全性](/azure/azure-monitor/platform/roles-permissions-security)。
-  用户还需要 Windows 虚拟桌面租户（RDS 读取器角色）的读取访问权限。 有关详细信息，请参阅[Windows 虚拟桌面中的委派访问](delegated-access-virtual-desktop.md)。

您还需要为用户提供以下信息：

- 应用的 URL
- 租户组他们可以访问的单个租户的名称。

## <a name="use-the-diagnostics-tool"></a>使用诊断工具

使用从组织收到的信息登录到您的帐户后，准备好 UPN 以适合您的用户查询活动。 搜索将为您提供上周内发生的指定活动类型下的所有活动。

### <a name="how-to-read-activity-search-results"></a>如何阅读活动搜索结果

活动按时间戳排序，首先使用最新活动。 如果结果返回错误，请先检查是否为服务错误。 对于服务错误，创建包含活动信息的支持票证，以帮助我们调试问题。 所有其他错误类型通常可以通过用户或管理员来解决。 有关最常见的错误方案的列表以及如何解决这些问题，请参阅[识别和诊断问题](diagnostics-role-service.md#common-error-scenarios)。

>[!NOTE]
>在链接的文档中，服务错误称为"外部错误"。 当我们更新 PowerShell 引用时，这将更改。

连接活动可能有多个错误。 您可以展开活动类型以查看用户遇到的任何其他错误。 选择错误代码的名称以打开对话框以查看有关它的详细信息。

### <a name="investigate-the-session-host"></a>调查会话主机 

在搜索结果中，查找并选择您想要的信息的会话主机。

您可以分析会话主机运行状况：

- 根据预定义的阈值，您可以检索日志分析查询的会话主机运行状况信息。
- 如果没有活动或会话主机未连接到日志分析，则信息将不可用。

您还可以与会话主机上的用户进行交互：

- 您可以注销或向已签名的用户发送消息。
- 默认情况下，您最初搜索的用户是被选中的，但您也可以选择其他用户来一次发送消息或注销多个用户。

### <a name="windows-performance-counter-thresholds"></a>Windows 性能计数器阈值

- 逻辑磁盘（\*\\） %可用空间：

    - 显示可用逻辑磁盘上总可用空间的百分比。
    - 阈值：小于 20% 被标记为不正常。

- 逻辑磁盘（C：）\\平均磁盘队列长度：

    - 表示存储系统条件。
    - 阈值：高于 5 标记为不正常。

- 内存（\*\\） 可用 MB：

    - 系统的可用内存。
    - 阈值：小于 500 MB 标记为不正常。

- 处理器信息（\*\\） 处理器时间：

    - 阈值：高于 80% 被标记为不正常。

- [每个会话的用户输入延迟\*（\\） 最大输入延迟](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)：

    - 阈值：高于 2000 毫秒被标记为不正常。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用日志分析在使用诊断](diagnostics-log-analytics.md)中监视活动日志。
- 阅读常见错误方案以及如何在[识别和诊断问题](diagnostics-role-service.md)时修复它们。
