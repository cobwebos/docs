---
title: 安全代码最佳做法
titleSuffix: Azure Machine Learning
description: 了解在针对 Azure 机器学习进行开发时可能存在的潜在安全威胁。 了解 Azure ML 提供的缓解措施，以及确保开发环境始终安全的最佳做法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 4bc9a982f6ce77b803a3ba91e050bcda9ec74fed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728516"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>使用 Azure 机器学习的安全代码最佳做法

在 Azure 机器学习中，可以从任何源将文件和内容上传到 Azure 中。 加载的 Jupyter 笔记本或脚本中的内容可能会从会话中读取数据，在 Azure 中访问组织中的数据，或代表你运行恶意进程。

> [!IMPORTANT]
> 仅从受信任的源运行笔记本或脚本。 例如，你或你的安全团队已查看其中的笔记本或脚本。

## <a name="potential-threats"></a>潜在威胁

使用 Azure 机器学习进行开发通常涉及基于 Web 的开发环境（Notebooks 和 Azure 机器学习工作室）。 使用基于 Web 的开发环境时，潜在威胁包括：

* [跨站点脚本 (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __DOM 注入__：此类攻击可以修改浏览器中显示的 UI。 例如，更改“运行”按钮在 Jupyter Notebook 中的行为方式。
    * __访问令牌/Cookie__：XSS 攻击还可以访问本地存储和浏览器 Cookie。 你的 Azure Active Directory (AAD) 身份验证令牌存储在本地存储中。 XSS 攻击可以使用此令牌代表你进行 API 调用，然后将数据发送到外部系统或 API。

* [跨站点请求伪造 (CSRF)](https://owasp.org/www-community/attacks/csrf)：此攻击可能会将图像或链接的 URL 替换为恶意脚本或 API 的 URL。 加载图像或单击链接时，会调用该 URL。

## <a name="azure-ml-studio-notebooks"></a>Azure 机器学习工作室笔记本

Azure 机器学习工作室在浏览器中提供托管的笔记本体验。 笔记本中的单元格可输出包含恶意代码的 HTML 文档或片段。  呈现输出时，可以执行该代码。

__可能的威胁__：
* 跨站点脚本 (XSS)
* 跨站点请求伪造 (CSRF)

__由 Azure 机器学习提供的缓解措施__：
* __代码单元格输出__在 iframe 中进行沙盒处理。 iframe 会阻止脚本访问父 DOM、Cookie 或会话存储。
* 使用 dompurify 库清理 __Markdown 单元格__内容。 这会阻止恶意脚本在呈现 Markdown 单元格的过程中执行。
* __图像 URL__ 和 __Markdown 链接__会发送到 Microsoft 拥有的终结点，该终结点会检查其中是否存在恶意值。 如果检测到恶意值，终结点会拒绝该请求。

__建议的操作__：
* 确保你信任文件的内容，然后再将其上传到工作室。 上传时，必须确认你上传的是受信任的文件。
* 选择用于打开外部应用程序的链接时，系统会提示你信任该应用程序。

## <a name="azure-ml-compute-instance"></a>Azure 机器学习计算实例

Azure 机器学习计算实例托管 __Jupyter__ 和 __Jupyter Lab__。 使用其中的一个时，笔记本中的单元格或笔记本中的代码可输出包含恶意代码的 HTML 文档或片段。 呈现输出时，可以执行该代码。 使用在计算实例上托管的 __RStudio__ 时，也存在相同的威胁。

__可能的威胁__：
* 跨站点脚本 (XSS)
* 跨站点请求伪造 (CSRF)

__由 Azure 机器学习提供的缓解措施__：
* 无。 Jupyter 和 Jupyter Lab 是在 Azure 机器学习计算实例上托管的开源应用程序。

__建议的操作__：
* 确保你信任文件的内容，然后再将其上传到工作室。 上传时，必须确认你上传的是受信任的文件。

## <a name="report-security-issues-or-concerns"></a>报告安全问题或疑虑 

Azure 机器学习符合 Microsoft Azure 悬赏计划的要求。 有关详细信息，请访问 [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure)。

## <a name="next-steps"></a>后续步骤

* [Azure 机器学习的企业安全性](concept-enterprise-security.md)