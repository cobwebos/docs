---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554724"
---
通过创建下面的 Azure 资源之一开始使用异常检测器服务。

* [试用资源](https://azure.microsoft.com/try/cognitive-services/#decision)（不需要 Azure 订阅）： 
    * 有效期为七天，免费。 注册后，试用密钥和终结点将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上可用。 
    * 如果你想尝试异常检测器，但没有 Azure 订阅，这是一个很好的选择。

* [异常检测器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)：
    * 在删除资源之前，可通过 [Azure 门户](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)使用。
    * 使用免费定价层试用该服务，稍后升级到用于生产的付费层。

### <a name="create-an-environment-variable"></a>创建环境变量

>[!NOTE]
> 在 2019 年 7 月 1 日之后创建的非试用资源的终结点使用如下所示的自定义子域格式。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

从创建的资源使用密钥和终结点，创建两个用于身份验证的环境变量：

* `ANOMALY_DETECTOR_KEY` - 用于验证请求的资源密钥。
* `ANOMALY_DETECTOR_ENDPOINT` - 用于发送 API 请求的资源终结点。 它将如下所示： 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

使用操作系统的说明。

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

添加环境变量后，请重启控制台窗口。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

编辑 `.bash_profile`，然后添加环境变量：

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

添加环境变量后，请从控制台窗口运行 `source .bash_profile`，使更改生效。

***