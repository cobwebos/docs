---
title: 将自定义域映射到 Azure Blob 存储终结点
titleSuffix: Azure Storage
description: 将自定义域映射到 Azure 存储帐户中的 Blob 存储或 web 终结点。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370468"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>将自定义域映射到 Azure Blob 存储终结点

可以将自定义域映射到 blob 服务终结点或[静态网站](storage-blob-static-website.md)终结点。 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> 此映射仅适用于子域（例如： `www.contoso.com`）。 如果希望 web 终结点在根域上可用（例如： `contoso.com`），则必须使用 Azure CDN。 有关指南，请参阅本文中的将[自定义域映射到已启用 HTTPS](#enable-https)部分。 由于你转到本文的这一节来启用自定义域的根域，因此用于启用 HTTPS 的那一节中的步骤是可选的。 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>映射已启用 HTTP 的自定义域

此方法更简单，但只启用 HTTP 访问。 如果将存储帐户配置为需要通过 HTTPS 进行[安全传输](../common/storage-require-secure-transfer.md)，则必须为自定义域启用 HTTPS 访问。 

若要启用 HTTPS 访问，请参阅本文中的[使用 https 启用映射自](#enable-https)定义域部分。 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>映射自定义域

> [!IMPORTANT]
> 你的自定义域将在你完成配置时暂时不可用。 如果你的域当前支持的应用程序的服务级别协议（SLA）要求零停机，请按照本文中的将[自定义域映射为零停机](#zero-down-time)一节中的步骤进行操作，以确保用户能够在 DNS 映射发生时访问你的域。

如果你不关心用户的域暂时不可用，请执行以下步骤。

： heavy_check_mark：步骤1：获取存储终结点的主机名。

： heavy_check_mark：步骤2：使用域提供程序创建规范名称（CNAME）记录。

： heavy_check_mark：步骤3：将自定义域注册到 Azure。 

： heavy_check_mark：步骤4：测试自定义域。

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>步骤1：获取存储终结点的主机名 

主机名是没有协议标识符和尾随斜杠的存储终结点 URL。 

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格的 "**设置**" 下，选择 "**属性**"。  

3. 将**主 Blob 服务终结点**的值或**主静态网站终结点**复制到文本文件。 

4. 从该字符串中删除协议标识符（*如*HTTPS）和尾随斜杠。 下表包含一些示例。

   | 终结点的类型 |  endpoint | 主机名 |
   |------------|-----------------|-------------------|
   |blob 服务  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |静态网站  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   稍后将此值设置为。

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>步骤2：使用您的域提供程序创建规范名称（CNAME）记录

创建一个 CNAME 记录，使其指向您的主机名。 CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名。

1. 登录到域注册机构的网站，然后前往 "管理 DNS" 设置页。

   可在名为“域名”、“DNS”或“名称服务器管理”的部分中找到该页。

2. 找到用于管理 CNAME 记录的部分。 

   可能需要转到高级设置页，并查找“CNAME”、“别名”或“子域”。

3. 创建 CNAME 记录。 作为该记录的一部分，请提供以下各项： 

   - 子域别名，如 `www` 或 `photos`。 子域是必需的，不支持根域。 
      
   - 本文前面的[获取存储终结点的主机名](#endpoint)部分中获取的主机名。 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>步骤3：将自定义域注册到 Azure

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格中的“Blob 服务”下，选择“自定义域”。  

   ![自定义域选项](./media/storage-custom-domain-name/custom-domain-button.png "自定义域")

   此时会打开“自定义域”窗格。

3. 在 "**域名**" 文本框中，输入自定义域的名称，包括子域  
   
   例如，如果你的域是*contoso.com* ，而你的子域别名是*www*，则输入 `www.contoso.com`。 如果你的子域是*照片*，请输入 `photos.contoso.com`。

4. 若要注册自定义域，请选择 "**保存**" 按钮。

   通过域名服务器（DNS）传播 CNAME 记录后，如果用户具有适当的权限，则可以使用自定义域查看 blob 数据。

#### <a name="step-4-test-your-custom-domain"></a>步骤4：测试自定义域

若要确认自定义域是否映射到了 Blob 服务终结点，请在存储帐户中的公共容器内创建一个 Blob。 然后在 Web 浏览器中，使用以下格式的 URI 来访问该 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要访问 *photos.contoso.com* 自定义子域中的 *myforms* 容器内的 Web 窗体：可使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>将自定义域映射到零停机时间

> [!NOTE]
> 如果你不关心用户的域暂时不可用，请考虑按照本文的[映射自](#map-a-domain)定义域部分中的步骤进行操作。 更简单的方法是使用更少的步骤。  

如果你的域当前支持的应用程序的服务级别协议（SLA）要求零停机，则请按照以下步骤操作，以确保用户可以在 DNS 映射发生时访问你的域。 

： heavy_check_mark：步骤1：获取存储终结点的主机名。

： heavy_check_mark：步骤2：使用域提供程序创建中间规范名称（CNAME）记录。

： heavy_check_mark：步骤3：将自定义域预先注册到 Azure。

： heavy_check_mark：步骤4：使用域提供程序创建 CNAME 记录。

： heavy_check_mark：步骤5：测试自定义域。

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>步骤1：获取存储终结点的主机名 

主机名是没有协议标识符和尾随斜杠的存储终结点 URL。 

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格的 "**设置**" 下，选择 "**属性**"。  

3. 将**主 Blob 服务终结点**的值或**主静态网站终结点**复制到文本文件。 

4. 从该字符串中删除协议标识符（*如*HTTPS）和尾随斜杠。 下表包含一些示例。

   | 终结点的类型 |  endpoint | 主机名 |
   |------------|-----------------|-------------------|
   |blob 服务  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |静态网站  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   稍后将此值设置为。

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>步骤2：使用您的域提供程序创建中间规范名称（CNAME）记录

创建一个临时 CNAME 记录以指向您的主机名。 CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名。

1. 登录到域注册机构的网站，然后前往 "管理 DNS" 设置页。

   可在名为“域名”、“DNS”或“名称服务器管理”的部分中找到该页。

2. 找到用于管理 CNAME 记录的部分。 

   可能需要转到高级设置页，并查找“CNAME”、“别名”或“子域”。

3. 创建 CNAME 记录。 作为该记录的一部分，请提供以下各项： 

   - 子域别名，如 `www` 或 `photos`。 子域是必需的，不支持根域。

     将 `asverify` 子域添加到别名。 例如： `asverify.www` 或 `asverify.photos` 。
       
   - 本文前面的[获取存储终结点的主机名](#endpoint)部分中获取的主机名。 

     将子域 `asverify` 添加到主机名。 例如：`asverify.mystorageaccount.blob.core.windows.net`。

4. 若要注册自定义域，请选择 "**保存**" 按钮。

   如果注册成功，则门户会通知存储帐户已成功更新。 自定义域已由 Azure 验证，但发往域的流量尚未路由到存储帐户。

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>步骤3：将自定义域预注册到 Azure

在将自定义域预先注册到 Azure 时，你允许 Azure 识别你的自定义域，而不必修改该域的 DNS 记录。 这样一来，当你修改域的 DNS 记录时，它将映射到 blob 终结点，而不会造成停机。

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格中的“Blob 服务”下，选择“自定义域”。  

   ![自定义域选项](./media/storage-custom-domain-name/custom-domain-button.png "自定义域")

   此时会打开“自定义域”窗格。

3. 在 "**域名**" 文本框中，输入自定义域的名称，包括子域  
   
   例如，如果你的域是*contoso.com* ，而你的子域别名是*www*，则输入 `www.contoso.com`。 如果你的子域是*照片*，请输入 `photos.contoso.com`。

4. 选中“使用间接 CNAME 验证”复选框。

5. 若要注册自定义域，请选择 "**保存**" 按钮。
  
   通过域名服务器（DNS）传播 CNAME 记录后，如果用户具有适当的权限，则可以使用自定义域查看 blob 数据。

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>步骤4：使用域提供程序创建 CNAME 记录

创建一个临时 CNAME 记录以指向您的主机名。

1. 登录到域注册机构的网站，然后前往 "管理 DNS" 设置页。

   可在名为“域名”、“DNS”或“名称服务器管理”的部分中找到该页。

2. 找到用于管理 CNAME 记录的部分。 

   可能需要转到高级设置页，并查找“CNAME”、“别名”或“子域”。

3. 创建 CNAME 记录。 作为该记录的一部分，请提供以下各项： 

   - 子域别名，如 `www` 或 `photos`。 子域是必需的，不支持根域。
      
   - 本文前面的[获取存储终结点的主机名](#endpoint-2)部分中获取的主机名。 

#### <a name="step-5-test-your-custom-domain"></a>步骤5：测试自定义域

若要确认自定义域是否映射到了 Blob 服务终结点，请在存储帐户中的公共容器内创建一个 Blob。 然后在 Web 浏览器中，使用以下格式的 URI 来访问该 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要访问 *photos.contoso.com* 自定义子域中的 *myforms* 容器内的 Web 窗体：可使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>删除自定义域映射

若要删除自定义域映射，请取消注册该自定义域。 使用以下过程之一。

#### <a name="portal"></a>[门户](#tab/azure-portal)

若要删除自定义域设置，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格中的“Blob 服务”下，选择“自定义域”。  
   此时会打开“自定义域”窗格。

3. 清除包含自定义域名的文本框的内容。

4. 选择“保存”按钮。

成功删除自定义域后，你将看到一个门户通知，指出你的存储帐户已成功更新

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要删除自定义域注册，请使用 [az storage account update](https://docs.microsoft.com/cli/azure/storage/account) CLI 命令，并为 `""` 参数值指定空字符串 (`--custom-domain`)。

* 命令格式：

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* 命令示例：

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要删除自定义域注册，请使用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet，并为 `""` 参数值指定空字符串 (`-CustomDomainName`)。

* 命令格式：

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* 命令示例：

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>映射已启用 HTTPS 的自定义域

此方法涉及更多步骤，但它启用 HTTPS 访问。 

如果不需要用户使用 HTTPS 访问 blob 或 web 内容，请参阅本文中的 "[仅使用启用 HTTP 的自](#enable-http)定义域" 一节。 

若要映射自定义域并启用 HTTPS 访问，请执行以下操作：

1. 在 blob 或 web 终结点上启用[Azure CDN](../../cdn/cdn-overview.md) 。 

   有关 Blob 存储终结点，请参阅[将 Azure 存储帐户与 Azure CDN 集成](../../cdn/cdn-create-a-storage-account-with-cdn.md)。 

   有关静态网站终结点，请参阅[将静态网站与 Azure CDN 集成](static-website-content-delivery-network.md)。

2. [将 Azure CDN 内容映射到自定义域](../../cdn/cdn-map-content-to-custom-domain.md)。

3. [在 Azure CDN 自定义域上启用 HTTPS](../../cdn/cdn-custom-ssl.md)。

   > [!NOTE] 
   > 更新静态网站时，请确保通过清除 CDN 终结点来清除 CDN 边缘服务器上的缓存内容。 有关详细信息，请参阅[清除 Azure CDN 终结点](../../cdn/cdn-purge-endpoint.md)。

4. 可有可无查看以下指南：

   * [具有 Azure CDN 的共享访问签名（SAS）令牌](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures)。

   * [带有 Azure CDN 的 HTTP 到 HTTPS 的重定向](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)。

   * [使用 Blob 存储与 Azure CDN 时的定价和计费](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)。

## <a name="next-steps"></a>后续步骤

* [了解 Azure Blob 存储中托管的静态网站](storage-blob-static-website.md)
