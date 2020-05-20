---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446414"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>分析键值对和表的表单

接下来，使用新的经过训练的模型分析文档并从中提取键值对和表。 在新的 Python 脚本中运行以下代码，以便调用[分析表单](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) API。 在运行该脚本之前，请进行以下更改：

1. 将 `<file path>` 替换为表单的文件路径（例如，C:\temp\file.pdf）。 此项也可以是远程文件的 URL。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Test** 文件夹下的文件。
1. 将 `<model_id>` 替换为在上一部分收到的模型 ID。
1. 将 `<endpoint>` 替换为从表单识别器订阅密钥中获得的终结点。 可以在表单识别器资源的“概览”选项卡中找到该终结点。 
1. 将 `<file type>` 替换为文件类型。 支持的类型：`application/pdf`、`image/jpeg`、`image/png`、`image/tiff`。
1. 将 `<subscription key>` 替换为订阅密钥。

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. 将代码保存在以 .py 为扩展名的文件中。 例如，*form-recognizer-analyze.py*。
1. 打开命令提示符窗口。
1. 在提示符处，使用 `python` 命令运行示例。 例如，`python form-recognizer-analyze.py` 。

调用**分析表单** API 时，会收到一个 `201 (Success)` 响应，其中包含 **Operation-Location** 标头。 此标头的值是用于跟踪“分析”操作结果的 ID。 上述脚本将此标头的值输出到控制台。

## <a name="get-the-analyze-results"></a>获取分析结果

将以下代码添加到 Python 脚本的底部。 这样就可以在新的 API 调用中使用来自上一调用的 ID 值，以便检索分析结果。 “分析表单”操作是异步的，因此此脚本会定期调用 API，直到结果可用为止。  我们建议调用间隔为一秒或更长时间。

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
