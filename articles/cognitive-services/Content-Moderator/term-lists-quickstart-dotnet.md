---
title: 通过 Azure 内容审查器中的自定义术语列表进行审查 | Microsoft Docs
description: 如何通过自定义术语列表使用适用于 .NET 的 Azure 内容审查器进行审查。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/11/2018
ms.author: sajagtap
ms.openlocfilehash: 6da72ad070d9c3a6be38e24626dff77b52fed852
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365549"
---
# <a name="moderate-with-custom-term-lists-in-net"></a>通过 .NET 中的自定义术语列表进行审查

Azure 内容审查器中的默认全局术语列表足以满足大多数内容审查需求。 但是，可能需要屏蔽特定于组织的术语。 例如，可能需要标记竞争对手的名称作进一步审查。 

可使用适用于 .NET 的内容审查器 SDK 创建供文本审查 API 使用的自定义术语列表。

> [!NOTE]
> 最大限制为“5 个术语列表”，每个列表“不超过 10,000 条术语”。
>

本文提供了信息和代码示例，帮助你开始使用适用于 .NET 的内容审查器 SDK 执行以下操作：
- 创建列表。
- 向列表添加术语。
- 针对列表中的术语屏蔽术语。
- 从列表中删除术语。
- 删除列表。
- 编辑列表信息。
- 刷新索引，以便新的扫描中包括对列表的更改。

本文假定你已熟悉 Visual Studio 和 C#。

## <a name="sign-up-for-content-moderator-services"></a>注册内容审查器服务

需要有订阅密钥才能通过 REST API 或 SDK 使用内容审查器服务。

在内容审查器仪表板中，可以在“设置” > “凭据” > “API” > “试用 Ocp-Apim-Subscription-Key”中查找订阅密钥。 有关详细信息，请参阅[概述](overview.md)。

## <a name="create-your-visual-studio-project"></a>创建 Visual Studio 项目

1. 向解决方案添加新的“控制台应用 (.NET Framework)”项目。

1. 将该项目命名为 TermLists。 选择此项目作为解决方案的单个启动项目。

1. 向在[内容审查器客户端帮助程序快速入门](content-moderator-helper-quickstart-dotnet.md)中创建的“ModeratorHelper”项目添加一个引用。

### <a name="install-required-packages"></a>安装所需程序包

为 TermLists 项目安装以下 NuGet 包：

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>更新程序的 using 语句

修改程序的 using 语句。

    using System;
    using System.Threading;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;

### <a name="add-private-properties"></a>添加私有属性

将以下私有属性添加到命名空间 TermLists 的 Program 类。

    /// <summary>
    /// The language of the terms in the term lists.
    /// </summary>
    private const string lang = "eng";

    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Content Moderator APIs.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of minutes to delay after updating the search index before
    /// performing image match operations against a the list.
    /// </summary>
    private const double latencyDelay = 0.5;

## <a name="create-a-term-list"></a>创建术语列表

使用 ContentModeratorClient.ListManagementTermLists.Create 创建术语列表。 要创建的第一个参数是一个包含 MIME 类型的字符串，此类型应为“application/json”。 有关详细信息，请参阅 [API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)。 第二个参数是 Body 对象，该对象包含新术语列表的名称和说明。

将以下方法定义添加到 TermLists 命名空间中的 Program 类。

> [!NOTE]
> 内容审查器服务密钥有一个每秒请求数 (RPS) 速率限制，如果超过该限制，SDK 会引发错误代码为 429 的异常。 
>
> 免费层密钥有一个 RPS 速率限制。

    /// <summary>
    /// Creates a new term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <returns>The term list ID.</returns>
    static string CreateTermList (ContentModeratorClient client)
    {
        Console.WriteLine("Creating term list.");

        Body body = new Body("Term list name", "Term list description");
        TermList list = client.ListManagementTermLists.Create("application/json", body);
        if (false == list.Id.HasValue)
        {
            throw new Exception("TermList.Id value missing.");
        }
        else
        {
            string list_id = list.Id.Value.ToString();
            Console.WriteLine("Term list created. ID: {0}.", list_id);
            Thread.Sleep(throttleRate);
            return list_id;
        }
    }

## <a name="update-term-list-name-and-description"></a>更新术语列表名称和说明

使用 ContentModeratorClient.ListManagementTermLists.Update 更新术语列表信息。 要更新的第一个参数是术语列表 ID。 第二个参数是应为“application/json”的 MIME 类型。 有关详细信息，请参阅 [API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685)。 第三个参数是 Body 对象，它包含新名称和说明。

