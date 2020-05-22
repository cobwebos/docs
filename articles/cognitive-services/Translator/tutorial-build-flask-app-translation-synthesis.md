---
title: 教程：生成用于翻译、合成与分析文本的 Flask 应用 - 翻译
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将生成一个基于 Flask 的 Web 应用以翻译文本、分析情绪，并将翻译的文本合成为语音。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: 955476eefc7575edb90634ce305bbebdf62e2371
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592349"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>教程：生成使用 Azure 认知服务的 Flask 应用

在本教程中，你将生成一个 Flask Web 应用，该应用使用 Azure 认知服务来翻译文本、分析情绪，并将翻译的文本合成为语音。 本教程重点介绍用于实现该应用程序的 Python 代码和 Flask 路由，但是，也会介绍用于统合该应用的 HTML 和 Javascript 代码。 如果遇到任何问题，请使用下面的反馈按钮告诉我们。

本教程的内容：

> [!div class="checklist"]
> * 获取 Azure 订阅密钥
> * 设置开发环境并安装依赖项
> * 创建 Flask 应用
> * 使用“翻译”来翻译文本
> * 使用文本分析来分析输入文本和翻译内容中的积极/消极情绪
> * 使用语音服务将翻译的文本转换为合成语音
> * 在本地运行 Flask 应用

> [!TIP]
> 如果想要跳过这些步骤并查看最终的全部代码，请参阅 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial) 上的完整示例和生成说明。

## <a name="what-is-flask"></a>什么是 Flask？

Flask 是用于创建 Web 应用程序的微框架。 也就是说，Flask 提供所需的工具、库和技术用于生成 Web 应用程序。 此 Web 应用程序可以是一些网页、一篇博客、Wiki，也可以具有较强的实用性，例如基于 Web 的日历应用程序或商业网站。

完成本教程后若要进行深入了解，请参考以下有用链接：

