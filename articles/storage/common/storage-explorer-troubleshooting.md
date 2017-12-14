---
title: "Azure 存储资源管理器疑难解答指南 | Microsoft Docs"
description: "Azure 两个调试功能的概述"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: 3187939fa813f941c2fe12a359df474a6c487c71
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure 存储资源管理器疑难解答指南

Microsoft Azure 存储资源管理器（预览版）是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。 应用可连接到托管在 Azure、Sovereign Clouds 和 Azure Stack 上的存储帐户。

本指南汇总了存储资源管理器中常见问题的解决方案。

## <a name="sign-in-issues"></a>登录问题

仅支持 Azure Active Directory (AAD) 帐户。 如果使用 ADFS 帐户，预计将无法正常登录存储资源管理器。 继续之前，请尝试重新启动应用程序，看问题是否能够解决。

### <a name="error-self-signed-certificate-in-certificate-chain"></a>错误：证书链中的自签名证书

几个原因会导致该错误，其中最常见的两个如下：

1. 应用通过“透明代理”连接，这意味着服务器（如公司服务器）在拦截 HTTPS 流量、对其解密，然后使用自签名证书对其加密。

2. 正在运行的应用程序（如防病毒软件）在向收到的 HTTPS 消息注入自签名 SSL 证书。

若存储资源管理器遇到这些问题，将无法再判断收到的 HTTPS 消息是否被篡改。 若拥有一份自签名证书的副本，可让存储资源管理器信任它。 若无法确定谁在注入证书，请执行以下步骤找到它：

1. 安装 Open SSL

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html)（任意轻量版本应该都满足要求）

    - Mac 和 Linux：应包含在操作系统中

2. 运行 Open SSL

    - Windows：打开安装目录，单击“/bin/”，然后双击“openssl.exe”。
    - Mac 和 Linux：从终端运行“openssl”。

3. 执行 s_client -showcerts -connect microsoft.com:443

4. 查找自签名证书。 若不确定哪个证书为自签名，请查找使用者（“s:”）和证书颁发者（“i:”）相同的任意位置。

5. 找到任何自签名证书后，将每个证书中从“-----BEGIN CERTIFICATE-----”（含）到“-----END CERTIFICATE-----”（含）的部分复制和粘贴到新的 .cer 文件。

6. 打开存储资源管理器，单击“编辑” > “SSL 证书” > “导入证书”，然后使用文件选取器查找、选择和打开创建的 .cer 文件。

若通过上述步骤无法找到任何自签名证书，请通过反馈工具联系我们以获取更多帮助。

### <a name="unable-to-retrieve-subscriptions"></a>无法检索订阅

若成功登录后无法检索订阅，请执行以下步骤解决此问题：

- 通过登录 Azure 门户验证帐户是否有权访问该订阅。

- 请确保使用正确的环境登录（Azure、Azure 中国、Azure 德国、Azure 美国政府或自定义环境/Azure Stack）。

- 如果使用代理，请确保已正确配置存储资源管理器代理。

- 尝试移除并重新添加帐户。

- 尝试从根目录（即 C:\Users\ContosoUser）删除以下文件，然后添加帐户：

    - .adalcache

    - .devaccounts

    - .extaccounts

- 登录查询任何错误信息时，请查看开发人员工具控制台（按 F12）：