将以下方法定义添加到 TermLists 命名空间中的 Program 类。

    /// <summary>
    /// Update the information for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="name">The new name for the term list.</param>
    /// <param name="description">The new description for the term list.</param>
    static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
    {
        Console.WriteLine("Updating information for term list with ID {0}.", list_id);
        Body body = new Body(name, description);
        client.ListManagementTermLists.Update(list_id, "application/json", body);
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-term-to-a-term-list"></a>向术语列表添加术语

将以下方法定义添加到 TermLists 命名空间中的 Program 类。

    /// <summary>
    /// Add a term to the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="term">The term to add to the term list.</param>
    static void AddTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
        client.ListManagementTerm.AddTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

## <a name="get-all-terms-in-a-term-list"></a>获取术语列表中的所有术语

将以下方法定义添加到 TermLists 命名空间中的 Program 类。

    /// <summary>
    /// Get all terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to get all terms.</param>
    static void GetAllTerms(ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
        Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
        TermsData data = terms.Data;
        foreach (TermsInList term in data.Terms)
        {
            Console.WriteLine(term.Term);
        }
        Thread.Sleep(throttleRate);
    }

## <a name="add-code-to-refresh-the-search-index"></a>添加代码以刷新搜索索引

对术语列表进行更改后，刷新其搜索索引，使更改在下次使用术语列表时包含在内。 此步骤类似于桌面上的搜索引擎（如果启用）或 Web 搜索引擎的操作，即不断刷新其索引以包含新文件或页面。

使用 ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod 刷新术语列表搜索索引。

将以下方法定义添加到 TermLists 命名空间中的 Program 类。

    /// <summary>
    /// Refresh the search index for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to refresh.</param>
    static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
        client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
        Thread.Sleep((int)(latencyDelay * 60 * 1000));
    }

## <a name="screen-text-using-a-term-list"></a>屏蔽使用术语列表的文本

通过 ContentModeratorClient.TextModeration.ScreenText 屏蔽使用术语列表的文本，它将采用以下参数。

- 术语列表中的术语所采用的语言。
- MIME 类型，可以是“text/html”、“text/xml”、“text/markdown”或“text/plain”。
- 要屏蔽的文本。
- 布尔值。 将此字段设置为 true，在屏蔽它之前自动更正文本。
- 布尔值。 将此字段设置为 true，检测文本中的个人身份信息 (PII)。
- 术语列表 ID。

有关详细信息，请参阅 [API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)。

ScreenText 返回 Screen 对象，该对象具有 Terms 属性，此属性可列出内容审查器在屏蔽期间检测到的任何术语。 请注意，如果屏蔽期间内容审查器未检测到任何术语，则 Terms 属性的值为 null。

将以下方法定义添加到 TermLists 命名空间中的 Program 类。

    /// <summary>
    /// Screen the indicated text for terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to use to screen the text.</param>
    /// <param name="text">The text to screen.</param>
    static void ScreenText (ContentModeratorClient client, string list_id, string text)
    {
        Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
        Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
        if (null == screen.Terms)
        {
            Console.WriteLine("No terms from the term list were detected in the text.");
        }
        else
        {
            foreach (DetectedTerms term in screen.Terms)
            {
                Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
            }
        }
        read.Sleep(throttleRate);
    }

## <a name="delete-terms-and-lists"></a>删除术语和列表

删除术语或列表非常简单。 使用 SDK 执行以下任务：

- 删除术语。 (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- 删除列表中的所有术语而不删除列表。 (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- 删除列表及其所有内容。 (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>删除术语

将以下方法定义添加到 TermLists 命名空间中的 Program 类。

    /// <summary>
    /// Delete a term from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete the term.</param>
    /// <param name="term">The term to delete.</param>
    static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
        client.ListManagementTerm.DeleteTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-all-terms-in-a-term-list"></a>删除术语列表中的所有术语

将以下方法定义添加到 TermLists 命名空间中的 Program 类。

    /// <summary>
    /// Delete all terms from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete all terms.</param>
    static void DeleteAllTerms (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
        client.ListManagementTerm.DeleteAllTerms(list_id, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-a-term-list"></a>删除术语列表

将以下方法定义添加到 TermLists 命名空间中的 Program 类。

    /// <summary>
    /// Delete the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to delete.</param>
    static void DeleteTermList (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Deleting term list with ID {0}.", list_id);
        client.ListManagementTermLists.Delete(list_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>汇总

将 Main 方法定义添加到 TermLists 命名空间中的 Program 类。 最后，关闭 Program 类和 TermLists 命名空间。

    static void Main(string[] args)
    {
        using (var client = Clients.NewClient())
        {
            string list_id = CreateTermList(client);

            UpdateTermList(client, list_id, "name", "description");
            AddTerm(client, list_id, "term1");
            AddTerm(client, list_id, "term2");

            GetAllTerms(client, list_id);

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            string text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteTerm(client, list_id, "term1");

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteAllTerms(client, list_id);
            DeleteTermList(client, list_id);

            Console.WriteLine("Press ENTER to close the application.");
            Console.ReadLine();
        }
    }

## <a name="run-the-application-to-see-the-output"></a>运行应用程序以查看输出

输出将类似以下行，但数据可能不同。

    Creating term list.
    Term list created. ID: 252.
    Updating information for term list with ID 252.
    
    Adding term "term1" to term list with ID 252.
    Adding term "term2" to term list with ID 252.
    
    Getting terms in term list with ID 252.
    term1
    term2
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term1" from list ID 252 at index 32.
    Found term: "term2" from list ID 252 at index 46.
    
    Removed term "term1" from term list with ID 252.
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term2" from list ID 252 at index 46.
    
    Removing all terms from term list with ID 252.
    Deleting term list with ID 252.
    Press ENTER to close the application.
    
## <a name="next-steps"></a>后续步骤

为适用于 .NET 的此内容审查器快速入门以及其他内容审查器快速入门[下载 Visual Studio 解决方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)，并开始集成。
