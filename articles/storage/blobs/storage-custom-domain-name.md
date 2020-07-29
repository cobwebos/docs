---
title: 将自定义域映射到 Azure Blob 存储终结点
titleSuffix: Azure Storage
description: 将自定义域映射到 Azure 存储帐户中的 Blob 存储或 Web 终结点。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 95fd62584ef73f3f2f198c84913652f460fc5b1a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465530"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>将自定义域映射到 Azure Blob 存储终结点

可以将自定义域映射到 blob 服务终结点或[静态网站](storage-blob-static-website.md)终结点。 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> 这种映射仅适用于子域（例如：`www.contoso.com`）。 如果希望在根域上提供 web 终结点（例如： `contoso.com` ），则必须使用 Azure CDN。 有关指南，请参阅本文中的将[自定义域映射到已启用 HTTPS](#enable-https)部分。 由于你转到本文的这一节来启用自定义域的根域，因此用于启用 HTTPS 的那一节中的步骤是可选的。 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>在启用仅限 HTTP 的情况下映射自定义域

此方法更简单，但要启用仅限 HTTP 的访问。 如果将存储帐户配置为需要通过 HTTPS 进行[安全传输](../common/storage-require-secure-transfer.md)，则必须为自定义域启用 HTTPS 访问。 

若要启用 HTTPS 访问，请参阅本文中的[使用 https 启用映射自](#enable-https)定义域部分。 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>映射自定义域

> [!IMPORTANT]
> 在完成配置的过程中，用户会有短暂的时间无法使用你的自定义域。 如果域当前支持的某个应用程序所附带的服务级别协议 (SLA) 要求停机时间为零，请遵循本文的[在不停机的情况下映射自定义域](#zero-down-time)部分中的步骤，以确保用户在 DNS 映射期间能够访问该域。

如果你不担心域有短暂的时间对用户不可用，请执行以下步骤。

： heavy_check_mark：步骤1：获取存储终结点的主机名。

： heavy_check_mark：步骤2：使用域提供程序创建规范名称（CNAME）记录。

： heavy_check_mark：步骤3：将自定义域注册到 Azure。 

： heavy_check_mark：步骤4：测试自定义域。

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>步骤1：获取存储终结点的主机名 

主机名是没有协议标识符和尾部斜杠的存储终结点 URL。 

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格中的“设置”下，选择“属性”。********  

3. 将**主 Blob 服务终结点**的值或**主静态网站终结点**复制到文本文件。 

4. 从该字符串中删除协议标识符（例如 HTTPS）和尾部斜杠。** 下表提供了一些示例。

   | 终结点的类型 |  endpoint | 主机名 |
   |------------|-----------------|-------------------|
   |Blob 服务  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |静态网站  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   请设置此值供稍后使用。

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>步骤2：使用您的域提供程序创建规范名称（CNAME）记录

创建一条 CNAME 记录，以指向主机名。 CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名。

1. 登录到域注册机构的网站，并转到用于管理 DNS 设置的页面。

   可在名为“域名”、“DNS”或“名称服务器管理”的部分中找到该页。************

2. 找到用于管理 CNAME 记录的部分。 

   可能需要转到高级设置页，并查找“CNAME”、“别名”或“子域”。************

3. 创建 CNAME 记录。 在该记录中提供以下项： 

   - 子域别名，例如 `www` 或 `photos`。 必须指定子域，不支持根域。 
      
   - 在本文前面的[获取存储终结点的主机名](#endpoint)部分获取的主机名。 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>步骤3：将自定义域注册到 Azure

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格中的“Blob 服务”下，选择“自定义域”。********  

   ![自定义域选项](./media/storage-custom-domain-name/custom-domain-button.png "自定义域")

   此时会打开“自定义域”窗格。****

3. 在“域名”文本框中输入自定义域的名称，包括子域****  
   
   例如，如果域是 *contoso.com*，子域别名是 *www*，请输入 `www.contoso.com`。 如果子域是 *photos*，请输入 `photos.contoso.com`。

4. 若要注册自定义域，请选择“保存”按钮。****

   CNAME 记录通过域名服务器 (DNS) 传播后，如果用户具有相应的权限，则他们可以使用自定义域查看 Blob 数据。

#### <a name="step-4-test-your-custom-domain"></a>步骤4：测试自定义域

若要确认自定义域是否映射到了 Blob 服务终结点，请在存储帐户中的公共容器内创建一个 Blob。 然后在 Web 浏览器中，使用以下格式的 URI 来访问该 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要访问 *photos.contoso.com* 自定义子域中的 *myforms* 容器内的 Web 窗体：可使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>在不停机的情况下映射自定义域

> [!NOTE]
> 如果你不担心域有短暂的时间对用户不可用，请考虑执行本文的[映射自定义域](#map-a-domain)部分中的步骤。 这是一种更简单的方法，且步骤更少。  

如果域当前支持的某个应用程序所附带的服务级别协议 (SLA) 要求停机时间为零，请执行这些步骤，以确保用户在 DNS 映射期间能够访问该域。 

： heavy_check_mark：步骤1：获取存储终结点的主机名。

： heavy_check_mark：步骤2：使用域提供程序创建中间规范名称（CNAME）记录。

： heavy_check_mark：步骤3：将自定义域预先注册到 Azure。

： heavy_check_mark：步骤4：使用域提供程序创建 CNAME 记录。

： heavy_check_mark：步骤5：测试自定义域。

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>步骤1：获取存储终结点的主机名 

主机名是没有协议标识符和尾部斜杠的存储终结点 URL。 

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格中的“设置”下，选择“属性”。********  

3. 将**主 Blob 服务终结点**的值或**主静态网站终结点**复制到文本文件。 

4. 从该字符串中删除协议标识符（例如 HTTPS）和尾部斜杠。** 下表提供了一些示例。

   | 终结点的类型 |  endpoint | 主机名 |
   |------------|-----------------|-------------------|
   |Blob 服务  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |静态网站  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   请设置此值供稍后使用。

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>步骤2：使用您的域提供程序创建中间规范名称（CNAME）记录

创建一条临时 CNAME 记录，以指向主机名。 CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名。

1. 登录到域注册机构的网站，并转到用于管理 DNS 设置的页面。

   可在名为“域名”、“DNS”或“名称服务器管理”的部分中找到该页。************

2. 找到用于管理 CNAME 记录的部分。 

   可能需要转到高级设置页，并查找“CNAME”、“别名”或“子域”。************

3. 创建 CNAME 记录。 在该记录中提供以下项： 

   - 子域别名，例如 `www` 或 `photos`。 必须指定子域，不支持根域。

     将 `asverify` 子域添加到别名。 例如： `asverify.www` 或 `asverify.photos` 。
       
   - 在本文前面的[获取存储终结点的主机名](#endpoint)部分获取的主机名。 

     将子域 `asverify` 添加到主机名。 例如：`asverify.mystorageaccount.blob.core.windows.net`。

4. 若要注册自定义域，请选择“保存”按钮。****

   如果注册成功，则门户会通知存储帐户已成功更新。 自定义域已由 Azure 验证，但发往域的流量尚未路由到存储帐户。

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>步骤3：将自定义域预注册到 Azure

将自定义域预先注册到 Azure 后，可使 Azure 识别该自定义域，且无需修改该域的 DNS 记录。 这样，在修改该域的 DNS 记录时，它会映射到 Blob 终结点，并且不会出现停机。

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格中的“Blob 服务”下，选择“自定义域”。********  

   ![自定义域选项](./media/storage-custom-domain-name/custom-domain-button.png "自定义域")

   此时会打开“自定义域”窗格。****

3. 在“域名”文本框中输入自定义域的名称，包括子域****  
   
   例如，如果域是 *contoso.com*，子域别名是 *www*，请输入 `www.contoso.com`。 如果子域是 *photos*，请输入 `photos.contoso.com`。

4. 选中“使用间接 CNAME 验证”复选框。****

5. 若要注册自定义域，请选择“保存”按钮。****
  
   CNAME 记录通过域名服务器 (DNS) 传播后，如果用户具有相应的权限，则他们可以使用自定义域查看 Blob 数据。

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>步骤4：使用域提供程序创建 CNAME 记录

创建一条临时 CNAME 记录，以指向主机名。

1. 登录到域注册机构的网站，并转到用于管理 DNS 设置的页面。

   可在名为“域名”、“DNS”或“名称服务器管理”的部分中找到该页。************

2. 找到用于管理 CNAME 记录的部分。 

   可能需要转到高级设置页，并查找“CNAME”、“别名”或“子域”。************

3. 创建 CNAME 记录。 在该记录中提供以下项： 

   - 子域别名，例如 `www` 或 `photos`。 必须指定子域，不支持根域。
      
   - 在本文前面的[获取存储终结点的主机名](#endpoint-2)部分获取的主机名。 

#### <a name="step-5-test-your-custom-domain"></a>步骤5：测试自定义域

若要确认自定义域是否映射到了 Blob 服务终结点，请在存储帐户中的公共容器内创建一个 Blob。 然后在 Web 浏览器中，使用以下格式的 URI 来访问该 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要访问 *photos.contoso.com* 自定义子域中的 *myforms* 容器内的 Web 窗体：可使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>删除自定义域映射

若要删除自定义域映射，请取消注册自定义域。 使用以下过程之一。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

若要删除自定义域设置，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。

2. 在菜单窗格中的“Blob 服务”下，选择“自定义域”。********  
   此时会打开“自定义域”窗格。****

3. 清除包含自定义域名的文本框的内容。

4. 选择“保存”按钮。

成功删除自定义域后，会看到一条门户通知，指出存储帐户已成功更新

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要删除自定义域注册，请使用 [az storage account update](https://docs.microsoft.com/cli/azure/storage/account) CLI 命令，并为 `--custom-domain` 参数值指定空字符串 (`""`)。

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

若要删除自定义域注册，请使用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet，并为 `-CustomDomainName` 参数值指定空字符串 (`""`)。

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

<a id="enable-https"></a>

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