![开发人员工具](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>无法看到身份验证页面

若无法看到身份验证页面，请执行以下步骤解决此问题：

- 根据连接速度不同，加载登录页面可能需要一会儿，请至少等待一分钟再关闭身份验证对话框。

- 如果使用代理，请确保已正确配置存储资源管理器代理。

- 按 F12 键查看开发人员控制台。 从开发人员控制台查看响应，查能否找到身份验证不起作用的任何线索。

### <a name="cannot-remove-account"></a>无法移除帐户

如果无法移除帐户，或重新验证链接不起作用，请执行以下步骤解决此问题：

- 尝试从根目录删除以下文件，然后重新添加帐户：

    - .adalcache

    - .devaccounts

    - .extaccounts

- 若要删除附加了 SAS 的存储资源，请删除以下文件：

    - Windows 中的 %AppData%/StorageExplorer 文件夹

    - Mac 中的 /Users/<your_name>/Library/Applicaiton SUpport/StorageExplorer

    - Linux 中的 ~/.config/StorageExplorer

> [!NOTE]
>  删除这些文件之后，需要重新输入所有凭据。

## <a name="proxy-issues"></a>代理问题

首先，请确保输入的以下信息全部正确：

- 代理 URL 和端口号

- 用户名和密码（若代理需要）

### <a name="common-solutions"></a>常见解决方法

如果还有问题，请执行以下步骤解决：

- 若能不使用代理连接到 Internet，请验证存储资源管理器在不启用代理设置的情况下是否工作。 若是，则可能是代理设置出错。 请与代理管理员一起确定问题。

- 验证其他使用该代理服务器的应用程序是否按预期运行。

- 验证能否使用 Web 浏览器连接到 Microsoft Azure 门户。

- 验证能否从服务终结点接收响应。 在浏览器中输入一个终结点 URL。 若能连接，会收到 InvalidQueryParameterValue 或类似的 XML响应。

- 如果其他人也通过你的代理服务器使用存储资源管理器，请验证他们能否连接。 若能，则你可能需要联系代理服务器管理员。

### <a name="tools-for-diagnosing-issues"></a>诊断问题的工具

若有网络服务工具（如 Fiddler for Windows），或许能够按以下操作诊断问题：

- 如果必须通过代理工作，则须将网络工具配置为通过代理连接。

- 检查网络工具使用的端口号。

- 在存储资源管理器中，输入本地主机 URL和网络工具的端口号作为代理设置。 如果操作无误，网络工具将开始记录存储资源管理器向管理和服务终结点发出的网络请求。 例如，如果在浏览器中输入 https://cawablobgrs.blob.core.windows.net/ 作为 Blob 终结点，将收到类似以下的响应，表示资源存在，尽管无法访问。

![代码示例](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>联系代理服务器管理员

如果代理设置正确，则可能需要联系代理服务器管理员，并

- 确保代理不会阻止流向 Azure 管理或资源终结点的流量。

- 验证代理服务器使用的身份验证协议。 目前存储资源管理器不支持 NTLM 代理。

## <a name="unable-to-retrieve-children-error-message"></a>“无法检索子级”错误消息

如果通过代理连接到 Azure，请验证代理设置是否正确。 如果从订阅或帐户所有者处获得资源的访问权，请验证是否具有该资源的读取或列出权限。

### <a name="issues-with-sas-url"></a>SAS URL 的问题
若正使用 SAS URL连接到服务，且遇到此错误：

- 验证该 URL 是否提供读取和列出资源的必需权限。

- 验证该 URL 是否未过期。

- 如果 SAS URL建立在访问策略基础上，请验证该访问策略是否被撤销。

如果意外附加了无效的 SAS URL，并且无法分离，请执行以下步骤：
1.  运行存储资源管理器时，按 F12 打开开发人员工具窗口。
2.  单击“应用程序”选项卡，然后单击左侧树中的“本地存储”> file://。
3.  查找与有问题的 SAS URI 服务类型关联的键。 例如，如果用于 blob 容器的 SAS URI 错误，请查找名为“StorageExplorer_AddStorageServiceSAS_v1_blob”的键。
4.  键的值应为 JSON 数组。 查找与错误 URI 关联的对象，并将其删除。
5.  按 Ctrl+R 重新加载存储资源管理器。


## <a name="next-steps"></a>后续步骤

如果以上解决方案均不起作用，可以通过反馈工具提交问题，并提供电子邮件和尽可能多的问题详细信息，以便我们联系你解决问题。

为此，请单击“帮助”菜单，然后单击“发送反馈”。

![反馈](./media/storage-explorer-troubleshooting/4022503_en_1.png)
