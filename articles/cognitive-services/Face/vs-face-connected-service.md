---
title: 人脸 API C# 教程 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 创建一个简单的 Windows 应用，该应用使用认知服务人脸 API 检测图像中人脸的特征。
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ghogen
ms.openlocfilehash: b51760f889db27aa25e54582070ee7d3adcf66f8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38663885"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>在 Visual Studio 中使用连接服务连接到认知服务人脸 API

通过使用认知服务人脸 API，可以检测、分析、组织和标记照片中的人脸。

本文及其同类文章提供了对认知服务人脸 API 使用 Visual Studio 连接服务功能的详细信息。 安装了认知服务扩展的 Visual Studio 2017 15.7 或更高版本中均提供此功能。

## <a name="prerequisites"></a>先决条件

- **一个 Azure 订阅**。 如果没有帐户，可以注册一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- **Visual Studio 2017 版本 15.7**（装有 **Web 开发**工作负荷）。 [立即下载](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>创建项目，并添加对认知服务人脸 API 的支持

1. 创建新的 ASP.NET Core Web 项目。 使用空项目模板。 

1. 在“解决方案资源管理器”中，选择“添加” > “连接服务”。
   此时会显示“连接服务”页，其中包含可添加到项目的服务。

   ![“添加连接服务”菜单项](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. 在可用服务菜单中，选择“认知服务人脸 API”。

   ![选择要连接的服务](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   如果已登录到 Visual Studio，并且有与帐户关联的 Azure 订阅，则会显示一个页面，其中提供了订阅下拉列表。

   ![选择订阅](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. 选择要使用的订阅，然后选择人脸 API 的名称，或选择“编辑”链接以修改自动生成的名称，选择资源组和定价层。

   ![编辑连接服务详细信息](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   有关定价层的详细信息，请访问链接。

1. 选择“添加”，添加对“连接服务”的支持。
   Visual Studio 会修改项目以添加 NuGet 包、配置文件条目和其他更改，从而支持与人脸 API 的连接。

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>使用人脸 API 检测图像中人脸的特征

1. 在 Startup.cs 中添加以下 using 语句。
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. 添加配置字段，并添加一个构造函数，该构造函数初始化 Startup 类中的配置字段以在程序中启用“配置”。

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. 在项目的 wwwroot 文件夹中，添加图像文件夹，并向 wwwroot 文件夹添加图像文件。 例如，可使用此[人脸 API 页](https://azure.microsoft.com/services/cognitive-services/face/)中的一张图像。 右键单击其中一张图像，将其保存到本地硬盘驱动器，然后在解决方案资源管理器中右键单击图像文件夹，选择“添加” > “现有项”，将其添加到项目中。 项目在解决方案资源管理器中应如下所示：
 
   ![包含图像文件的图像文件夹](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. 右键单击图像文件，选择“属性”，然后选择“如果较新则复制”。

   ![如果较新则复制](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. 将配置方法替换为以下代码，访问人脸 API 并测试图像。 将 imagePath 字符串更改为人脸图像正确的路径和文件名。

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    此步骤中的代码会使用添加连接服务时添加的密钥，构造调用人脸 REST API 的 HTTP 请求。

1. 添加帮助程序函数 GetImageAsByteArray 和 JsonPrettyPrint。

   ```csharp
        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            string INDENT_STRING = "    ";
            var indent = 0;
            var quoted = false;
            var sb = new StringBuilder();
            for (var i = 0; i < json.Length; i++)
            {
                var ch = json[i];
                switch (ch)
                {
                    case '{':
                    case '[':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case '}':
                    case ']':
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        sb.Append(ch);
                        break;
                    case '"':
                        sb.Append(ch);
                        bool escaped = false;
                        var index = i;
                        while (index > 0 && json[--index] == '\\')
                            escaped = !escaped;
                        if (!escaped)
                            quoted = !quoted;
                        break;
                    case ',':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case ':':
                        sb.Append(ch);
                        if (!quoted)
                            sb.Append(" ");
                        break;
                    default:
                        sb.Append(ch);
                        break;
                }
            }
            return sb.ToString();
        }
   ```

1. 运行 Web 应用程序并查看人脸 API 在图像中发现的信息。
 
   ![人脸 API 图像和格式化结果](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将其删除。 这会删除认知服务和相关资源。 若要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，请将其选中。
1. 选择“删除资源组”。
1. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

阅读[人脸 API 文档](Overview.md)，了解有关人脸 API 的详细信息。
