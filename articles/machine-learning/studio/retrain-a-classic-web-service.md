---
title: "重新训练经典 Web 服务 | Microsoft Docs"
description: "了解如何以编程方式重新训练模型并更新 Web 服务，以使用 Azure 机器学习中的最新训练模型。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondlaghaeian
editor: 
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: ad18d82109e3048625f32d90af9677956350fb84
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="retrain-a-classic-web-service"></a>重新训练经典 Web 服务
部署的预测 Web 服务是默认的评分终结点。 默认终结点与原始训练和评分实验保持同步，因此不能替换默认终结点的训练模型。 要重新训练 Web 服务，必须将新的终结点添加到 Web 服务。 

## <a name="prerequisites"></a>系统必备
必须设置训练实验和预测实验，如[以编程方式重新训练机器学习模型](retrain-models-programmatically.md)中所示。 

> [!IMPORTANT]
> 预测实验必须部署为经典机器学习 Web 服务。 
> 
> 

有关部署 Web 服务的其他信息，请参阅[部署 Azure 机器学习 Web 服务](publish-a-machine-learning-web-service.md)。

## <a name="add-a-new-endpoint"></a>添加新的终结点
已部署的预测 Web 服务包含与原始训练和评分实验训练模型保持同步的默认评分终结点。 若要使用新的训练模型更新 Web 服务，必须创建新的评分终结点。 

若要创建新的评分终结点，请在可以使用训练模型更新的预测 Web 服务上执行以下操作：

> [!NOTE]
> 确保将终结点添加到预测 Web 服务，而不是训练 Web 服务。 如果已正确部署训练和预测 Web 服务，应该看到已列出两个单独的 Web 服务。 预测 Web 服务应以“[predictive exp.]”结尾。
> 
> 

以下两种方法可将新终结点添加到 Web 服务：

1. 以编程方式
2. 使用 Microsoft Azure Web 服务门户

### <a name="programmatically-add-an-endpoint"></a>以编程方式添加终结点
可以使用在此 [GitHub 存储库](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs)中提供的示例代码添加评分终结点。

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>使用 Microsoft Azure Web 服务门户添加终结点
1. 在机器学习工作室的左侧导航栏中，单击“Web 服务”。
2. 在 Web 服务仪表板的底部，单击“管理终结点预览”。
3. 单击 **“添加”**。
4. 键入新终结点的名称及说明。 选择日志记录级别以及是否启用示例数据。 有关日志记录的详细信息，请参阅[为机器学习 Web 服务启用日志记录](web-services-logging.md)。

## <a name="update-the-added-endpoints-trained-model"></a>更新已添加终结点的训练模型
若要完成重新训练过程，必须更新已新添加的终结点的训练模型。

如果使用示例代码添加了终结点，这会在输出中包括 *HelpLocationURL* 值标识的帮助 URL 位置。

若要检索路径 URL，请执行以下步骤：

1. 将 URL 复制并粘贴到浏览器。
2. 单击“更新资源”链接。
3. 复制 PATCH 请求的 POST URL。 例如：
   
     PATCH URL：https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

现在可以使用训练模型更新之前创建的评分终结点。

以下示例代码演示了如何使用 *BaseLocation*、*RelativeLocation*、*SasBlobToken* 和 PATCH URL 更新终结点。

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

可以从终结点仪表板获取用于调用的 *apiKey* 和 *endpointUrl*。

*Resources* 中的 *Name* 参数的值应与预测实验中已保存的训练模型的资源名称相匹配。 若要获取资源名称，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，单击“机器学习”。
3. 在“名称”下，单击你的工作区，并单击“Web 服务”。
4. 在“名称”下，单击“人口普查模型 [预测实验]”。
5. 单击已添加的新终结点。
6. 在终结点仪表板上，单击“更新资源”。
7. 在 Web 服务的更新资源 API 文档页上，可以在“可更新资源”下找到“资源名称”。

如果在完成更新终结点之前 SAS 令牌到期，必须使用作业 ID 执行 GET 操作，才能获取全新令牌。

当代码成功运行时，新终结点应当在大约 30 秒内开始使用重新训练的模型。

## <a name="summary"></a>摘要
通过使用重新训练 API，可以更新预测 Web 服务的训练模型，从而启用如下方案：

* 使用新数据定期重新训练模型。
* 向客户分配模型，以便他们可以使用自己的数据重新训练模型。

## <a name="next-steps"></a>后续步骤
[Azure 机器学习经典 Web 服务重新训练的故障排除](troubleshooting-retraining-models.md)