* [Flask 文档](http://flask.pocoo.org/)
* [Flask for Dummies - Flask 初学者指南](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>先决条件

下面是学习本教程所需的软件和订阅密钥。

* [Python 3.5.2 或更高版本](https://www.python.org/downloads/)
* [Git 工具](https://git-scm.com/downloads)
* IDE 或文本编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/) 或 [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) 或 [Firefox](https://www.mozilla.org/firefox)
* “翻译”订阅密钥（请注意，不需要选择区域。）
* **美国西部**区域的**文本分析**订阅密钥。
* **美国西部**区域的**语音服务**订阅密钥。

## <a name="create-an-account-and-subscribe-to-resources"></a>创建帐户并订阅资源

如前所述，在本教程中需要三个订阅密钥。 这意味着，需要在 Azure 帐户中为以下服务创建资源：
* 转换器
* 文本分析
* 语音服务

参考[在 Azure 门户中创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的分步说明创建资源。

> [!IMPORTANT]
> 对于本教程，请在美国西部区域创建资源。 如果使用其他区域，需要在每个 Python 文件中调整基 URL。

## <a name="set-up-your-dev-environment"></a>设置开发环境

生成 Flask Web 应用之前，需要为项目创建一个工作目录，并安装几个 Python 包。

### <a name="create-a-working-directory"></a>创建工作目录

1. 打开命令行 (Windows) 或终端 (macOS/Linux)。 然后，为项目创建工作目录和子目录：  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. 切换到项目的工作目录：  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>使用 `virtualenv` 创建并激活虚拟环境

让我们使用 `virtualenv` 创建 Flask 应用的虚拟环境。 使用虚拟环境可确保在干净的环境中操作。

1. 在工作目录中，运行以下命令以创建虚拟环境：**macOS/Linux：**
   ```
   virtualenv venv --python=python3
   ```
   我们已显式声明，该虚拟环境应使用 Python 3。 这可以确保装有多个 Python 版本的用户使用正确的版本。

   **Windows CMD/Windows Bash：**
   ```
   virtualenv venv
   ```
   为简单起见，我们将虚拟环境命名为 venv。

2. 用于激活虚拟环境的命令根据平台/shell 而异：   

   | 平台 | Shell | Command |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | 命令行 | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   运行此命令后，命令行或终端会话的前面应带有 `venv`。

3. 随时可以通过在命令行或终端中键入以下命令，来停用该会话：`deactivate`。

> [!NOTE]
> Python 提供了大量的文档用于介绍如何创建和管理虚拟环境，具体请参阅 [virtualenv](https://virtualenv.pypa.io/en/latest/)。

### <a name="install-requests"></a>安装 Requests

Requests 是用于发送 HTTP 1.1 请求的常用模块。 无需手动将查询字符串添加到 URL，或者对 POST 数据进行格式编码。

1. 若要安装 Requests，请运行：

   ```
   pip install requests
   ```

> [!NOTE]
> 若要详细了解 Requests，请参阅 [Requests：HTTP for Humans](https://2.python-requests.org/en/master/)。

### <a name="install-and-configure-flask"></a>安装并配置 Flask

接下来，需要安装 Flask。 Flask 处理 Web 应用的路由，并使我们能够发出服务器到服务器的调用，以便向最终用户隐藏订阅密钥。

1. 若要安装 Flask，请运行：
   ```
   pip install Flask
   ```
   确保已安装 Flask。 运行：
   ```
   flask --version
   ```
   终端中应会列显版本。 如果列显其他任何信息，则表示出错。

2. 若要运行 Flask 应用，可以使用 flask 命令，或者结合 Flask 使用 Python 的 -m 开关。 在执行该操作之前，需要通过导出 `FLASK_APP` 环境变量，来告诉终端要使用哪个应用：

   **macOS/Linux**：
   ```
   export FLASK_APP=app.py
   ```

   Windows：
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>创建 Flask 应用

在本部分，你将创建一个基本的 Flask 应用，以便在用户进入应用的根目录时返回一个 HTML 文件。 请不会花费过多的时间来尝试分解代码，因为稍后我们会回来更新此文件。

### <a name="what-is-a-flask-route"></a>什么是 Flask 路由？

让我们花费片刻时间来了解“[路由](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)”。 路由用于将 URL 绑定到特定的函数。 Flask 使用路由修饰器将函数注册到特定的 URL。 例如，当用户导航到 Web 应用的根目录 (`/`) 时，将会呈现 `index.html`。  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

让我们通过一个额外的示例来彻底了解此过程。

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

此代码确保当用户导航到 `http://your-web-app.com/about` 时，会呈现 `about.html` 文件。

尽管这些示例演示的是如何向用户呈现 HTML 页面，但路由也可用于在按下某个按钮时调用 API，或者在不离开主页的情况下执行任意数量的操作。 为翻译、情绪和语音合成创建路由时，将会看到此工作方式。

### <a name="get-started"></a>入门

1. 在 IDE 中打开项目，然后在工作目录的根目录中创建名为 `app.py` 的文件。 接下来，将以下代码复制到 `app.py` 并保存：

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   此代码块告知应用，每当用户导航到 Web 应用的根目录 (`/`) 时，都要显示 `index.html`。

2. 接下来，让我们创建 Web 应用的前端。 在 `templates` 目录中，创建名为 `index.html` 的文件。 然后，将以下代码复制到 `templates/index.html` 中。

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. 让我们测试该 Flask 应用。 在终端中，运行：

   ```
   flask run
   ```

4. 打开浏览器并导航到提供的 URL。 应会看到单页应用。 请按 **Ctrl + C** 终止该应用。

## <a name="translate-text"></a>翻译文本

大致了解简单 Flask 应用的工作方式后，让我们：

* 编写一些 Python 代码来调用“翻译”并返回响应
* 创建一个 Flask 路由用于调用 Python 代码
* 使用文本输入和翻译区域、语言选择器和翻译按钮更新 HTML
* 编写 Javascript 代码，使用户能够通过 HTML 来与 Flask 应用交互

### <a name="call-the-translator"></a>调用“翻译”

要做的第一件事是编写一个函数来调用“翻译”。 此函数采用两个参数：`text_input` 和 `language_output`。 每当用户在该应用中按下翻译按钮时，就会调用此函数。 HTML 中的文本区域作为 `text_input` 发送，HTML 中的语言选择值作为 `language_output` 发送。

1. 让我们先在工作目录的根目录中创建名为 `translate.py` 的文件。
2. 接下来，将以下代码添加到 `translate.py`。 此函数采用两个参数：`text_input` 和 `language_output`。
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. 添加“翻译”订阅密钥并保存。

### <a name="add-a-route-to-apppy"></a>将路由添加到 `app.py`

接下来，需要在 Flask 应用中创建一个用于调用 `translate.py` 的路由。 每当用户在该应用中按下翻译按钮时，就会调用此路由。

对于此应用，路由将接受 `POST` 请求。 这是因为，该函数需要提供所要翻译的文本，以及翻译的输出语言。

Flask 会提供帮助器函数来帮助分析和管理每个请求。 在提供的代码中，`get_json()` 从 `POST` 请求返回 JSON 形式的数据。 然后，使用 `data['text']` 和 `data['to']` 将文本和输出语言值传递给 `translate.py` 中的 `get_translation()` 函数。 最后一步是返回 JSON 形式的响应，因为需要在 Web 应用中显示此数据。

在以下部分，为情绪分析和语音合成创建路由时需要重复此过程。

1. 打开 `app.py`，在 `app.py` 的顶部找到 import 语句，并添加以下行：

   ```python
   import translate
   ```
   现在，我们的 Flask 应用可以使用通过 `translate.py` 提供的方法。

2. 将以下代码复制到 `app.py` 的末尾并保存：

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>更新 `index.html`

创建用于翻译文本的函数，并在 Flask 应用中添加一个用于调用该函数的路由后，下一步是开始生成应用的 HTML。 以下 HTML 的作用是：

* 提供一个文本区域，让用户输入要翻译的文本。
* 包含一个语言选择器。
* 包含 HTML 元素用于呈现检测到的语言，以及翻译期间返回的置信度评分。
* 提供一个只读的文本区域用于显示翻译输出。
* 包含稍后要在本教程中添加到此文件的情绪分析和语音合成代码的占位符。

让我们更新 `index.html`。

1. 打开 `index.html` 并找到以下代码注释：
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. 将代码注释替换为以下 HTML 块：
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

下一步是编写一些 Javascript 代码。 这是 HTML 与 Flask 路由之间的桥梁。

### <a name="create-mainjs"></a>创建 `main.js`  

`main.js` 文件是 HTML 与 Flask 路由之间的桥梁。 应用将使用 jQuery、Ajax 和 XMLHttpRequest 的组合来呈现内容，并向 Flask 路由发出 `POST` 请求。

在以下代码中，将使用 HTML 中的内容来构造对 Flask 路由发出的请求。 具体而言，文本区域和语言选择器的内容将分配到变量，然后在请求中一起传递给 `translate-text`。

然后，代码将循环访问响应，并使用翻译、检测到的语言和置信度评分更新 HTML。

1. 在 IDE 中，在 `static/scripts` 目录中创建名为 `main.js` 的文件。
2. 将以下代码复制到 `static/scripts/main.js` 中：
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>测试翻译

让我们在应用中测试翻译。

```
flask run
```

导航到提供的服务器地址。 在输入区域中键入文本，选择语言，然后按“翻译”。 随后应会看到翻译结果。 如果未看到翻译，请确保已添加订阅密钥。

> [!TIP]
> 如果所做的更改未显示，或者应用不按预期方式工作，请尝试清除缓存，或打开私密/隐匿窗口。

按 **CTRL + C** 终止应用，然后转到下一部分。

## <a name="analyze-sentiment"></a>分析情绪

[文本分析 API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) 可用于执行情绪分析、从文本中提取关键短语，或检测源语言。 在此应用中，我们将使用情绪分析来确定提供的文本是积极的、中性的还是消极的。 API 将返回介于 0 与 1 之间的数字评分。 评分接近 1 代表积极的情绪，评分接近 0 代表消极的情绪。

在本部分，你将执行以下几项操作：

* 编写一些 Python 代码，以调用文本分析 API 来执行情绪分析并返回响应
* 创建一个 Flask 路由用于调用 Python 代码
* 使用情感评分区域和一个按钮更新 HTML，以执行分析
* 编写 Javascript 代码，使用户能够通过 HTML 来与 Flask 应用交互

### <a name="call-the-text-analytics-api"></a>调用文本分析 API

让我们编写一个函数来调用文本分析 API。 此函数采用四个参数：`input_text`、`input_language`、`output_text` 和 `output_language`。 每当用户在该应用中按下“运行情绪分析”按钮时，就会调用此函数。 用户在文本区域和语言选择器中输入的数据，以及检测到的语言和翻译输出，将连同每个请求一起提供。 响应对象包含源文本和翻译结果的情绪评分。 在以下部分，你将编写一些 Javascript 代码来分析响应，并在应用中使用该响应。 现在，让我们重点了解如何调用文本分析 API。

1. 在工作目录的根目录中创建名为 `sentiment.py` 的文件。
2. 接下来，将以下代码添加到 `sentiment.py`。
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. 添加文本分析订阅密钥并保存。

### <a name="add-a-route-to-apppy"></a>将路由添加到 `app.py`

在 Flask 应用中创建一个用于调用 `sentiment.py` 的路由。 每当用户在该应用中按下“运行情绪分析”按钮时，就会调用此路由。 与翻译路由一样，此路由将接受 `POST` 请求，因为该函数需要提供参数。

1. 打开 `app.py`，在 `app.py` 的顶部找到 import 语句，并将其更新：

   ```python
   import translate, sentiment
   ```
   现在，我们的 Flask 应用可以使用通过 `sentiment.py` 提供的方法。

2. 将以下代码复制到 `app.py` 的末尾并保存：
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>更新 `index.html`

创建用于运行情绪分析的函数，并在 Flask 应用中添加一个用于调用该函数的路由后，下一步是开始编写应用的 HTML。 以下 HTML 的作用是：

* 将一个用于运行情绪分析的按钮添加到应用
* 添加一个用于解释情绪评分的元素
* 添加一个用于显示情绪评分的元素

1. 打开 `index.html` 并找到以下代码注释：
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. 将代码注释替换为以下 HTML 块：
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>更新 `main.js`

在以下代码中，将使用 HTML 中的内容来构造对 Flask 路由发出的请求。 具体而言，文本区域和语言选择器的内容将分配到变量，然后在请求中一起传递给 `sentiment-analysis` 路由。

然后，代码将循环访问响应，并使用情绪评分更新 HTML。

1. 在 IDE 中，在 `static` 目录中创建名为 `main.js` 的文件。

2. 将以下代码复制到 `static/scripts/main.js` 中：
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>测试情绪分析

让我们在应用中测试情绪分析。

```
flask run
```

导航到提供的服务器地址。 在输入区域中键入文本，选择语言，然后按“翻译”。 随后应会看到翻译结果。 接下来，按下“运行情绪分析”按钮。 随后应会看到两个评分。 如果未看到翻译，请确保已添加订阅密钥。

> [!TIP]
> 如果所做的更改未显示，或者应用不按预期方式工作，请尝试清除缓存，或打开私密/隐匿窗口。

按 **CTRL + C** 终止应用，然后转到下一部分。

## <a name="convert-text-to-speech"></a>将文本转换到语音

应用可以使用[文本转语音 API](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) 将文本转换为类似于人类的自然合成语音。 该服务支持标准语音、神经语音和自定义语音。 我们的示例应用使用了少量的可用声音，有关完整语音列表，请参阅[支持的语言](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)。

在本部分，你将执行以下几项操作：

* 编写一些 Python 代码，以使用文本转语音 API 将文本转换为语音
* 创建一个 Flask 路由用于调用 Python 代码
* 使用一个用于将文本转换为语音的按钮，以及一个用于播放音频的元素来更新 HTML
* 编写 Javascript 代码，使用户能够与 Flask 应用交互

### <a name="call-the-text-to-speech-api"></a>调用文本转语音 API

让我们编写一个函数用于将文本转换为语音。 此函数采用两个参数：`input_text` 和 `voice_font`。 每当用户在该应用中按下“将文本转换为语音”按钮时，就会调用此函数。 `input_text`是调用“翻译文本”后返回的翻译输出，`voice_font` 是语音字体选择器中的 HTML 值。

1. 在工作目录的根目录中创建名为 `synthesize.py` 的文件。

2. 接下来，将以下代码添加到 `synthesize.py`。
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. 添加语音服务订阅密钥并保存。

### <a name="add-a-route-to-apppy"></a>将路由添加到 `app.py`

在 Flask 应用中创建一个用于调用 `synthesize.py` 的路由。 每当用户在该应用中按下“将文本转换为语音”按钮时，就会调用此路由。 与翻译和情绪分析的路由一样，此路由将接受 `POST` 请求，因为该函数需要两个参数：要合成的文本，以及要播放的语音字体。

1. 打开 `app.py`，在 `app.py` 的顶部找到 import 语句，并将其更新：

   ```python
   import translate, sentiment, synthesize
   ```
   现在，我们的 Flask 应用可以使用通过 `synthesize.py` 提供的方法。

2. 将以下代码复制到 `app.py` 的末尾并保存：

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>更新 `index.html`

创建用于将文本转换为语音的函数，并在 Flask 应用中添加一个用于调用该函数的路由后，下一步是开始编写应用的 HTML。 以下 HTML 的作用是：

* 提供语音选择下拉列表
* 添加一个用于将文本转换为语音的按钮
* 添加一个用于播放合成语音的音频元素

1. 打开 `index.html` 并找到以下代码注释：
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. 将代码注释替换为以下 HTML 块：
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, AriaRUS)">English (US) | Female | AriaRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. 接下来，找到以下代码注释：
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. 将代码注释替换为以下 HTML 块：

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. 请务必保存已完成的工作。

### <a name="update-mainjs"></a>更新 `main.js`

在以下代码中，将使用 HTML 中的内容来构造对 Flask 路由发出的请求。 具体而言，翻译和语音字体将分配到变量，然后在请求中一起传递给 `text-to-speech` 路由。

然后，代码将循环访问响应，并使用情绪评分更新 HTML。

1. 在 IDE 中，在 `static` 目录中创建名为 `main.js` 的文件。
2. 将以下代码复制到 `static/scripts/main.js` 中：
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. 即将完成。 要做的最后一件事是在 `main.js` 中添加一些代码，以根据选择的翻译语言自动选择语音字体。 将以下代码块添加到 `main.js`：
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>测试应用

让我们在应用中测试语音合成。

```
flask run
```

导航到提供的服务器地址。 在输入区域中键入文本，选择语言，然后按“翻译”。 随后应会看到翻译结果。 接下来，选择一个语音，然后按下“将文本转换为语音”按钮。 翻译结果应以合成语音的形式播放。 如果未看到翻译，请确保已添加订阅密钥。

> [!TIP]
> 如果所做的更改未显示，或者应用不按预期方式工作，请尝试清除缓存，或打开私密/隐匿窗口。

好了，现已创建一个可以执行翻译、分析情绪和合成语音的正常运行的应用。 请按 **CTRL + C** 终止该应用。 请务必查看其他 [Azure 认知服务](https://docs.microsoft.com/azure/cognitive-services/)。

## <a name="get-the-source-code"></a>获取源代码

此项目的源代码已在 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial) 中提供。

## <a name="next-steps"></a>后续步骤

* [翻译引用](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [文本分析 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [文本到语音 API 参考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
