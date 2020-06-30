---
title: 快速入门：生成缩略图 - REST、Java
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用计算机视觉 API 和 Java 基于图像生成缩略图。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 355a450cbdcf44d79cf73904e3b932afb62b7f55
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987254"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-java"></a>快速入门：使用计算机视觉 REST API 和 Java 生成缩略图

本快速入门将使用计算机视觉 REST API 基于图像生成缩略图。 可以指定高度和宽度，可以与输入图像的纵横比不同。 计算机视觉使用智能裁剪来智能识别感兴趣的区域并基于该区域生成裁剪坐标。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Java&trade; Platform 标准版开发工具包 7 或 8](https://aka.ms/azure-jdks)（JDK 7 或 8）
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 为密钥和终结点 URL [创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别将其命名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`。

## <a name="create-and-run-the-sample-application"></a>创建和运行示例应用程序

要创建和运行示例，请执行以下步骤：

1. 在最喜爱的 IDE 或编辑器中新建一个 Java 项目。 如果此选项可用，请从命令行应用程序模板创建 Java 项目。
1. 将以下库导入到你的 Java 项目中。 如果使用 Maven，则为每个库提供 Maven 坐标。
   - [Apache HTTP 客户端](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.X)
   - [Apache HTTP 核心](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.X)
   - [JSON 库](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. 向主类中添加以下 `import` 声明： 

   ```java
    import java.awt.*;
    import javax.swing.*;
    import java.net.URI;
    import java.io.InputStream;
    import javax.imageio.ImageIO;
    import java.awt.image.BufferedImage;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONObject;
   ```

1. 将下面示例代码的其余部分添加到导入的内容下（如有必要，请更改为自己的类名）。
1. 将计算机视觉订阅密钥和终结点添加到环境变量。
1. （可选）将 `imageToAnalyze` 的值替换为自己的图像的 URL。
1. 保存，然后生成 Java 项目。
1. 如果使用的是 IDE，请运行 `GenerateThumbnail`。 否则，请从命令行运行（下面的命令）。

```java
/**
 * This sample uses the following libraries (create a "lib" folder to place them in): 
 * Apache HTTP client:
 * org.apache.httpcomponents:httpclient:4.5.X 
 * Apache HTTP core:
 * org.apache.httpcomponents:httpccore:4.4.X 
 * JSON library:
 * org.json:json:20180130
 *
 * To build/run from the command line: 
 *     javac GenerateThumbnail.java -cp .;lib\*
 *     java -cp .;lib\* GenerateThumbnail
 */

public class GenerateThumbnail {

    // Add your Computer Vision subscription key and endpoint to your environment
    // variables. Then, close and then re-open your command shell or project for the
    // changes to take effect.
    private static String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    private static String endpoint = System.getenv("COMPUTER_VISION_ENDPOINT");
    // The endpoint path
    private static final String uriBase = endpoint + "vision/v3.0/generateThumbnail";
    // It's optional if you'd like to use your own image instead of this one.
    private static final String imageToAnalyze = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API method.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            System.out.println("status" + response.getStatusLine().getStatusCode());

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200) {
                // Display the thumbnail.
                System.out.println("\nDisplaying thumbnail.\n");
                displayImage(entity.getContent());
            } else {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // Displays the given input stream as an image.
    public static void displayImage(InputStream inputStream) {
        try {
            BufferedImage bufferedImage = ImageIO.read(inputStream);

            ImageIcon imageIcon = new ImageIcon(bufferedImage);

            JLabel jLabel = new JLabel();
            jLabel.setIcon(imageIcon);

            JFrame jFrame = new JFrame();
            jFrame.setLayout(new FlowLayout());
            jFrame.setSize(100, 150);

            jFrame.add(jLabel);
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>检查响应

成功的响应将以二进制数据形式返回，这些数据表示缩略图的图像数据。 如果请求成功，则会根据响应中的二进制数据生成缩略图，并且该缩略图将显示在由示例应用程序创建的一个单独窗口中。 如果请求失败，则响应将显示在控制台窗口中。 失败请求的响应包含错误代码和消息，用以帮助确定出错的地方。

## <a name="next-steps"></a>后续步骤

了解一款使用计算机视觉执行光学字符识别 (OCR) 功能的 Java Swing 应用程序；创建智能裁剪缩略图；对图像中的视觉特征进行检测、分类、标记和说明。

> [!div class="nextstepaction"]
> [计算机视觉 API Java 教程](../Tutorials/java-tutorial.md)

* 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。
