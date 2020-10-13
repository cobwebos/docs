---
title: 在合作伙伴中心获取对商业 marketplace 门户的支持
description: 了解合作伙伴中心的支持选项，包括如何提供支持请求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/18/2020
ms.openlocfilehash: d69001922520fc7162071b4828492bb1d9f80d0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329879"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>支持合作伙伴中心的商业市场计划

Microsoft 支持各种产品和服务。 找到正确的支持团队对于确保适当且及时的响应十分重要。 请考虑使用以下有助于将查询路由到相应团队的方案：

- 如果你是发布者，并有来自客户的问题，请让你的客户使用 [Azure 门户](https://portal.azure.com/)中的支持链接请求支持。

- 如果你是发布者，并且有与你的应用或服务相关的问题，请查看以下支持选项。

## <a name="support-options-for-publishers"></a>发布服务器的支持选项

1. 使用工作帐户登录到 [合作伙伴中心的 "商用 Marketplace" 计划](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) 。  (如果尚未执行此操作，则需要 [创建合作伙伴中心帐户](./create-account.md)。 ) 

2. 在页面右侧的上部菜单中，选择 " **支持** " 图标。
 
3. " **帮助和支持** " 窗格将显示在页面的右侧。
 
   ![支持下拉菜单](./media/support/commercial-marketplace-support-pane.png)

    或中转到 **主页** 窗格，并选择 " **帮助和支持**"。

   ![主页帮助和支持](./media/support/homepage-help-support.png)

4. 选择 **[文档](../index.yml)** 以查看问题和资源的综合答案。

5. 选择 " **[Marketplace 合作伙伴社区论坛](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/bd-p/2222)** "，通过利用其他 Microsoft 发布者的知识来回答您的问题。

6. 选择 **[其他帮助](https://aka.ms/marketplacepublishersupport)** 以打开 **新的支持请求** 票证。  

## <a name="how-to-open-a-support-ticket"></a>如何开具支持票证

现在，可以在 " **帮助和支持** " 屏幕上打开支持票证。

![帮助和支持](./media/support/help-and-support.png)

>[!Note]
>如果你登录到合作伙伴中心，你将获得更好的支持体验。

**选项1：** 输入如下关键字： Marketplace、Azure 应用、SaaS 产品/服务、帐户管理、潜在客户管理、部署问题、付出，等等。

**选项2：** 浏览主题-> 选择 **类别** = 商业市场-> 选择相应的 **主题** ，然后选择 " **副标题**"。

找到所选主题后，请选择 " **查看解决方案**"。

![后续步骤](./media/support/next-step.png)

以下选项将变为可用：

* 若要选择其他主题，请在 " **所选问题**" 下选择其他主题链接。
* 查看此问题的说明（如果有）。  这是上面所示的 **建议步骤**。
* 查看 **建议的步骤**（如果有）。
* 查看 **建议的文档**（如果有）。

![建议的解决方案](./media/support/recommended-solutions.png)

如果在 **建议解决方案**中找不到答案，请单击 " **提供问题详细信息**"。  填写窗体和所有必填字段，有助于加快解析过程。  然后单击 " **提交**"。

>[!Note]
>如果你尚未登录到合作伙伴中心，并且该主题要求身份验证，则会要求你登录，然后才能继续。  对于公共主题，无需进行身份验证。

## <a name="track-your-existing-support-requests"></a>跟踪你的现有支持请求

若要查看所有打开和关闭的票证，请在左侧导航栏中转到 " **商用 Marketplace** "，然后选择 " **支持**"。

## <a name="record-issue-details-with-a-har-file"></a>使用 HAR 文件记录问题详细信息

若要帮助支持代理解决你的问题，请考虑将 HTTP 存档格式 (HAR) 文件附加到你的支持票证。 HAR 文件是 web 浏览器中的网络请求日志。

> [!WARNING]
> HAR 文件可能会记录有关合作伙伴中心帐户的敏感数据。

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge 和 Google Chrome

使用 **Microsoft Edge** 或 **GOOGLE Chrome**生成 HAR 文件：

1. 请在遇到此问题的网页上进行。
2. 在窗口的右上角，选择省略号图标，然后选择 "**更多工具**" "  >  **开发人员工具**"。 可以按 F12 键作为快捷方式。
3. 在 "开发人员工具" 窗格中，选择 " **网络** " 选项卡。
4. 选择 " **停止记录网络日志** "，并 **清除** 以删除现有日志。 记录图标将变为灰色。

    ![如何删除 Microsoft Edge 或 Google Chrome 中的现有日志](media/support/chromium-stop-clear-session.png)

5. 选择 " **记录网络日志** " 开始记录。 开始记录时，记录图标将变为红色。
 
    ![如何在 Microsoft Edge 或 Google Chrome 中开始录制](media/support/chromium-start-session.png)

6. 再现要解决的问题。
7. 重现问题后，选择 " **停止记录网络日志**"。
8. 选择 " **导出 HAR**"，用向下箭头图标标记，然后保存文件。

    ![如何导出 Microsoft Edge 或 Google Chrome 中的 HAR 文件](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

使用 **Mozilla Firefox**生成 HAR 文件：

1. 请在遇到此问题的网页上进行。
1. 在窗口的右上角，选择省略号图标，然后选择 " **Web 开发人员**  >  **切换工具**"。 可以按 F12 键作为快捷方式。
1. 选择 " **网络** " 选项卡，然后选择 " **清除** " 以删除现有日志。
 
    ![如何删除 Mozilla Firefox 中的现有日志](media/support/firefox-clear-session.png)
 
1. 再现要解决的问题。
1. 重现问题后，选择 " **HAR 导出/导入**" "  >  **全部保存**"。
 
    ![如何导出 Mozilla Firefox 中的 HAR 文件](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

使用 **Safari**生成 HAR 文件：

1. 在 safari 中启用开发人员工具：选择**safari**  >  **首选项**。 中转到 " **高级** " 选项卡，然后选择 **菜单栏中的 "显示开发" 菜单**。
1. 请在遇到此问题的网页上进行。
1. 依次选择“Develop”（开发）、“Show Web Inspector”（显示 Web 检查器）。 
1. 选择 " **网络** " 选项卡，然后选择 " **清除网络项** " 以删除现有日志。
 
    ![如何在 Safari 中删除现有日志](media/support/safari-clear-session.png)
 
1. 再现要解决的问题。
1. 重现问题后，选择 " **导出** " 并保存文件。
 
    ![如何在 Safari 中导出 HAR 文件](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
