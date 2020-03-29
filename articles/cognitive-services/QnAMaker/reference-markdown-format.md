---
title: 标记格式 - QnA 制造商
description: 以下是可在 QnA Maker 的答案文本中使用的标记格式列表。
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045390"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>QnA Maker 答案文本中支持的标记格式

QnA Maker 将答案文本存储为标记。 有许多标记的味道。 为了确保正确返回和显示答案文本，请使用此引用。

使用 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** 教程验证 Markdown。 此教程具有用于快速复制/粘贴验证的**试一试**功能。

## <a name="supported-markdown-format"></a>支持的降标格式

以下是可在 QnA Maker 的答案文本中使用的标记格式列表。

|目的|格式|示例 Markdown|渲染<br>如聊天机器人中显示的|
|--|--|--|--|
2个句子之间的新行。|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![在两个句子之间设置新行格式](./media/qnamaker-concepts-datasources/format-newline.png)|
|从 h1 到 h6 的标头`#`，表示哪个标头的数量。 1`#`是 h1。|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![具有标记头的格式](./media/qnamaker-concepts-datasources/format-headers.png)<br>![具有标记头 H1 到 H5 的格式](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|斜体 |`*text*`|`How do I create a bot with *QnA Maker*?`|![格式斜体](./media/qnamaker-concepts-datasources/format-italics.png)|
|强（粗体）|`**text**`|`How do I create a bot with **QnA Maker**?`|![具有粗体强标记的格式](./media/qnamaker-concepts-datasources/format-strong.png)|
|链接的 URL|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL 格式（超链接）](./media/qnamaker-concepts-datasources/format-url.png)|
|*公共图像的 URL|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![公共图像 URL 的格式 ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|删除线|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![打击通的格式](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|粗体和斜体|`***text***`|`How can I create a ***QnA Maker*** bot?`|![粗体和斜体格式](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|链接的粗体 URL|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![粗体 URL 的格式](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|链接的斜体 URL|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![斜体 URL 的格式](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|转义标记符号|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![斜体 URL 的格式](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|有序列表|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>前面的示例使用内置于标记中的自动编号。<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>前面的示例使用显式编号。|![排序列表的格式](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|无序列表|`\n * item1 \n * item2`<br>或<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![无序列表的格式](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|嵌套列表|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>可以一起嵌套有序和无序列表。 选项卡`\t`指示子元素的缩进级别。|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![嵌套无序列表的格式](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![嵌套有序列表的格式](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA 制造商不会以任何方式处理图像。 它是客户端应用程序的角色来呈现映像。

如果要使用更新/替换知识库 API 添加内容，并且内容/文件包含 html 标记，则可以通过确保以编码格式转换标记的打开和关闭来保留文件中的 HTML。

| 保留 HTML  | API 请求中的表示  | 以 KB 为单位的表示形式 |
|-----------|---------|-------------------------|
| 是 | \&lt;br\&gt; | &lt;Br&gt; |
| 是 | \&lt;h3\&gt;\&头 lt;/h3\&gt; | &lt;h3&gt;&lt;头 /h3&gt; |

此外，CR LF（\r\n）在 KB 中转换为 \n。 LF（\n）保持不变。 如果要转义任何转义序列（如 \t 或 \n），则可以使用反斜杠，例如\\\\："r\\\\n"\\\\和"t"

## <a name="next-steps"></a>后续步骤

查看批处理测试[文件格式](reference-tsv-format-batch-testing.md)。
