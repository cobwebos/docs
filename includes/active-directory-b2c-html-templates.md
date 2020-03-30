---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189001"
---
## <a name="sample-templates"></a>示例模板
可以在以下位置找到用于 UI 自定义的示例模板：

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

此项目包含以下模板：
- [海蓝](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [青灰](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

要使用示例：

1. 在本地计算机上克隆回购。 选择模板文件夹`/ocean_blue`或`/slate_gray`。
1. 将模板文件夹和`/assets`文件夹下的所有文件上载到 Blob 存储，如前几节所述。
1. 接下来，打开`\*.html`或`/ocean_blue``/slate_gray`的根中的每个文件，将相对 URL 的所有实例替换为步骤 2 中上载的 css、图像和字体文件的 URL。 例如：
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    目标
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. 保存`\*.html`文件并将其上载到 Blob 存储。
1. 现在修改策略，指向您的 HTML 文件，如前所述。
1. 如果看到缺少字体、图像或 CSS，请在扩展程序和\*.html 文件中检查引用。
