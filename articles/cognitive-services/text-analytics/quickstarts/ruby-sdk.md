---
title: 快速入门：使用 Ruby SDK 调用文本分析认知服务
titleSuffix: Azure Cognitive Services
description: 获取信息和代码示例，以便快速完成 Azure 认知服务中的文本分析 API 的使用入门。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 7def77c0b1cf99fcc2cee77a28782dddaf2ac45d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992924"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>快速入门：使用 Ruby SDK 调用文本分析服务

<a name="HOLTop"></a>


根据本快速入门中的说明，开始使用用于 Ruby 的文本分析 SDK 来分析语言。 虽然[文本分析](//go.microsoft.com/fwlink/?LinkID=759711) REST API 与大多数编程语言兼容，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb) 上找到此示例的源代码。

有关 API 的技术文档，请参阅 [API 定义](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

还必须拥有在注册期间生成的[终结点和访问密钥](../How-tos/text-analytics-how-to-access-key.md)。 

根据[此处](https://rubyinstaller.org/downloads/)介绍的系统体系结构，下载并安装 Ruby 2.5.5 版本。

> [!Tip]
>  虽然可以使用 Ruby 直接调用 [REST API 终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)，但使用 `Microsoft.Azure.CognitiveServices.TextAnalytics` SDK 可以轻松地调用此服务，不需对 JSON 进行序列化和反序列化操作。
>
> 一些有用链接：
> - [Ruby gem 页](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
> - [GitHub 上的 SDK 代码](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics/lib/v2.1/generated/azure_cognitiveservices_textanalytics)

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>创建 Ruby 项目并安装 SDK

1. 创建新的 Ruby 项目并添加名为 `Gemfile` 的新文件
2. 向项目添加文本分析 SDK，方法是将以下代码添加到 `Gemfile`。

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>创建文本分析客户端

1. 在最喜爱的编辑器或 IDE 中，创建名为 `TextAnalyticsExamples.rb` 的新文件。 导入文本分析 SDK。

2. 文本分析客户端将使用凭据对象。 使用 `CognitiveServicesCredentials.new()` 创建它并传递订阅密钥。

3. 使用正确的文本分析终结点创建此客户端。

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>情绪分析

使用文本分析 SDK 或 API 时，可以针对一组文本记录执行情绪分析。 以下示例显示了多个文档的情绪分数。

1. 创建名为 `SentimentAnalysisExample()` 的新函数，该函数将上面创建的文本分析客户端用作参数。

2. 定义一组需要分析的 `MultiLanguageInput` 对象。 为每个对象添加语言和文本。 此 ID 可以是任意值。

    ```ruby
    def SentimentAnalysisExample(client)
      # The documents to be analyzed. Add the language of the document. The ID can be any value.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'en'
      input_1.text = 'I had the best day of my life.'
    
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'en'
      input_2.text = 'This was a waste of my time. The speaker put me to sleep.'
    
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'es'
      input_3.text = 'No tengo dinero ni nada que dar...'
    
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'it'
      input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ```

3. 在同一函数中，将文档组合到一个列表中。 将它添加到 `MultiLanguageBatchInput` 对象的 `documents` 字段。 

4. 调用客户端的 `sentiment()` 函数，使用 `MultiLanguageBatchInput` 对象作为参数来发送文档。 如果返回了任何结果，请将其输出。
    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.sentiment(
          multi_language_batch_input: input_documents
      )
      
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
          puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
      end
    end
    ```

5. 调用 `SentimentAnalysisExample()` 函数。

    ```ruby
    SentimentAnalysisExample(textAnalyticsClient)
    ```

### <a name="output"></a>输出

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>语言检测

文本分析服务可以从大量的语言和区域设置中检测出文本文档的语言。 以下示例显示多个文档撰写时使用的语言。

1. 创建名为 `DetectLanguageExample()` 的新函数，该函数将上面创建的文本分析客户端用作参数。 

2. 定义一组需要分析的 `LanguageInput` 对象。 为每个对象添加语言和文本。 此 ID 可以是任意值。

    ```ruby
    def DetectLanguageExample(client)
       # The documents to be analyzed.
       language_input_1 = LanguageInput.new
       language_input_1.id = '1'
       language_input_1.text = 'This is a document written in English.'
    
       language_input_2 = LanguageInput.new
       language_input_2.id = '2'
       language_input_2.text = 'Este es un document escrito en Español..'
    
       language_input_3 = LanguageInput.new
       language_input_3.id = '3'
       language_input_3.text = '这是一个用中文写的文件'
    ```

3. 在同一函数中，将文档组合到一个列表中。 将它添加到 `LanguageBatchInput` 对象的 `documents` 字段。 

4. 调用客户端的 `detect_language()` 函数，使用 `LanguageBatchInput` 对象作为参数来发送文档。 如果返回了任何结果，请将其输出。
    ```ruby
       input_documents = LanguageBatchInput.new
       input_documents.documents = [language_input_1, language_input_2, language_input_3]
    
    
       result = client.detect_language(
           language_batch_input: input_documents
       )
    
       if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
         puts '===== LANGUAGE DETECTION ====='
         result.documents.each do |document|
           puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
         end
       else
         puts 'No results data..'
       end
     end
    ```

5. 调用 `DetectLanguageExample` 函数

    ```ruby
    DetectLanguageExample(textAnalyticsClient)
    ```

### <a name="output"></a>输出

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>实体识别

文本分析服务可以区分并提取文本文档中的不同实体（人物、位置和事物）。 以下示例显示了在多个示例文档中发现的实体。

1. 创建名为 `Recognize_Entities()` 的新函数，该函数将上面创建的文本分析客户端用作参数。

2. 定义一组需要分析的 `MultiLanguageInput` 对象。 为每个对象添加语言和文本。 此 ID 可以是任意值。

    ```ruby
      def RecognizeEntitiesExample(client)
        # The documents to be analyzed.
        input_1 = MultiLanguageInput.new
        input_1.id = '1'
        input_1.language = 'en'
        input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'
    
        input_2 = MultiLanguageInput.new
        input_2.id = '2'
        input_2.language = 'es'
        input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. 在同一函数中，将文档组合到一个列表中。 将它添加到 `MultiLanguageBatchInput` 对象的 `documents` 字段。 

4. 调用客户端的 `entities()` 函数，使用 `MultiLanguageBatchInput` 对象作为参数来发送文档。 如果返回了任何结果，请将其输出。

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
        input_documents.documents = [input_1, input_2]
    
        result = client.entities(
            multi_language_batch_input: input_documents
        )
    
        if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
          puts '===== ENTITY RECOGNITION ====='
          result.documents.each do |document|
            puts "Document ID: #{document.id}"
              document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                  puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
              end
          end
        else
          puts 'No results data..'
        end
      end
    ```

5. 调用 `RecognizeEntitiesExample` 函数
    ```ruby
    RecognizeEntitiesExample(textAnalyticsClient)
    ```

### <a name="output"></a>输出

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>关键短语提取

文本分析服务可以提取句子中的关键短语。 以下示例显示了在多个采用多种语言的示例文档中发现的实体。

1. 创建名为 `KeyPhraseExtractionExample()` 的新函数，该函数将上面创建的文本分析客户端用作参数。

2. 定义一组需要分析的 `MultiLanguageInput` 对象。 为每个对象添加语言和文本。 此 ID 可以是任意值。

    ```ruby
    def KeyPhraseExtractionExample(client)
      # The documents to be analyzed.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'ja'
      input_1.text = '猫は幸せ'
  
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'de'
      input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'
  
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'en'
      input_3.text = 'My cat is stiff as a rock.'
  
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'es'
      input_4.text = 'A mi me encanta el fútbol!'
      ```

3. 在同一函数中，将文档组合到一个列表中。 将它添加到 `MultiLanguageBatchInput` 对象的 `documents` 字段。 

4. 调用客户端的 `key_phrases()` 函数，使用 `MultiLanguageBatchInput` 对象作为参数来发送文档。 如果返回了任何结果，请将其输出。

    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.key_phrases(
          multi_language_batch_input: input_documents
      )
    
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
          puts "Document Id: #{document.id}"
          puts '  Key Phrases'
          document.key_phrases.each do |key_phrase|
            puts "    #{key_phrase}"
          end
        end
      else
        puts 'No results data..'
      end
    end
    ```

5. 调用 `KeyPhraseExtractionExample` 函数

    ```ruby
    KeyPhraseExtractionExample(textAnalyticsClient)
    ```

### <a name="output"></a>输出

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 进行文本分析](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另请参阅

 [文本分析概述](../overview.md)  
 [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)
