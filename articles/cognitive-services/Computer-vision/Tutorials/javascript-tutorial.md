---
title: 计算机视觉 API JavaScript 教程 | Microsoft Docs
description: 介绍一款使用 Microsoft 认知服务计算机视觉 API 的基本 JavaScript 应用。 执行 OCR、创建缩略图，并处理图像中的视觉特征。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 09/19/2017
ms.author: kefre
ms.openlocfilehash: 89bdc0524e07c1cb6a1473e0a52791fe20271e06
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365571"
---
# <a name="computer-vision-api-javascript-tutorial"></a>计算机视觉 API JavaScript 教程

本教程展示 Microsoft 认知服务计算机视觉 REST API 的功能。

介绍一款 JavaScript 应用程序，它使用计算机视觉 REST API 执行光学符号识别 (OCR)、创建智能裁剪的缩略图，还检测、分类、标记和描述图像中的人脸等视觉特征。 在本例中，你提交一个图像 URL 进行分析和处理。 你可使用此开源示例作为模板，在 JavaScript 中自行构建使用计算机视觉 REST API 的应用。

JavaScript 表单应用程序已编写完毕，但不具有计算机视觉功能。 在本教程中，你添加计算机视觉 REST API 特定的代码，补全应用程序的功能。

## <a name="prerequisites"></a>先决条件

### <a name="platform-requirements"></a>平台要求

本教程是通过简单的文本编辑器进行开发的。

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>订阅计算机视觉 API 并获得订阅密钥 

