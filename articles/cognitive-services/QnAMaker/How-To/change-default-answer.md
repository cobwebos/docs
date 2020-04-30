---
title: 获取默认的答案 QnA Maker
description: 如果与问题没有匹配项，将返回默认的答案。 你可能需要更改标准默认值答案的默认应答。
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097092"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>更改 QnA Maker 资源的默认答案

如果与问题没有匹配项，将返回默认的答案。 你可能需要更改标准默认值答案的默认应答。

## <a name="change-default-answer-in-the-azure-portal"></a>更改 Azure 门户中的默认答案

1. 转到 [Azure 门户](https://portal.azure.com)并导航到表示所创建的 QnA Maker 服务的资源组。

2. 单击以打开“应用服务”****。

    ![在 Azure 门户中访问 QnA Maker 的应用服务](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. 单击“应用程序设置”****，然后将 **DefaultAnswer** 字段编辑为所需的默认响应。 单击“ **保存**”。

    ![选择“应用程序设置”，然后编辑 QnA Maker 的 DefaultAnswer](../media/qnamaker-concepts-confidencescore/change-response.png)

4. 重启应用服务

    ![更改 DefaultAnswer 后，重启 QnA Maker 应用服务](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>后续步骤

* [创建知识库](../How-to/manage-knowledge-bases.md)