---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 734fce2c01614d5dca73f171fb59f25f39d13705
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461497"
---
## <a name="configure-an-environment-variable-for-authentication"></a>配置身份验证的环境变量

应用程序需要进行身份验证对它们使用认知服务的访问。 若要进行身份验证，我们建议创建一个环境变量来存储在 Azure 资源中的密钥。 

你的密钥后，其写入新的环境变量在运行该应用程序的本地计算机上。 若要设置环境变量，请打开控制台窗口，并遵照适用于操作系统的说明。 替换为`your-key`与你异常情况检测程序的访问密钥：

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    添加环境变量后，可能需要重启任何正在运行的、需要读取环境变量的程序（包括控制台窗口）。 例如，如果使用的 Visual Studio 作为编辑器，重新运行该示例之前启动 Visual Studio。

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。
    
* macOS
    
    编辑 .bash_profile，然后添加环境变量：
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    添加环境变量后，请从控制台窗口运行 `source .bash_profile`，使更改生效。
