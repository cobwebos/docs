---
title: 故障排除
description: 如何对 Azure FarmBeats 进行故障排除。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ad1cb3b08f92923ef45b48d79ad8bbdc3277d370
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131975"
---
# <a name="troubleshooting"></a>故障排除

以下部分介绍常见的 Azure FarmBeats 问题以及如何解决这些问题。

若要获得更多帮助，请在 farmbeatssupport@microsoft.com向我们写入，在此电子邮件中包含文件。

 使用以下步骤下载 "部署日志文件"：

1. 突出显示的图标，并从下拉选项中选择 "**下载**" 选项。

    ![项目场节拍](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. 在下一个屏幕上，输入你的部署日志文件的路径。 例如，farmbeats-deployer。

## <a name="sensor-telemetry"></a>传感器遥测

### <a name="telemetry-not-seen"></a>未检测到遥测

**症状**：已部署设备/传感器，并已将 FarmBeats 与设备伙伴链接;但无法在 FarmBeats 上获取/查看遥测数据。

**纠正操作**：访问 Azure 门户并执行以下步骤：

1. 中转到你的 FarmBeats 数据中心资源组。   
2. 选择**事件中心**（DatafeedEventHubNamespace） 并检查传入消息数。   
3. 如果没有**传入消息**，请联系你的设备伙伴。  
4. 如果有**传入消息**，请联系 farmbeatssupport@microsoft.com 数据中心和加速器日志以及捕获的遥测数据。

请参阅文档中的 "[日志" 部分](#collect-logs-manually)，了解如何下载日志。    

### <a name="device-appears-offline"></a>设备显示脱机

**症状**：设备已安装，并且你已将 FarmBeats 链接到设备伙伴。 设备处于联机状态并发送遥测数据，但它们显示为脱机状态。

**纠正操作**：没有为此设备配置报告间隔。 请与设备制造商联系以设置报告间隔。 

### <a name="error-deleting-a-resource"></a>删除资源时出错

以下是删除设备时的常见错误情况：  

**消息**：在传感器中引用了设备：有一个或多个传感器与设备相关联。 删除传感器，然后删除设备。  

**含义**：设备与在场中部署的多个传感器相关联。   

**纠正操作**：  

1. 通过加速器删除与设备关联的传感器。  
2. 如果要将传感器关联到不同设备，请要求设备伙伴执行相同操作。  
3. 使用删除 API 调用删除设备，并将 force 参数设置为 "true"。  

**消息**：设备在设备中被引用为 ParentDeviceId：有一个或多个设备作为子设备与此设备关联。 删除它们，然后删除此设备。  

**意思**：你的设备具有与之关联的其他设备  

**纠正操作**

1. 删除与特定设备关联的设备  
2. 删除特定设备  

    > [!NOTE]
    > 如果传感器与设备相关联，则无法删除该设备。 有关如何删除关联的传感器的详细信息，请参阅 "获取传感器数据" 一章中的 "[删除传感器](get-sensor-data-from-sensor-partner.md)"。


## <a name="issues-with-jobs"></a>作业问题

### <a name="farmbeats-internal-error"></a>FarmBeats 内部错误

**消息**： FarmBeats 内部错误，有关详细信息，请参阅故障排除指南。

**纠正操作**：这可能是由于数据管道中的暂时性故障导致的。 再次创建作业。 如果错误仍然存在，请在 FarmBeats 论坛上的文章中添加该错误，或联系 FarmBeatsSupport@microsoft.com。

## <a name="accelerator-troubleshooting"></a>加速器疑难解答

### <a name="access-control"></a>Access Control

**添加角色分配时出错**

**消息**：找不到匹配的用户

**纠正操作**：检查您尝试执行角色分配的电子邮件 ID。 电子邮件 ID 必须与在 Active Directory 中为该用户注册的电子邮件 ID 完全匹配。 如果错误仍然存在，请在 FarmBeats 论坛或联系 FarmBeatsSupport@microsoft.com 上的文章中添加错误。

### <a name="unable-to-log-in-to-accelerator"></a>无法登录到加速器

**消息**：错误：你无权调用服务。 请与管理员联系以获取授权。

**纠正操作**：请求管理员授权你访问 FarmBeats 部署。 为此，可通过在快捷键的 "设置" 窗格中执行 RoleAssignment Api 或访问控制来完成。  

如果已添加并遇到此错误，请刷新页面以重试。  如果错误仍然存在，请在 FarmBeats 论坛上的文章中添加该错误，或联系 FarmBeatsSupport@microsoft.com。

![项目场节拍](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>加速器-发生未知错误  

**消息**：错误：发生未知错误

**纠正操作**：如果使页处于空闲状态的时间太长，则会发生这种情况。 刷新页面。  

如果错误仍然存在，请在 FarmBeats 论坛或联系 FarmBeatsSupport@microsoft.com 上的文章中添加错误。

**即使在升级 FarmBeatsDeployment 之后，FarmBeats 加速器也不显示最新版本。**

**纠正操作**：这是由于浏览器中的服务辅助角色持久性导致的。
关闭所有已打开该加速器的浏览器选项卡，然后关闭浏览器窗口。 启动浏览器的新实例，并再次加载加速器 URI。 这会加载新版本的加速器。

## <a name="sentinel-imagery-related-issues"></a>Sentinel 图像相关问题

### <a name="sentinel-wrong-username-or-password"></a>Sentinel 错误的用户名或密码

**作业失败消息**;需要完全身份验证才能访问此资源。

**更正操作**：重新运行安装程序以将数据中心用于正确的用户名和密码。

**纠正操作**：重新运行失败的作业或运行5-7 天日期范围的卫星索引作业，并检查作业是否成功。

### <a name="sentinel-hub-wrongurlor-not-accessible"></a>Sentinel 中心错误 URL 或无法访问 

**作业失败消息**：抱歉，出现了问题。 您尝试访问的页面暂时不可用。 

**纠正操作**：
1.  在浏览器中打开 Sentinel URL （ https://scihub.copernicus.eu/dhus/)，并检查是否可以访问网站。 
2.  如果无法访问网站，请检查是否有任何防火墙/公司网络等。正在阻止网站并执行必要的步骤以允许上述 URL。 
3.  请重新运行失败的作业，或在5-7 天的日期范围内运行附属索引作业，并检查作业是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>用于维护的 Sentinel 服务器关闭

**作业失败消息**： Copernicus 开放式访问中心即将恢复！ 抱歉，我们目前正在进行维护。 稍后我们将重新联机！ 

**纠正操作**：

1.  如果在 Sentinel 服务器上执行了任何维护活动，则会出现此问题。 
2.  如果任何作业/管道因上述原因而失败，请在一段时间后重新提交作业。 
3.  用户可以访问 https://scihub.copernicus.eu/news/ 来检查有关任何计划内/计划内 Sentinel 维护活动的信息。  
4.  请重新运行失败的作业，或在5-7 天的日期范围内运行附属索引作业，并检查作业是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel 达到的最大连接数

**作业失败消息**：用户 "<username>" 实现的两个并发流的最大数目 

**纠正操作**
1.  如果任何作业因上述原因而失败，则在另一个部署/软件中使用相同的 sentinel 帐户。 
2.  用户可以创建新的 sentinel 帐户，并重新运行安装程序，以便通过新的 sentinel 用户名和密码升级数据中心。  
3.  请重新运行失败的作业或运行5-7 天日期范围的卫星索引作业，并检查作业是否成功。

### <a name="sentinel-server-refused-connection"></a>Sentinel 服务器拒绝连接 

**作业失败消息**：

服务器拒绝连接： http://172.30.175.69:8983/solr/dhus 

**纠正操作**：如果在 Sentinel 服务器上执行任何维护活动，则会出现此问题。 
1.  如果任何作业/管道因上述原因而失败，请在一段时间后重新提交作业。 
2.  用户可以访问 https://scihub.copernicus.eu/news/ 来检查有关任何计划内/计划内 Sentinel 维护活动的信息。  
3.  请重新运行失败的作业或运行5-7 天日期范围的卫星索引作业，并检查作业是否成功。


## <a name="collect-logs-manually"></a>手动收集日志

[安装并部署]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)到 Azure 存储资源管理器。

### <a name="how-to-collect-data-hub-adf-job-logs"></a>如何收集数据中心 ADF 作业日志
1. 登录到 https://portal.azure.com
2. 在 "**搜索**" 文本框中，搜索 "FarmBeats 数据中心资源组"。

    > [!NOTE]
    > 选择 FarmBeats 部署时指定的数据中心资源组。

在资源组仪表板中，搜索（datahublogs）存储帐户。 例如，datahublogsmvxmq  

1.  在 "**名称**" 列中选择存储帐户，查看**存储帐户**仪表板。
2.  在（datahubblogs）页中，选择 "**在资源管理器中打开**" 以查看**打开 Azure 存储资源管理器**应用程序。
3.  在左面板中，选择 "（datahubblogs ...）" **、"** **作业日志**"。
4.  在 "**作业日志**" 选项卡中，选择 "**下载**"。
5.  选择要将日志下载到计算机上的本地文件夹的位置。
6.  将下载的 zip 文件通过电子邮件发送到 farmbeatssupport@microsoft.com

    ![项目场节拍](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>如何收集加速器 ADF 作业日志

1.  登录到 https://portal.azure.com
2.  在 "**搜索**" 文本框中，搜索 "FarmBeats 加速器资源组"。

    > [!NOTE]
    > 选择在部署 FarmBeats 时指定的加速器资源组。

3.  在资源组仪表板中，搜索 "存储 ..."。 子域名称。 例如，storagedop4k
4.  在 "**名称**" 列中选择存储帐户，查看存储帐户仪表板。
5.  在（"存储 ..."）页中，选择 "**在资源管理器中打开**" 以查看打开的 Azure 存储资源管理器应用程序。
6.  在左侧面板中，< "存储 ..."）、" **Blob 容器**"，选择 "**作业日志**"。
7.  在 "**作业日志**" 选项卡中，选择 "**下载**"。
8.  选择要将日志下载到计算机上的本地文件夹的位置。
9.  将下载的 zip 文件通过电子邮件发送到 farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>如何收集数据中心应用服务日志

1.  登录到 https://portal.azure.com
2.  在 "**搜索**" 文本框中，搜索 "FarmBeats 数据中心资源组"。

    > [!NOTE]
    > 选择 FarmBeats 部署时指定的数据中心资源组。

3.  在资源组中，搜索（datahublogs）存储帐户。 例如，datahublogsmvxmq
4.  在 "**名称**" 列中选择存储帐户，查看**存储帐户**仪表板。
5.  在（datahubblogs）页中，选择 "**在资源管理器中打开**" 以查看**打开 Azure 存储资源管理器**应用程序。
6.  在左侧面板中，选择 "datahubblogs **"，然后**选择 "appinsights"。
7.  在 "appinsights" 选项卡中，选择 "**下载**"。
8.  选择用于将日志下载到计算机上的本地文件夹的路径。
9.  通过电子邮件发送下载的文件夹到 farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>如何收集加速器应用服务日志

1.  登录到 https://portal.azure.com
2.  在**搜索**中，搜索 "FarmBeats 加速器资源组"。

    > [!NOTE]
    > 选择在 FarmBeats 部署时提供的 Farmbeats 加速器资源组。

3.  在资源组中，搜索（存储 ...）存储帐户。 例如，storagedop4k
4.  在 "**名称**" 列中选择存储帐户，查看**存储帐户**仪表板。
5.  在（"存储 ..."）页中，选择 "**在资源管理器中打开**" 以查看**打开的 Azure 存储资源管理器**应用程序。
6.  在左面板（"存储 ..."）、" **Blob 容器**" 中，选择 "appinsights"。
7.  在 "appinsights" 选项卡中，选择 "**下载**"。
8.  选择要将日志下载到计算机上的本地文件夹的位置。
9.  通过电子邮件发送下载的文件夹到 farmbeatssupport@microsoft.com

## <a name="known-issues"></a>已知问题

## <a name="batch-related-issues"></a>与批处理相关的问题

**错误**：已达到指定订阅的 Batch 帐户的区域帐户配额。

**纠正操作**：增加配额或删除未使用的批处理帐户，然后重新运行部署。

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory 相关问题

**错误**：无法将所需设置更新为 Azure AD 应用 d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0：权限不足，无法完成该操作。 确保为 Azure AD 应用正确配置了上述设置。

**意思**： Azure AD 应用注册配置未正确进行。  

**纠正操作**：向 IT 管理员（具有租户读取访问权限）咨询用于创建 Azure AD 应用注册的[脚本](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts)。 此脚本还会自动执行配置步骤。

**错误**：无法在此租户中创建新的 Active Directory 应用程序 "dummyname"：已存在另一个具有相同的属性标识符 uri 值的对象

**意思**：已存在具有相同名称 Azure AD 应用注册。

**纠正操作**：删除现有 Azure AD 应用注册或重新使用它进行安装。 如果要重复使用现有 Azure AD 将应用程序 ID 和客户端密码传递到安装程序并重新部署。

## <a name="inputjson-related-issues"></a>与 json 相关的问题

从输入 json 文件读取输入**时出错**

**纠正操作**：此问题主要是由于为安装程序指定了正确的输入 json 路径或名称而导致的。 做出适当的更正，然后重试部署。

**分析 json 输入时出错**

**纠正操作**：此问题主要是由于输入 json 文件中的值不正确引起的。 做出适当的更正，然后重试部署。

## <a name="high-cpu-usage"></a>CPU 使用率较高

**错误**：收到有关 CPU 使用率高的电子邮件警报。 

**纠正操作**： 
1.  中转到你的 FarmBeats 数据中心资源组。
2.  选择应用服务。  
3.  请参阅 "扩展（应用服务计划）"，并选择适当的[定价层](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="next-steps"></a>后续步骤

如果仍然遇到任何问题，请在我们的[支持论坛](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)上联系我们。
