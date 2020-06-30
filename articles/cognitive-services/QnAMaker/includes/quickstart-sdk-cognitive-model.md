---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2020
ms.locfileid: "85114504"
---

## <a name="using-this-example-knowledge-base"></a>使用示例知识库

本快速入门中的知识库以 2 个对话 QnA 对开始（这样做一是为了简化此示例，二是为了在 Update 方法中有高度可预测的 ID 供使用），将问题的跟进提示与新对相关联。 这是为本快速入门计划的，按特定顺序实现。

如果计划未来要使用依赖于现有 QnA 对的跟进提示来开发知识库，可选择执行以下操作：
* 对于较大的知识库，请在支持自动化的文本编辑器或 TSV 工具中管理该知识库，然后立即使用更新完全替换知识库。
* 对于较小的知识库，请完全在 QnA Maker 门户中管理跟进提示。

本快速入门中使用的 QnA 对的详细信息：
* QnA 对的类型 - 更新后，此知识库中有 2 种类型的 QnA 对：chitchat 以及特定于域的信息。 如果你的知识库与某个会话应用程序（如聊天机器人）相关联，则这种情况很常见。
* 尽管知识库答案可以按元数据进行筛选或使用跟进提示，但本快速入门并不会介绍此内容。 可在[此处](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)找到与语言无关的 generateAnswer 示例。
* 答案文本为 markdown 格式，并且可以包含[多种 markdown 格式](../reference-markdown-format.md)，例如图像（可公开使用的基于 Internet 的图像）、链接（指向公开可用的 URL）和提要点，但本快速入门不涉及这些多样内容。
