---
title: 排查 Azure 数据工厂 UX 问题
description: 了解如何排查 Azure 数据工厂 UX 问题。
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9f23155df6d9e63448b35974c331bf78c3e5f90c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426210"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>排查 Azure 数据工厂 UX 问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了排查 Azure 数据工厂 UX 问题的常见方法。

## <a name="adf-ux-not-loading"></a>ADF UX 未加载

> [!NOTE]
> Azure 数据工厂 UX 官方支持 Microsoft Edge 和 Google Chrome。 使用其他 Web 浏览器可能会导致意外行为或未记录的行为。

### <a name="third-party-cookies-blocked"></a>已阻止第三方 Cookie

ADF UX 使用浏览器 Cookie 来保留用户会话并启用交互式开发和监视体验。 你的浏览器可能会阻止第三方 Cookie，因为你正在使用匿名会话或启用了广告阻止程序。 阻止第三方 Cookie 可能会导致加载门户时出现问题，例如被重定向到空白页 https://adf.azure.com/accesstoken.html ，或收到警告消息，指出已阻止第三方 Cookie。 若要解决此问题，请使用以下步骤在浏览器上启用第三方 Cookie 选项：

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>允许所有 Cookie

1. 在浏览器中访问“chrome://settings/cookies”。
1. 选择“允许所有 Cookie”选项 

    ![在 Chrome 中允许所有 Cookie](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. 刷新 ADF UX，然后重试。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>仅允许 ADF UX 使用 Cookie
如果你不想要允许所有 Cookie，则可以选择只允许 ADF UX：
1. 访问“chrome://settings/cookies”。
1. 选择“始终可以使用 Cookie 的站点”选项下的“添加”  

    ![将 ADF UX 添加到 Chrome 中允许的站点](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. 添加 **adf.azure.com** 站点，选中 " **所有 cookie** " 选项，然后选择 "保存"。 

    ![允许来自 ADF UX 站点的所有 Cookie](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. 刷新 ADF UX，然后重试。

### <a name="microsoft-edge"></a>Microsoft Edge

1. 在浏览器中访问“edge://settings/content/cookies”。
1. 确保启用了“允许站点保存和读取 Cookie 数据”，并禁用了“阻止第三方 Cookie”选项  

    ![允许 Edge 中的所有 Cookie](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. 刷新 ADF UX，然后重试。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>仅允许 ADF UX 使用 Cookie

如果你不想要允许所有 Cookie，则可以选择只允许 ADF UX：

1. 访问“edge://settings/content/cookies”。
1. 在 " **允许** " 部分下，选择 " **添加** 并添加 **adf.azure.com** 网站"。 

    ![将 ADF UX 添加到 Edge 中允许的站点](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. 刷新 ADF UX，然后重试。

## <a name="connection-failed-on-adf-ux"></a>ADF UX 上出现连接失败

有时，单击“测试连接”、“预览”等之后，你会在 ADF UX 上看到“连接失败”错误，类似于以下屏幕截图 。

![连接失败](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

在这种情况下，你可以先在浏览器中使用 InPrivate 浏览模式尝试相同的操作。

如果它仍不工作，请在浏览器中按 F12 打开 **开发人员工具**。 转到“网络”选项卡，选中“禁用缓存”，重试失败的操作，并找到失败的请求（红色） 。

![失败的请求](media/data-factory-ux-troubleshoot-guide/failed-request.png)

然后，在这种情况下查找 **主机名** (， **dpnortheurope.svc.datafactory.azure.com** 从失败请求的 **请求 URL**) 。

直接在浏览器的地址栏中键入该主机名。 如果你在浏览器中看到 404，这通常意味着客户端正常，问题出在 ADF 服务端。 通过 ADF UX 错误消息中的“活动 ID”提交支持票证。

![找不到资源](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

如果没有看到 404，或者在浏览器中看到类似下方的错误，这通常意味着你遇到了一些客户端问题。 进一步执行故障排除步骤。

![客户端错误](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

打开 " **命令提示符** " 并键入 " **nslookup dpnortheurope.svc.datafactory.azure.com**"。 正常响应应如下所示：

![命令响应 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

如果你看到正常的 DNS 响应，请与本地 IT 支持取得进一步联系，检查防火墙设置，确定是否阻止了与此主机名的 HTTPS 连接。 如果无法解决该问题，请通过 ADF UX 错误消息中的“活动 ID”提交支持票证。

如果你看到除此之外的任何内容，这通常意味着解析 DNS 名称时，DNS 服务器出现了问题。 通常，更改 ISP（Internet 服务提供商）或 DNS（例如，改为 Google DNS 8.8.8.8）可能是一种可行的解决办法。 如果问题仍然存在，可以进一步尝试 **nslookup datafactory.azure.com** 和 **nslookup azure.com** ，以查看 DNS 解析失败的级别，并将所有信息提交给本地 IT 支持部门或 ISP 进行故障排除。 如果他们认为该问题仍在 Microsoft 端，请使用 ADF UX 错误消息中的 **活动 ID** 来提交支持票证。

![命令响应 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>更改数据集中的链接服务类型

文件格式数据集可以与所有基于文件的连接器一起使用；例如，你可以在 Azure Blob 或 Azure Data Lake Storage Gen2 上配置 Parquet 数据集。 注意，每个连接器在活动中通过不同的应用模型，支持不同的数据存储相关设置集。 

在 ADF 创作 UI 上，若你在活动（包括复制、查找、GetMetadata、删除活动）中使用文件格式数据集，并且在该数据集中，你希望指向不同于当前类型的链接服务（例如从文件系统切换到 ADLS Gen2），则你会看到以下警告消息。 若要确保它是一个干净的开关，在您同意的情况下，将修改引用此数据集的管道和活动，以使用新类型，并且任何与新类型不兼容的现有数据存储设置都将被清除，因为它不再适用。

若要详细了解每个连接器支持的数据存储设置，可以转到相应的连接器文章 -> 复制活动属性以查看详细的属性列表。 请参阅 [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、 [文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

![警告消息](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

* [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
* [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter 中有关数据工厂的信息](https://twitter.com/hashtag/DataFactory)
* [Azure 视频](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft 问答页](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
