---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88657021"
---
<!-- ### Upload files with the CLI -->

以下命令上传单个文件。  

更改以下值：

1. 将 `/path/to/file` 更改为文件的本地路径。  
1. 将 `MyContainer` 更改为先前使用资产 ID（而非名称）创建的资产。
1. 将 `MyBlob` 更改为想要用于已上传的文件的名称。
1. 将 `MyStorageAccountName` 更改为正在使用的存储帐户的名称。
1. 将 `MyStorageAccountKey` 更改为存储帐户的访问密钥。

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
