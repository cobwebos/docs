---
title: 在 Azure 存储中托管静态网站
description: 了解如何直接从 Azure 存储 GPv2 帐户中的容器提供静态内容（HTML、CSS、JavaScript 和映像文件）。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 35b5a85ea6fba87e785b581a7a20d0c28f312820
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484139"
---
# <a name="host-a-static-website-in-azure-storage"></a>在 Azure 存储中托管静态网站

可以直接从 Azure 存储 GPv2 帐户中的容器提供静态内容（HTML、CSS、JavaScript 和映像文件）。 若要了解详细信息，请参阅[Azure 存储中的静态网站托管](storage-blob-static-website.md)。

本文介绍如何使用 Azure 门户、Azure CLI 或 PowerShell 启用静态网站托管。

<a id="portal" />

## <a name="portal"></a>[门户](#tab/azure-portal)

有关分步教程，请参阅[教程：在 Blob 存储中承载静态网站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)。

启用静态网站宿主后，可以通过浏览器使用网站的公共 URL 查看网站的页面。

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>使用 Azure 门户查找网站 URL

在存储帐户的 "帐户概述" 页旁显示的窗格中，选择 "**静态网站**"。 站点的 URL 将显示在 "**主终结点**" 字段中。

![Azure 存储静态网站指标 - 指标](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用[Azure 命令行接口（CLI）](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)来启用静态网站托管。

1. 首先，打开[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest); 如果已在本地[安装](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)了 Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅的 ID。

3. 启用静态网站宿主。

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

   * 将 `<error-document-name>` 占位符替换为在浏览器请求站点上不存在的页面时向用户显示的错误文档的名称。

   * 将 `<index-document-name>` 占位符替换为索引文档的名称。 本文档通常是 "索引 html"。

4. 将对象从源目录上传到 $web 容器。

   > [!NOTE]
   > 如果使用 Azure Cloud Shell，请确保在引用 `$web` 容器时添加 `\` 转义符（例如 `\$web`）。 如果使用的是 Azure CLI 的本地安装，则无需使用转义符。

   此示例假设正在 Azure Cloud Shell 会话中运行命令。

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
   ```

   * 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

   * 将 `<source-path>` 占位符替换为要上传的文件所在位置的路径。

   > [!NOTE]
   > 如果使用的是 Azure CLI 的位置安装，则可以使用本地计算机上的任何位置的路径（例如： `C:\myFolder`。
   >
   > 如果使用 Azure Cloud Shell，则必须引用对 Cloud Shell 可见的文件共享。 此位置可以是云共享本身的文件共享，也可以是从 Cloud Shell 装载的现有文件共享。 若要了解如何执行此操作，请参阅[在 Azure Cloud Shell 中持久保存文件](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)。

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>使用 Azure CLI 查找网站 URL

您可以通过使用网站的公共 URL 来查看浏览器中的内容。

使用以下命令查找 URL：

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

* 将 `<resource-group-name>` 的占位符值替换为资源组的名称。

<a id="powershell" />

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

您可以使用 Azure PowerShell 模块启用静态网站宿主。

1. 打开 Windows PowerShell 命令窗口。

2. 验证 Azure PowerShell 模块 Az 0.7 版或更高版本。

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。

3. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
   Connect-AzAccount
   ```

4. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅的 ID。

5. 获取用于定义要使用的存储帐户的存储帐户上下文。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * 将 `<resource-group-name>` 的占位符值替换为资源组的名称。

   * 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

6. 启用静态网站宿主。

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * 将 `<error-document-name>` 占位符替换为在浏览器请求站点上不存在的页面时向用户显示的错误文档的名称。

   * 将 `<index-document-name>` 占位符替换为索引文档的名称。 本文档通常是 "索引 html"。

7. 将对象从源目录上传到 $web 容器。

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Properties @{ ContentType = "text/html; charset=utf-8";} `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * 将 `<path-to-file>` 的占位符值替换为要上传的文件的完全限定路径（例如： `C:\temp\index.html`）。

   * 将 `<blob-name>` 的占位符值替换为要为生成的 blob 提供的名称（例如： `index.html`）。

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>使用 PowerShell 查找网站 URL

您可以通过使用网站的公共 URL 来查看浏览器中的内容。

使用以下命令查找 URL：

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* 将 `<resource-group-name>` 的占位符值替换为资源组的名称。

* 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

<a id="metrics" />

---

## <a name="enable-metrics-on-static-website-pages"></a>在静态网站页面上启用指标

一旦启用了指标，就会在 "指标" 仪表板中报告 **$web**容器中的文件的流量统计信息。

1. 单击 "**设置**" > **监视** > **指标**"。

   将通过挂接到不同的指标 API 来生成指标数据。 门户只会显示在给定时间范围内使用的 API 成员，以便重点关注可返回数据的成员。 为了确保能够选择必要的 API 成员，第一步是扩展时间范围。

2. 单击 "时间范围" 按钮，然后选择 "**最近24小时**"，并单击 "**应用**"。

   ![Azure 存储静态网站指标 - 时间范围](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. 从 "*命名空间*" 下拉选择 " **Blob** "。

   ![Azure 存储静态网站指标 - 命名空间](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 然后选择“传出”指标。

   ![Azure 存储静态网站指标 - 指标](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. 从“聚合”选择器中选择“总和”。

   ![Azure 存储静态网站指标 - 聚合](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. 单击 "**添加筛选器**" 按钮，并从*属性*选择器中选择 " **API 名称**"。

   ![Azure 存储静态网站指标 - API 名称](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. 选中 "*值*" 选择器中 " **GetWebContent** " 旁边的框，以填充指标报表。

   ![Azure 存储静态网站指标 - GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>后续步骤

* 了解如何使用静态网站配置自定义域。 请参阅将[自定义域映射到 Azure Blob 存储终结点](storage-custom-domain-name.md)。