创建示例前，必须先订阅 Microsoft 认知服务中随附的计算机视觉 API。 有关订阅和密钥管理的详细信息，请参阅[订阅](https://azure.microsoft.com/try/cognitive-services/)。 主密钥和辅助密钥均适用于本教程。 

## <a name="download-the-tutorial-project"></a>下载教程项目

复制[认知服务 JavaScript 计算机视觉教程](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial)，或下载 .zip 文件并将其解压缩到空目录。

如果更想要使用添加了所有教程代码的完备教程，可使用“已完成”文件夹中的文件。

## <a name="add-the-tutorial-code"></a>添加教程代码

JavaScript 应用程序设置有 6 个 .html 文件（每个功能一个文件）。 每个文件展示计算机视觉的不同函数（分析、OCR 等）。 教程的 6 个部分相互独立，因此可向一个文件、全部 6 个文件或仅向几个文件添加教程代码。 而且，可按任意顺序向文件添加教程代码。

让我们开始吧。

## <a name="analyze-an-image"></a>分析图像

计算机视觉的分析功能可分析图像中超过 2,000 个可识别的对象、生物、风景和动作。 分析完成后，分析功能返回一个 JSON 对象，它使用描述性的标签、色彩分析和题注等解说图像。

要完成教程应用程序的分析功能，请执行以下步骤：

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>“分析”步骤 1：为表单按钮添加事件处理程序代码

在文本编辑器中打开 analyze.html 文件，找到文件底部附近的 analyzeButtonClick 函数。

analyzeButtonClick 事件处理程序函数将表单清空、显示 URL 中指定的图像，然后调用 AnalyzeImage 函数来分析图像。

将以下代码复制粘贴到 analyzeButtonClick 函数中。

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>“分析”步骤 2：添加用于 REST API 调用的包装器

AnalyzeImage 函数包装 REST API 调用以分析图像。 成功返回后，指定的文本区域将显示格式化的 JSON 分析结果，且指定的范围中将显示题注。

在紧接着 analyzeButtonClick 函数的后面复制粘贴 AnalyzeImage 函数。

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="analyze-step-3-run-the-application"></a>“分析”步骤 3：运行应用程序

保存 analyze.html 文件并在 Web 浏览器中将其打开。 在“订阅密钥”字段中填写订阅密钥，并验证确保“订阅区域中”使用的区域正确无误。 输入图像 URL 进行分析，然后单击“分析”按钮以分析图像并查看结果。

## <a name="recognize-a-landmark"></a>识别地标

计算机视觉的地标功能可分析图像中的自然和人造地标，例如山脉或著名的建筑物。 分析完成后，地标功能返回一个 JSON 对象，它标识在图像中找到的地标。

要完成教程应用程序的地标功能，请执行以下步骤：

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>“地标”步骤 1：为表单按钮添加事件处理程序代码

在文本编辑器中打开 landmark.html 文件，找到文件底部附近的 landmarkButtonClick 函数。

landmarkButtonClick 事件处理程序函数将表单清空、显示 URL 中指定的图像，然后调用 IdentifyLandmarks 函数来分析图像。

将以下代码复制粘贴到 landmarkButtonClick 函数中。

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>“地标”步骤 2：添加用于 REST API 调用的包装器

dentifyLandmarks 函数包装 REST API 调用以分析图像。 成功返回后，指定的文本区域将显示格式化的 JSON 分析结果，且指定的范围中将显示题注。

在紧接着 landmarkButtonClick 函数的后面复制粘贴 IdentifyLandmarks 函数。

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="landmark-step-3-run-the-application"></a>“地标”步骤 3：运行应用程序

保存 landmark.html 文件并在 Web 浏览器中将其打开。 在“订阅密钥”字段中填写订阅密钥，并验证确保“订阅区域中”使用的区域正确无误。 输入图像 URL 进行分析，然后单击“分析”按钮以分析图像并查看结果。

## <a name="recognize-celebrities"></a>识别名人

计算机视觉的名人功能可分析图像中的名人。 分析完成后，名人功能返回一个 JSON 对象，它标识在图像中找到的名人。

要完成教程应用程序的名人功能，请执行以下步骤：

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>“名人”步骤 1：为表单按钮添加事件处理程序代码

在文本编辑器中打开 celebrities.html 文件，找到文件底部附近的 celebritiesButtonClick 函数。

celebritiesButtonClick 事件处理程序函数将表单清空、显示 URL 中指定的图像，然后调用 IdentifyCelebrities 函数来分析图像。

将以下代码复制粘贴到 celebritiesButtonClick 函数中。

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>“名人”步骤 2：添加用于 REST API 调用的包装器

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="celebrities-step-3-run-the-application"></a>“名人”步骤 3：运行应用程序

保存 celebrities.html 文件并在 Web 浏览器中将其打开。 在“订阅密钥”字段中填写订阅密钥，并验证确保“订阅区域中”使用的区域正确无误。 输入图像 URL 进行分析，然后单击“分析”按钮以分析图像并查看结果。

## <a name="intelligently-generate-a-thumbnail"></a>智能生成缩略图

计算机视觉的缩略图功能可通过图像生成缩略图。 通过使用“智能裁剪”功能，缩略图功能将识别图像中的兴趣区域并集中于此区域创建缩略图，进而生成视觉上更美观的缩略图。

要完成教程应用程序的缩略图功能，请执行以下步骤：

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>“缩略图”步骤 1：为表单按钮添加事件处理程序代码

在文本编辑器中打开 thumbnail.html 文件，找到文件底部附近的 thumbnailButtonClick 函数。

thumbnailButtonClick 事件处理程序将表单清空、显示 URL 中指定的图像，然后调用 getThumbnail 函数两次来创建两个缩略图（一个智能裁剪的缩略图，一个未智能裁剪的缩略图）。

将以下代码复制粘贴到 thumbnailButtonClick 函数中。

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>“缩略图”步骤 2：添加用于 REST API 调用的包装器

getThumbnail 函数包装 REST API 调用以分析图像。 在成功返回后，指定的 img 元素中将显示缩略图。

在紧接着 thumbnailButtonClick 函数的后面复制粘贴以下 getThumbnail 函数。

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

### <a name="thumbnail-step-3-run-the-application"></a>“缩略图”步骤 3：运行应用程序

保存 thumbnail.html 文件并在 Web 浏览器中将其打开。 在“订阅密钥”字段中填写订阅密钥，并验证确保“订阅区域中”使用的区域正确无误。 输入图像 URL 进行分析，然后单击“生成缩略图”按钮以分析图像并查看结果。

## <a name="read-printed-text-ocr"></a>读取打印文本 (OCR)

计算机视觉的光学字符识别 (OCR) 功能可分析图像中的打印文本。 分析完成后，OCR 返回一个 JSON 对象，它包含图像中的文本及其位置。

要完成教程应用程序的 OCR 功能，请执行以下步骤：

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR 步骤 1：为表单按钮添加事件处理程序代码

在文本编辑器中打开 ocr.html 文件，找到文件底部附近的 ocrButtonClick 函数。

ocrButtonClick 事件处理程序函数将表单清空、显示 URL 中指定的图像，然后调用 ReadOcrImage 函数来分析图像。

将以下代码复制粘贴到 ocrButtonClick 函数中。

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>OCR 步骤 2：添加用于 REST API 调用的包装器

ReadOcrImage 函数包装 REST API 调用以分析图像。 成功返回后，指定的文本区域将显示描述文本和文本位置的格式化 JSON。

在紧接着 ocrButtonClick 函数的后面复制粘贴以下 ReadOcrImage 函数。

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="ocr-step-3-run-the-application"></a>OCR 步骤 3：运行应用程序

保存 ocr.html 文件并在 Web 浏览器中将其打开。 在“订阅密钥”字段中填写订阅密钥，并验证确保“订阅区域中”使用的区域正确无误。 输入要读取的文本图像的 URL，然后单击“读取”按钮以分析图像并查看结果。

## <a name="read-handwritten-text-handwriting-recognition"></a>读取手写文本（识别手写体）

计算机视觉的手写体识别功能可分析图像中的手写文本。 分析完成后，手写体识别功能返回一个 JSON 对象，它包含图像中的文本及其位置。

要完成教程应用程序的手写体识别功能，请执行以下步骤：

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>“手写体识别”步骤 1：为表单按钮添加事件处理程序代码

在文本编辑器中打开 handwriting.html 文件，找到文件底部附近的 handwritingButtonClick 函数。

handwritingButtonClick 事件处理程序函数将表单清空、显示 URL 中指定的图像，然后调用 HandwritingImage 函数来分析图像。

将以下代码复制粘贴到 handwritingButtonClick 函数中。

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>“手写体识别”步骤 2：添加用于 REST API 调用的包装器

ReadHandwrittenImage 函数包装分析图像时所需的两个 REST API 调用。 由于手写体识别非常耗时，因此分两步执行。 第一个调用提交图像进行分析；第二个调用检索完成处理时检测到的文本。

检索到文本后，指定的文本区域将显示描述文本和文本位置的格式化 JSON。

在紧接着 handwritingButtonClick 函数的后面复制粘贴以下 ReadHandwrittenImage 函数。

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requrires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="handwriting-recognition-step-3-run-the-application"></a>“手写体识别”步骤 3：运行应用程序

保存 handwriting.html 文件并在 Web 浏览器中将其打开。 在“订阅密钥”字段中填写订阅密钥，并验证确保“订阅区域中”使用的区域正确无误。 输入要读取的文本图像的 URL，然后单击“读取”按钮以分析图像并查看结果。

## <a name="next-steps"></a>后续步骤

- [计算机视觉 API C# 教程](CSharpTutorial.md)
- [计算机视觉 API Python 教程](PythonTutorial.md)
