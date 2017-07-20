---
title: Use Connectors in Azure Content Moderator | Microsoft Docs
description: Content Moderator workflows can use content from other APIs by using Connectors.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.technology: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 0e957eb5f553bf133b558388673342d5e6a4fd5b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---

# <a name="about-connectors"></a>About Connectors #

Workflows can use other APIs, not just the Content Manager API. However, other APIs first need to be connected to Content Manager using a Connector: the Connector provides a link to the other API.

Content Moderator includes Connectors by default, including these Connectors.

![Content Moderation Connectors](images/connectors-1.png)

## <a name="verifying-your-credentials"></a>Verifying your credentials ## 

Before you start defining a workflow, make sure you have valid credentials for the API you intend to use.

1.  Select Connectors from the Settings tab in the Review Tool Dashboard.

  ![Content Moderation Connectors](images/connectors-2.png)

2.  Click the Edit symbol next to the connector for which you want credentials.

  ![Content Moderation Connectors](images/connectors-3.png)

3.  The subscription key displays. If you make any edits, click Save when you are done.

  ![Content Moderation Connectors](images/connectors-4.png)
 
## <a name="adding-a-connector"></a>Adding a Connector ##

1.  Before you add a connector, you will need a subscription key. From the Settings > Credentials window, copy the Ocp-Admin-Subscription-Key.

2.  Click Connectors. From the available connectors displayed in the Review Tool Dashboard, click Connect. A window opens, asking for your subscription key.

  ![Content Moderation Connectors](images/connectors-5.png)

3.  Paste the key you just copied into the Ocp-Admin-Subscription-Key field, then click Save.

## <a name="next-steps"></a>Next steps ##

To learn how to use connectors to define custom workflows, see the [workflows](workflows.md) article.

