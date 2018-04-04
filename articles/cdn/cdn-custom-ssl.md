---
title: 在 Azure 内容交付网络自定义域上配置 HTTPS | Microsoft Docs
description: 了解如何在具有自定义域的 Azure CDN 终结点上启用或禁用 HTTPS。
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: casoper
ms.openlocfilehash: fea7121fc67944b20b8f39007edb0c0aad86aeaa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="configure-https-on-an-azure-content-delivery-network-custom-domain"></a>在 Azure 内容交付网络自定义域上配置 HTTPS

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Microsoft 支持对 Azure 内容交付网络 (CDN) 上的自定义域使用 HTTPS 协议。 借助 HTTPS 自定义域支持，可以通过 SSL 使用自己的域名传送安全内容以提高在传输过程中数据的安全性。 通过一键式启用、完整的证书管理可简化为自定义域启用 HTTPS 的工作流，所有这些都不会增加成本。

在传输过程中确保 Web 应用程序敏感数据的隐私和数据完整性至关重要。 使用 HTTPS 协议可确保通过 Internet 发送敏感数据时对其进行加密。 它提供信任、身份验证并保护 Web 应用程序免受攻击。 默认情况下，Azure CDN 在 CDN 终结点上支持 HTTPS。 例如，如果从 Azure CDN（例如 https:\//contoso.azureedge.net）创建 CDN 终结点，将自动启用 HTTPS。 此外，借助自定义域 HTTPS 支持，还可以为自定义域（例如 https:\//www.contoso.com）启用安全交付。 

HTTPS 功能的一些关键属性包括：

- 无需额外付费：证书获取或续订不收取费用，对于 HTTPS 流量不另外收费。 只需为从 CDN 出口的 GB 数付费。

