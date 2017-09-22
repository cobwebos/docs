---
title: "在 Azure 内容交付网络自定义域上启用或禁用 HTTPS | Microsoft Docs"
description: "了解如何在具有自定义域的 Azure CDN 终结点上启用或禁用 HTTPS。"
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c92f1e20acf55b8bd791fad43f17e162a5cb3847
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="enable-or-disable-https-on-an-azure-content-delivery-network-custom-domain"></a>在 Azure 内容交付网络自定义域上启用或禁用 HTTPS

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

利用对 Microsoft Azure 内容交付网络 (CDN) 自定义域的 HTTPS 支持，可以通过 SSL 使用自己的域名传送安全内容以提高在传输过程中数据的安全性。 通过一键式启用、完整的证书管理可简化为自定义域启用 HTTPS 的端到端工作流，所有这些都不会增加成本。

在传输过程中确保所有 Web 应用程序敏感数据的隐私和数据完整性至关重要。 使用 HTTPS 协议可确保通过 Internet 发送敏感数据时对其进行加密。 它提供信任、身份验证并保护 Web 应用程序免受攻击。 目前，Azure CDN 在 CDN 终结点上支持 HTTPS。 例如，如果从 Azure CDN（例如 https://contoso.azureedge.net）创建 CDN 终结点，HTTPS 默认启用。 现在，使用自定义域 HTTPS，可以为自定义域（例如 https://www.contoso.com）启用安全交付。 

HTTPS 功能的一些关键属性包括：

- 无需额外付费：证书获取或续订不收取费用，对于 HTTPS 流量不另外收费。 只需为从 CDN 出口的 GB 数付费。

