---
title: 托管在 Azure 存储中的静态网站
description: 了解如何在 Azure 存储 GPv2 帐户中提供直接在容器中的静态内容 （HTML、 CSS、 JavaScript 和图像文件）。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 5ab24a99b22fae172b5308ba7477953f27ecfd44
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435956"
---
# <a name="host-a-static-website-in-azure-storage"></a>托管在 Azure 存储中的静态网站

可以在 Azure 存储 GPv2 帐户中提供静态内容 （HTML、 CSS、 JavaScript 和图像文件） 直接从一个容器。 若要了解详细信息，请参阅[静态网站托管在 Azure 存储中](storage-blob-static-website.md)。

本文介绍如何启用静态网站托管使用 Azure 门户、 Azure CLI 或 PowerShell。

<a id="portal" />

## <a name="use-the-azure-portal"></a>使用 Azure 门户

有关分步教程，请参阅[教程：在 Blob 存储上托管静态网站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)。

启用静态网站托管后，可以通过网站的公共 URL 来查看你的站点从浏览器的页。

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>使用 Azure 门户中查找网站的 URL

在你的存储帐户的帐户概述页的旁边会显示窗格中，选择**静态网站**。 你的网站的 URL 将显示在**主终结点**字段。

![Azure 存储静态网站指标 - 指标](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>使用 Azure CLI

可以让使用静态网站承载[Azure 命令行接口 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

1. 首先，打开[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或如果你已[安装](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI 在本地，打开 Windows PowerShell 等命令控制台应用程序。

2. 如果你的身份与多个订阅相关联，则您的活动设置订阅的存储帐户来托管静态网站。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   替换为`<subscription-id>`占位符值替换你的订阅的 ID。

3. 启用静态网站托管。

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

   * 替换为`<error-document-name>`占位符替换将在浏览器请求不存在您站点上的页时向用户显示的错误文档的名称。

   * 替换为`<index-document-name>`索引文档名称的占位符。 本文档通常是"index.html"。

4. 将对象从源目录上传到 $web 容器  。

   > [!NOTE]
   > 如果使用 Azure Cloud Shell，请务必添加`\`引用时转义符`$web`容器 (例如： `\$web`)。 如果你使用 Azure CLI 的本地安装，然后您无需使用转义符。

   此示例假定你从 Azure Cloud Shell 会话中运行命令。

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

   * 替换为`<source-path>`占位符替换为你想要上传的文件的位置的路径。

   > [!NOTE]
   > 如果使用的位置安装的 Azure CLI，则可以在本地计算机上的任何位置使用路径 (例如： `C:\myFolder`。
   >
   > 如果你使用 Azure Cloud Shell，您必须引用可见到 Cloud Shell 的文件共享。 此位置可能是在云中的文件共享共享本身或从 Cloud Shell 中装载的现有文件共享。 若要了解如何执行此操作，请参阅[持久保存在 Azure Cloud Shell 中的文件](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)。

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>使用 Azure CLI 查找网站的 URL

可以使用该网站的公共 URL 来查看从浏览器的内容。

使用以下命令查找 URL:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

* 替换为`<resource-group-name>`占位符值替换资源组的名称。

<a id="powershell" />

## <a name="use-powershell"></a>使用 PowerShell

你可以启用静态网站托管使用 Azure PowerShell 模块。

1. 打开 Windows PowerShell 命令窗口。

2. 验证具有 Azure PowerShell 模块 Az 版本 0.7 或更高版本。

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。

3. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
   Connect-AzAccount
   ```

4. 如果你的身份与多个订阅相关联，则您的活动设置订阅的存储帐户来托管静态网站。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   替换为`<subscription-id>`占位符值替换你的订阅的 ID。

5. 获取用于定义你想要使用的存储帐户的存储帐户上下文。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * 替换为`<resource-group-name>`占位符值替换资源组的名称。

   * 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

6. 启用静态网站托管。

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * 替换为`<error-document-name>`占位符替换将在浏览器请求不存在您站点上的页时向用户显示的错误文档的名称。

   * 替换为`<index-document-name>`索引文档名称的占位符。 本文档通常是"index.html"。

7. 将对象从源目录上传到 $web 容器  。

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * 替换`<path-to-file>`占位符值替换为你想要上传的文件的完全限定路径 (例如： `C:\temp\index.html`)。

   * 替换`<blob-name>`占位符值替换为生成的 blob 的名称 (例如： `index.html`)。

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>使用 PowerShell 查找网站的 URL

可以使用该网站的公共 URL 来查看从浏览器的内容。

使用以下命令查找 URL:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* 替换为`<resource-group-name>`占位符值替换资源组的名称。

* 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>启用静态网站页上的度量值

一旦您启用指标，流量中的文件的统计信息 **$web**容器报告指标仪表板中。

1. 单击**设置** > **监视** > **指标**。

   将通过挂接到不同的指标 API 来生成指标数据。 门户只会显示在给定时间范围内使用的 API 成员，以便重点关注可返回数据的成员。 为了确保您可以选择所需的 API 成员，第一步是展开时间范围。

2. 单击时间范围按钮，然后选择**过去 24 小时**，然后单击**应用**。

   ![Azure 存储静态网站指标 - 时间范围](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. 选择**Blob**从*Namespace*下拉列表。

   ![Azure 存储静态网站指标 - 命名空间](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 然后选择“传出”指标。 

   ![Azure 存储静态网站指标 - 指标](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. 从“聚合”选择器中选择“总和”。  

   ![Azure 存储静态网站指标 - 聚合](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. 单击**添加筛选器**按钮，然后选择**API 名称**从*属性*选择器。

   ![Azure 存储静态网站指标 - API 名称](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. 为了增加点乐趣框旁边**GetWebContent**中*值*选择器来填充指标报表。

   ![Azure 存储静态网站指标 - GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>后续步骤

* [Azure 存储中的静态网站托管](storage-blob-static-website.md)
* [使用 Azure CDN 通过 HTTPS 访问包含自定义域的 blob](storage-https-custom-domain-cdn.md)
* [为 blob 或 Web 终结点配置自定义域名](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure 应用服务](/azure/app-service/overview)
* [生成首个无服务器 Web 应用程序](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教程：在 Azure DNS 中托管域](../../dns/dns-delegate-domain-azure-dns.md)