- 简单启用：可从 [Azure 门户](https://portal.azure.com)进行一键式预配。 还可以使用 REST API 或其他开发人员工具启用该功能。

- 完整的证书管理：处理所有证书获取和管理。 证书在过期之前将自动进行设置并续订，这可消除由于证书过期而导致服务中断的风险。

>[!NOTE] 
>在启用 HTTPS 支持前，必须已建立 [Azure CDN 自定义域](./cdn-map-content-to-custom-domain.md)。

## <a name="enabling-https"></a>启用 HTTPS

若要在自定义域上启用 HTTPS，请执行以下步骤：

### <a name="step-1-enable-the-feature"></a>步骤 1：启用该功能 

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到 Verizon 标准或高级 CDN 配置文件。

2. 在终结点的列表中，单击包含自定义域的终结点。

3. 单击要启用 HTTPS 的自定义域。

    ![自定义域列表](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. 单击“打开”以启用 HTTPS，然后单击“应用”。

    ![自定义域 HTTPS 状态](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>步骤 2：验证域

>[!NOTE]
>如果通过 DNS 提供商获得证书颁发机构授权 (CAA) 记录，则必须包含 DigiCert 作为一个有效的 CA。 CAA 记录允许域名所有者通过自己的 DNS 提供商指定哪些 CA 有权为其域名颁发证书。 如果某个 CA 收到具有 CAA 记录的域证书订单，但该 CA 未被列为授权的颁发者，则禁止向该域或子域颁发证书。 有关管理 CAA 记录的信息，请参阅[管理 CAA 记录](https://support.dnsimple.com/articles/manage-caa-record/)。 有关 CAA 记录工具，请参阅 [CAA 记录帮助器](https://sslmate.com/caa/)。

#### <a name="custom-domain-is-mapped-to-cdn-endpoint"></a>自定义域将映射到 CDN 终结点

将自定义域添加到终结点时，会在域注册机构的 DNS 表中创建一条 CNAME 记录，以映射到 CDN 终结点主机名。 如果该 CNAME 记录仍然存在，并且不包含 cdnverify 子域，则 DigiCert 证书颁发机构 (CA) 将使用它来验证自定义域的所有权。 

CNAME 记录应采用以下格式，其中 *Name* 是自定义域名，*Value* 是 CDN 终结点主机名：

| 名称            | Type  | 值                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

有关 CNAME 记录的详细信息，请参阅[创建 CNAME DNS 记录](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#step-2-create-the-cname-dns-records)。

如果 CNAME 记录采用正确的格式，DigiCert 会自动验证自定义域名，并将其添加到使用者可选名称 (SAN) 证书。 DigitCert 不会向你发送验证电子邮件，并且你无需批准请求。 该证书会在一年内有效，并会在过期前自动续订。 继续执行[步骤 3：等待传播](#step-3-wait-for-propagation)。 

#### <a name="cname-record-is-not-mapped-to-cdn-endpoint"></a>CNAME 记录未映射到 CDN 终结点

如果终结点的 CNAME 记录条目不再存在，或者它包含 cdnverify 子域，请按照此步骤中的其余说明进行操作。

在自定义域上启用 HTTPS 后，DigiCert 证书颁发机构 (CA) 会根据域的 [WHOIS](http://whois.domaintools.com/) 注册者信息，通过联系域的注册者来验证域的所有权。 通过 WHOIS 注册中列出的电子邮件地址（默认）或电话号码进行联系。 必须先完成域验证，才能在自定义域上激活 HTTPS。 可在 6 个工作日内批准域。 自动取消 6 个工作日内未批准的请求。 

![WHOIS 记录](./media/cdn-custom-ssl/whois-record.png)

DigiCert 还会将验证电子邮件发送到其他电子邮件地址。 如果 WHOIS 注册信息属于隐私，请验证是否可直接从以下任一地址进行审批：

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

应会在几分钟内收到如以下所示的电子邮件，要求你批准请求。 如果使用垃圾邮件筛选，请将 admin@digicert.com 添加到允许列表。 如果未在 24 小时内收到电子邮件，请与 Microsoft 支持部门联系。
    
![域验证电子邮件](./media/cdn-custom-ssl/domain-validation-email.png)

单击批准链接时，会定向到以下在线批准表： 
    
![域验证表单](./media/cdn-custom-ssl/domain-validation-form.png)

按表中的说明操作；有两种验证选项：

- 可批准通过同一根域（例如 consoto.com）的同一帐户下的所有将来订单。如果你打算为同一根域添加其他自定义域，建议使用此方法。

- 可以只批准该请求中使用的特定主机名。 后续请求将需要其他批准。

批准后，DigiCert 会将自定义域名添加到 SAN 证书。 该证书会在一年内有效，并会在过期前自动续订。

### <a name="step-3-wait-for-propagation"></a>步骤 3：等待传播

验证域名后，将需要长达 6-8 小时才能使自定义域 HTTPS 功能激活。 此过程完成后，Azure 门户中的自定义 HTTPS 状态会设置为“已启用”，且自定义域对话框中的四个操作步骤会标记为完成。 自定义域现可使用 HTTPS。

![启用 HTTPS 对话框](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>操作进程

下表显示启用 HTTPS 时出现的操作进程。 启用 HTTPS 后，自定义域对话框中将出现四个操作步骤。 每个步骤变为活动状态时，其下将随之显示更多子步骤详细信息。 并非所有这些子步骤都会执行。 步骤成功完成后，它旁边会显示一个绿色的复选标记。 

| 操作步骤 | 操作子步骤详细信息 | 
| --- | --- |
| 1 个提交请求 | 提交请求 |
| | 正在提交 HTTPS 请求。 |
| | 已成功提交 HTTPS 请求。 |
| 2 个域验证 | 已向你发送电子邮件，要求你验证域所有权。 正在等待确认。 ** |
| | 已成功验证域所有权。 |
| | 域所有权验证请求已过期（很可能是客户在 6 天内未响应）。 将不会在域中启用 HTTPS。 * |
| | 客户已拒绝域所有权验证请求。 将不会在域中启用 HTTPS。 * |
| 3 设置证书 | 证书颁发机构当前正在颁发在你的域中启用 HTTPS 所需的证书。 |
| | 证书已颁发，当前正将证书部署到 CDN 网络。 最多可能需要 6 小时才能完成此操作。 |
| | 已成功将证书部署到 CDN 网络。 |
| 4 个完成 | 已成功在域中启用 HTTPS。 |

\* 除非出现错误，否则不会显示此消息。 

\** 如果自定义域的 CNAME 条目直接指向 CDN 终结点主机名，则不会显示此消息。

如果提交请求之前出现错误，则会显示以下错误消息：

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>禁用 HTTPS

在自定义域上启用 HTTPS 后，可将其禁用。 若要禁用 HTTPS，请执行下列步骤：

### <a name="step-1-disable-the-feature"></a>步骤 1：禁用该功能 

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到 Verizon 标准或高级 CDN 配置文件。

2. 在终结点的列表中，单击包含自定义域的终结点。

3. 单击要禁用 HTTPS 的自定义域。

    ![自定义域列表](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. 单击“禁用”**上**禁用 HTTPS，然后单击“应用”。

    ![“自定义 HTTPS”对话框](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>步骤 2：等待传播

禁用自定义域 HTTPS 功能后，最多可能需要 6-8 小时才会生效。 此过程完成后，Azure 门户中的自定义 HTTPS 状态会设置为“已禁用”，且自定义域对话框中的三个操作步骤会标记为完成。 自定义域不再能够使用 HTTPS。

![禁用 HTTPS 对话框](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>操作进程

下表显示在禁用 HTTPS 时发生的操作进程。 禁用 HTTPS 后，自定义域对话框中将出现三个操作步骤。 每个步骤变为活动状态时，其他详细信息将显示在相应步骤下。 步骤成功完成后，它旁边会显示一个绿色的复选标记。 

| 操作进程 | 操作详细信息 | 
| --- | --- |
| 1 个提交请求 | 提交请求 |
| 2 取消设置证书 | 删除证书 |
| 3 个完成 | 已删除证书 |

## <a name="frequently-asked-questions"></a>常见问题

1. *谁是证书提供商？使用哪种类型的证书？*

    Microsoft 使用 DigiCert 提供的使用者可选名称 (SAN) 证书。 SAN 证书可以使用一个证书保护多个完全限定域名。

2. *能否使用我的专用证书？*
    
    目前不能，但它在规划之中。

3. *如果我未收到 DigiCert 发来的域验证电子邮件，怎么办？*

    如果未在 24 小时内收到电子邮件，请与 Microsoft 支持部门联系。 如果自定义域的 CNAME 条目直接指向终结点主机名（并且你未使用 cdnverify 子域名称），则你不会收到域验证电子邮件。 验证会自动进行。

4. *使用 SAN 证书是否没有使用专用证书安全？*
    
    SAN 证书遵循与专用证书相同的加密和安全标准。 所有颁发的 SSL 证书都使用 SHA-256 来增强服务器安全性。

5. *能否将自定义域 HTTPS 用于 Akamai 的 Azure CDN？*

    目前，只有 Verizon 的 Azure CDN 提供此功能。 Microsoft 正在努力在未来几个月内对 Akamai 的 Azure CDN 支持此功能。

6. *我是否需要通过我的 DNS 提供商获得证书颁发机构授权记录？*

    否，当前不需要证书颁发机构授权记录。 但是，如果你确实有一个，则必须包含 DigiCert 作为一个有效的 CA。


## <a name="next-steps"></a>后续步骤

- 了解如何[在Azure CDN 终结点上设置自定义域](./cdn-map-content-to-custom-domain.md)


