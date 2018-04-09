---
title: 在 Azure 存储资源管理器中管理 Azure Cosmos DB
description: 了解如何在 Azure 存储资源管理器中管理 Azure Cosmos DB。
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>存储资源管理器中的 Azure Cosmos DB 故障排除指南概述

[Azure 存储资源管理器中的 Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) 是一个独立的应用，用于连接到 Azure Cosmos DB 帐户，而该帐户托管在 Windows、macOS 或 Linux 版本的 Azure 和主权云上。 它可以用来管理 Azure Cosmos DB 实体、操作数据、更新存储过程和触发器以及其他 Azure 实体（如存储 Blob 和队列）。

本指南汇总了存储资源管理器中 Azure Cosmos DB 的常见问题的解决方案。

- [登录问题](#Sign-in-issues)
  - [证书链中的自签名证书](#Self-signed-certificate-in-certificate-chain)
  - [无法检索订阅](#Unable-to-retrieve-subscriptions)
  - [无法看到身份验证页面](#Unable-to-see-the-authentication-page)
  - [无法移除帐户](#Cannot-remove-account)
- [Http/Https 代理问题](#Http/Https-proxy-issue)
- [“本地和附加”节点下的“开发”节点问题](#Development-node-under-Local-and-Attached-node-issue)
- [在“本地和附加”节点中附加 Azure Cosmos DB 帐户错误](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [展开 Azure Cosmos DB 节点错误](#Expand-Azure-Cosmos-DB-node-error)
- [后续步骤](#Next-steps)

<h2 id="Sign-in-issues">登录问题</h2>

继续之前，请尝试重启应用程序，看问题是否能够解决。

<h2 id="Self-signed-certificate-in-certificate-chain">证书链中的自签名证书</h2>

出现此错误有多个原因，最常见的两个原因是：

1. 你位于“透明代理”之后，这意味着某人（例如 IT 部门）在拦截 HTTPS 流量，将其解密后又使用自签名证书对其加密。

2. 正在运行的软件（如防病毒软件）在向收到的 HTTPS 消息注入自签名 SSL 证书。

存储资源管理器在遇到其中一个这样的“自签名证书”时，将再也无法判断收到的 HTTPS 消息是否已被篡改。 但若拥有一份自签名证书的副本，则可让存储资源管理器信任它。 若无法确定谁在注入证书，则可自行尝试通过以下步骤找到它：

1. 安装 Open SSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html)（任意轻量版本均可）
     - Mac 和 Linux：应包含在操作系统中
2. 运行 Open SSL
    - Windows：转到安装目录，然后转到“/bin/”，然后双击“openssl.exe”。
    - Mac 和 Linux：从终端执行“openssl”
3. 执行 `s_client -showcerts -connect microsoft.com:443`
4. 查找自签名证书。 若不确定哪个证书为自签名，则请查找使用者（“s:”）和证书颁发者（“i:”）相同的任意位置。
5.  找到任何自签名证书后，将每个证书中从“-----BEGIN CERTIFICATE-----”（含）到“-----END CERTIFICATE-----”（含）的部分复制和粘贴到新的 .cer 文件。
6.  打开存储资源管理器，然后转到“编辑” > “SSL 证书” > “导入证书”。 使用文件选取器查找、选择和打开所创建的 .cer 文件。

若通过上述步骤无法找到任何自签名证书，可通过发送反馈以获取更多帮助。

<h2 id="Unable-to-retrieve-subscriptions">无法检索订阅</h2>

若成功登录后无法检索订阅，请执行以下操作：

- 通过登录 [Azure 门户](http://portal.azure.com/)验证帐户是否有权访问该订阅
- 请确保使用正确的环境登录（[Azure](http://portal.azure.com/)、[Azure 中国](https://portal.azure.cn/)、[Azure 德国](https://portal.microsoftazure.de/)、[Azure 美国政府](http://portal.azure.us/)或自定义环境/Azure Stack）
- 如果使用代理，请确保已正确配置存储资源管理器代理
- 尝试移除并重新添加帐户
- 尝试从主目录（例如：C:\Users\ContosoUser）删除以下文件，然后重新添加帐户：
  - .adalcache
  - .devaccounts
  - .extaccounts
- 登录查询任何错误消息时，请查看开发人员工具控制台（F12）

![console](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">无法看到身份验证页面</h2>  

如果无法看到身份验证页面：

- 根据连接速度不同，加载登录页面可能需要一会儿，请至少等待一分钟再关闭身份验证对话框
- 如果使用代理，请确保已正确配置存储资源管理器代理
- 按 F12 键显示开发人员控制台。 从开发人员控制台查看响应，看能否找到身份验证不起作用的任何线索

<h2 id="Cannot-remove-account">无法移除帐户</h2>

如果无法移除帐户，或重新验证链接不起作用

- 尝试从主目录删除以下文件，然后重新添加帐户：
  - .adalcache
  - .devaccounts
  - .extaccounts
- 若要删除附加了 SAS 的存储资源，请删除：
  - Windows 中的 %AppData%/StorageExplorer 文件夹
  - Mac 中的 /Users/<your_name>/Library/Applicaiton SUpport/StorageExplorer
  - Linux 中的 ~/.config/StorageExplorer
  - 删除这些文件之后，**需要重新输入所有凭据**


<h2 id="Http/Https-proxy-issue">Http/Https 代理问题</h2>

在 ASE 中配置 http/https 代理时，无法列出左侧树中的 Azure Cosmos DB 节点。 这是一个已知问题，下一版本会修复此问题。 目前可以在 Azure 门户中使用 Azure Cosmos DB 数据资源管理器作为解决方法。 

<h2 id="Development-node-under-Local-and-Attached-node-issue">“本地和附加”节点下的“开发”节点问题</h2>

在左侧树中单击“本地和附加”节点下的“开发”节点时没有响应。  此行为是预期的行为。 下一版本会支持 Azure Cosmos DB 本地模拟器。

![开发节点](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">在“本地和附加”节点中附加 Azure Cosmos DB 帐户错误</h2>

如果在“本地和附加”节点中附加 Azure Cosmos DB 帐户时看到以下错误，则请检查是否使用了正确的连接字符串。

![在“本地和附加”中附加 Azure Cosmos DB 错误](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">展开 Azure Cosmos DB 节点错误</h2>

尝试展开左侧的树节点时可能会看到以下错误。 

![展开错误](./media/troubleshoot-cosmosdb/expand-error.png)

请尝试以下建议：

- 检查 Azure Cosmos DB 帐户是否正在进行预配，然后在帐户成功创建以后再次进行尝试。
- 如果帐户位于“快速访问”节点或“本地和附加”节点下，则请检查该帐户是否已删除。 如果是这样，则需手动删除节点。

<h2 id="Next-steps">后续步骤</h2>

如果以上解决方案均不起作用，请向 Azure Cosmos DB 开发人员工具团队发送电子邮件 ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com))，对问题进行详细说明，以便解决问题。





