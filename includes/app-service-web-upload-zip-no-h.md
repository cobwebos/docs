---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/24/2018
ms.author: cephalin
ms.openlocfilehash: e4bc749047bbf0d55fc60a699ac956421775d5b0
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "58114300"
---
在 [Azure 门户](https://portal.azure.com)中，单击“资源组” > **cloud-shell-storage-\<your_region>** > **\<storage_account_name>**。

![查找 Cloud Shell 存储帐户](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

在存储帐户的“概览”页中，选择“文件”。

选择自动生成的文件共享，然后选择“上传”。 此文件共享在 Cloud Shell 中装载为 `clouddrive`。

![查找“上传”按钮](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

单击文件选择器并选择 ZIP 文件，然后单击“上传”。 

在 Cloud Shell 中，使用 `ls` 来验证是否可以在默认的 `clouddrive` 共享中看到上传的 ZIP 文件。

```azurecli-interactive
ls clouddrive
```