- 简单启用：可从 [Azure 门户](https://portal.azure.com)进行一键式预配。 还可以使用 REST API 或其他开发人员工具启用该功能。

- 完整的证书管理：处理所有证书获取和管理。 在证书过期之前自动预配并续订证书。 这会完全消除由于证书过期而导致服务中断的风险。

>[!NOTE] 
>在启用 HTTPS 支持前，必须已建立 [Azure CDN 自定义域](./cdn-map-content-to-custom-domain.md)。

## <a name="enabling-https"></a>启用 HTTPS

若要启用 HTTPS，请执行下列步骤：

### <a name="step-1-enable-the-feature"></a>步骤 1：启用该功能 

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到 Verizon 标准或高级 CDN 配置文件。

2. 在终结点的列表中，单击包含自定义域的终结点。

3. 单击要启用 HTTPS 的自定义域。

    ![“终结点”边栏选项卡](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. 单击“打开”以启用 HTTPS，然后单击“应用”。

    ![“自定义 HTTPS”对话框](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>步骤 2：验证域

>[!IMPORTANT] 
>必须先完成域验证，才能在自定义域上激活 HTTPS。 可在 6 个工作日内批准域。 将自动取消 6 个工作日内未批准的请求。 

在自定义域上启用 HTTPS 后，我们的 HTTPS 证书提供商 DigiCert 会根据域的 [WHOIS](http://whois.domaintools.com/) 注册者信息，通过联系域的注册者来验证域的所有权。 通过 WHOIS 注册中列出的电子邮件地址（默认）或电话号码进行联系。 

![WHOIS 记录](./media/cdn-custom-ssl/whois-record.png)

此外，DigiCert 还会将验证电子邮件发送到以下地址。 如果 WHOIS 注册信息是隐私，请验证是否可直接从以下任一地址进行审批：

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

应会在几分钟内收到如以下所示的电子邮件，要求你批准请求。 如果使用垃圾邮件筛选，请将 admin@digicert.com 添加到允许列表。 如果未在 24 小时内收到电子邮件，请与 Microsoft 支持部门联系。
    
![“自定义 HTTPS”对话框](./media/cdn-custom-ssl/domain-validation-email.png)

单击批准链接时，会定向到以下在线批准表： 
    
![“自定义 HTTPS”对话框](./media/cdn-custom-ssl/domain-validation-form.png)

按表中的说明操作；有两种验证选项：

- 可批准通过同一根域（例如 consoto.com）的同一帐户下的所有将来订单。如果计划在将来为同一根域添加其他自定义域，这是建议的方法。

- 可以只批准该请求中使用的特定主机名。 后续请求将需要其他批准。

批准后，DigiCert 会将自定义域名添加到 使用者可选名称 (SAN) 证书。 该证书会在一年内有效，并会在过期前自动续订。

### <a name="step-3-wait-for-propagation"></a>步骤 3：等待传播

验证域名后，将需要长达 6-8 小时才能使自定义域 HTTPS 功能激活。 此过程完成后，Azure 门户中的“自定义 HTTPS”状态会设置为“已启用”，且自定义域 HTTPS 边栏选项卡中的四个操作步骤会标记为完成。 自定义域现可使用 HTTPS。

![启用 HTTPS 对话框](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>操作进程

下表显示启用 HTTPS 时出现的操作进程。 启用 HTTPS 后，自定义域 HTTPS 边栏选项卡中将出现四个操作步骤。 每个步骤变为活动状态时，其下将随之显示额外的详细信息。 步骤成功完成后，它旁边会显示一个绿色的复选标记。 

| 操作步骤 | 操作步骤详细信息 | 
| --- | --- |
| 1 个提交请求 | 提交请求 |
| | 正在提交 HTTPS 请求。 |
| | 已成功提交 HTTPS 请求。 |
| 2 个域验证 | 已向你发送电子邮件，要求你验证域所有权。 正在等待确认。 |
| | 已成功验证域所有权。 |
| | 域所有权验证请求已过期（很可能是客户在 6 天内未响应）。 将不会在域中启用 HTTPS。 * |
| | 客户已拒绝域所有权验证请求。 将不会在域中启用 HTTPS。 * |
| 3 设置证书 | 证书颁发机构当前正在颁发在你的域中启用 HTTPS 所需的证书。 |
| | 证书已颁发，当前正将证书部署到 CDN 网络。 最多可能需要 6 小时才能完成此操作。 |
| | 已成功将证书部署到 CDN 网络。 |
| 4 个完成 | 已成功在域中启用 HTTPS。 |

\* 除非出现错误，否则不会显示此消息。 

如果提交请求之前出现错误，将看到以下错误消息：

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>禁用 HTTPS

启用 HTTPS 后，可以将其禁用。 若要禁用 HTTPS，请执行下列步骤：

### <a name="step-1-disable-the-feature"></a>步骤 1：禁用该功能 

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到 Verizon 标准或高级 CDN 配置文件。

2. 在终结点的列表中，单击包含自定义域的终结点。

3. 单击要禁用 HTTPS 的自定义域。

    ![“终结点”边栏选项卡](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. 单击“禁用”**上**禁用 HTTPS，然后单击“应用”。

    ![“自定义 HTTPS”对话框](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>步骤 2：等待传播

禁用自定义域 HTTPS 功能后，最多可能需要 6-8 小时才会生效。 此过程完成后，Azure 门户中的“自定义 HTTPS”状态会设置为“已禁用”，且自定义域 HTTPS 边栏选项卡中的三个操作步骤会标记为完成。 自定义域不再能够使用 HTTPS。

![禁用 HTTPS 对话框](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>操作进程

下表显示在禁用 HTTPS 时发生的操作进程。 禁用 HTTPS 后，自定义域 HTTPS 边栏选项卡中将出现三个操作步骤。 每个步骤变为活动状态时，其他详细信息将显示在相应步骤下。 步骤成功完成后，它旁边会显示一个绿色的复选标记。 

| 操作进程 | 操作详细信息 | 
| --- | --- |
| 1 个提交请求 | 提交请求 |
| 2 取消设置证书 | 删除证书 |
| 3 个完成 | 已删除证书 |

## <a name="frequently-asked-questions"></a>常见问题

1. *谁是证书提供商？使用哪种类型的证书？*

    我们使用 DigiCert 提供的使用者备用名称 (SAN) 证书。 SAN 证书可以使用一个证书保护多个完全限定域名。

2. *能否使用我的专用证书？*
    
    目前不能，但它在规划之中。

3. *如果我未收到 DigiCert 发来的域验证电子邮件，怎么办？*

    如果未在 24 小时内收到电子邮件，请与 Microsoft 支持部门联系。

4. *使用 SAN 证书是否没有使用专用证书安全？*
    
    SAN 证书遵循与专用证书相同的加密和安全标准。所有颁发的 SSL 证书都使用 SHA-256 来增强服务器安全性。

5. *能否将自定义域 HTTPS 用于 Akamai 的 Azure CDN？*

    目前，只有 Verizon 的 Azure CDN 提供此功能。 我们正在努力在未来几个月内对 Akamai 的 Azure CDN 支持此功能。


## <a name="next-steps"></a>后续步骤

- 了解如何[在Azure CDN 终结点上设置自定义域](./cdn-map-content-to-custom-domain.md)



