# <a name="azure-technical-documentation-contributor-guide"></a>Azure 技术文档供稿人指南
GitHub 存储库中存储了 [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) 上发布的 Azure 技术文档的源文件。

此存储库还包含有关如何为技术文档供稿的指导。 有关供稿人指南中的文章列表，请参阅[索引](contributor-guide/contributor-guide-index.md)。

## <a name="contribute-to-azure-documentation"></a>为 Azure 文档供稿
感谢你对 Azure 文档的关注！

* [供稿途径](#ways-to-contribute)
* [行为准则](#code-of-conduct)
* [有关在 Azure 中供稿](#about-your-contributions-to-azure-content)
* [存储库组织方式](#repository-organization)
* [使用 GitHub、Git 和本存储库](#use-github-git-and-this-repository)
* [如何使用标记来设置主题格式](#how-to-use-markdown-to-format-your-topic)
* [更多资源](#more-resources)
* [所有供稿人指南文章的索引](contributor-guide/contributor-guide-index.md)（打开新页）

## <a name="ways-to-contribute"></a>供稿途径
可按如下所述将更新内容提交到 [Azure 文档](https://docs.microsoft.com/azure)：

* 在 GitHub 用户界面中可以轻松为技术文章供稿。 在此存储库中找到相应的文章，或者在 [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) 上访问该文章，然后单击文章中的链接转到该文章的 GitHub 源。
* 如果要对现有文章进行重大更改、添加或更改图像，或者要补充新文章，则需要派生此存储库，安装 Git Bash、Markdown Pad，并了解一些 git 命令。

## <a name="code-of-conduct"></a>行为准则
本项目采用 [Microsoft 开源行为准则](https://opensource.microsoft.com/codeofconduct/)。 有关详细信息，请参阅[行为准则常见问题](https://opensource.microsoft.com/codeofconduct/faq/)；若要其他任何问题或意见，请联系 [opencode@microsoft.com](mailto:opencode@microsoft.com)。

## <a name="about-your-contributions-to-azure-content"></a>有关在 Azure 中供稿
### <a name="minor-corrections"></a>轻微更正
[docs.microsoft.com 使用条款](https://docs.microsoft.com/legal/termsofuse)涵盖你在此存储库中提交的、对文档和代码示例所做的轻微更正和澄清。

### <a name="larger-submissions"></a>大型提交件
如果你不是 Microsoft 的员工，当你提交有关新文件或对文档和代码示例进行的重大更改的提取请求时，我们会在 GitHub 中发送一条意见，要求你提交一份在线供稿许可协议 (CLA)。 在我们接受你的提取请求之前，需要你填写这份在线表单。

## <a name="repository-organization"></a>存储库组织
azure-docs 存储库中的内容遵循 https://docs.microsoft.com/azure 上的文档组织方式。 此存储库包含两个根文件夹：

### <a name="articles"></a>\articles
*\articles* 文件夹包含采用标记文件格式、扩展名为 *.md* 的文档文章。 文章通常按 Azure 服务分组。

文章需要遵循严格的文件命名准则 - 有关详细信息，请参阅[文件命名指导原则](contributor-guide/file-names-and-locations.md)。

*\articles* 文件夹包含用于保存根目录文章媒体文件的 *\media* 文件夹，其中的子文件夹保存每篇文章的图像。  服务文件夹包含每个服务文件夹中的文章的独立媒体文件夹。 文章图像文件夹的命名与文章文件相同，不过没有 *.md* 扩展名。

### <a name="includes"></a>\includes
可以创建要包含在一篇或多篇文章中的、可重复使用的内容部分。 请参阅[技术内容中使用的自定义扩展](contributor-guide/custom-markdown-extensions.md)。

### <a name="markdown-templates"></a>\markdown templates
此文件夹包含标准标记模板，该模板提供需要在文章中使用的基本标记格式。

### <a name="contributor-guide"></a>\contributor-guide
此文件夹包含属于供稿人指南的文章。

## <a name="use-github-git-and-this-repository"></a>使用 GitHub、Git 和本存储库
有关如何供稿、如何使用 GitHub UI 提供轻微更改，以及如何派生和克隆存储库以便提供重大更改的信息，请参阅[安装和设置 GitHub 创作工具](contributor-guide/tools-and-setup.md)。

如果安装 GitBash 并选择在本地工作，请参阅[用于创建新文章或更新现有文章的 Git 命令](contributor-guide/git-commands-for-master.md)中所列的，有关创建新的本地工作分支、做出更改以及将更改提交回到主分支的步骤

### <a name="branches"></a>分支
我们建议创建面向特定更改范围的本地工作分支。 每个分支都应限制为单个的概念/项目，以简化工作流并减少合并冲突的可能性。  新分支的适当工作范围包括：

* 供应新文章（和相关的图像）
* 对文章进行拼写和语法编辑。
* 在大量文章中应用一项格式更改（例如，添加新的版权页脚）。

## <a name="how-to-use-markdown-to-format-your-topic"></a>如何使用标记来设置主题格式
此存储库中的所有文章使用 GitHub 风格的标记。  资源列表如下。

* [标记基础知识](https://help.github.com/articles/markdown-basics/)
* [可打印标记速查表](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

## <a name="article-metadata"></a>文章元数据
文章元数据可启用某些功能，如作者属性、供稿人属性、位置提示、文章说明、SEO 优化以及 Microsoft 评估内容性能时所用的报告。 因此，元数据非常重要！ [此处提供了有关确保提供正确元数据的指导](contributor-guide/article-metadata.md)。

### <a name="labels"></a>标签
将在提取请求中分配自动化标签，帮助我们管理提取请求工作流，同时让你知道提取请求的处理进度：

* 相关的供稿许可协议
  * cla-not-required：更改相对较为次要，不需要你签署 CLA。
  * cla-required：更改范围相对较大，要求你签署 CLA。
  * cla-signed：供稿人已签署 CLA，因此提取请求现在可以转到审核环节。
* 主要标签：需要审核提取请求的内部组织可以借助 PnP、现代应用和 TDC 等标签来分类提取请求。
* 更改已发送到作者：已通知作者正在等待处理提取请求。

## <a name="more-resources"></a>更多资源
有关指南主题，请参阅[供稿人指南索引](contributor-guide/contributor-guide-index.md)。



<!--HONumber=Feb17_HO2-->


