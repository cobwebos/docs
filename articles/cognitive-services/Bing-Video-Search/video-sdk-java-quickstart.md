---
title: 必应视频搜索 SDK Java 快速入门 | Microsoft Docs
description: 如何设置必应视频搜索 SDK 控制台应用程序。
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/18/2018
ms.author: v-gedod
ms.openlocfilehash: 1d9ccd88de4b91060861c39ab20f6a203c820384
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366693"
---
# <a name="bing-video-search-sdk-java-quickstart"></a>必应视频搜索 SDK Java 快速入门

必应视频搜索 SDK 提供了用于视频查询以及对结果进行分析的 REST API 功能。

Git Hub 上提供了 [Java 必应视频搜索 SDK 示例的源代码](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch)。

## <a name="application-dependencies"></a>应用程序依赖项
在“搜索”下获取[认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/)。 通过使用 Maven、Gradle 或其他依赖项管理系统安装必应视频搜索 SDK 依赖项。 Maven POM 文件需要以下声明：
```
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```
## <a name="video-search-client"></a>视频搜索客户端
向类实现中添加以下导入。
```
import com.microsoft.azure.cognitiveservices.videosearch.*;
import com.microsoft.azure.cognitiveservices.videosearch.Freshness;
import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
import com.microsoft.azure.cognitiveservices.videosearch.implementation.TrendingVideosInner;
import com.microsoft.azure.cognitiveservices.videosearch.implementation.VideoDetailsInner;
import com.microsoft.azure.cognitiveservices.videosearch.implementation.VideoSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.videosearch.implementation.VideosInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

```
实现 **VideoSearchAPIImpl** 客户端，它需要 **ServiceClientCredentials** 类的一个实例。
```
public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
    return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
            new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                    builder.addNetworkInterceptor(
                            new Interceptor() {
                                @Override
                                public Response intercept(Chain chain) throws IOException {
                                    Request request = null;
                                    Request original = chain.request();
                                    // Request customization: add request headers
                                    Request.Builder requestBuilder = original.newBuilder()
                                            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                                    request = requestBuilder.build();
                                    return chain.proceed(request);
                                }
                            });
                }
            });
}
```
搜索有关“SwiftKey”的视频。 验证结果数。 输出第一个视频结果的 ID、名称和 URL。
```
public static void VideoSearch(String subscriptionKey)
{
    VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);

    try
    {
        VideosInner videoResults = client.searchs().list("SwiftKey");
        System.out.println("\r\nSearch videos for query \"SwiftKey\"");

        if (videoResults == null)
        {
            System.out.println("Didn't see any video result data..");
        }
        else
        {
            if (videoResults.value().size() > 0)
            {
                VideoObject firstVideoResult = videoResults.value().get(0);

                System.out.println(String.format("Video result count: %d", videoResults.value().size()));
                System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
                System.out.println(String.format("First video name: %s", firstVideoResult.name()));
                System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
            }
            else
            {
                System.out.println("Couldn't find video results!");
            }
        }
    }

    catch (ErrorResponseException ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }

}


```
搜索有关“Bellevue Trailer”的视频。 通过 *free*、*short* 和 *1080p resolution* 参数对搜索进行筛选。 验证结果数。 输出第一个视频结果的 ID、名称和 URL。
```
public static void VideoSearchWithFilters(String subscriptionKey)
{
    VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);

    try
    {
        VideosInner videoResults = client.searchs().list("Bellevue Trailer", "en-us", null, null, null, null, null,
                Freshness.MONTH, null, VideoLength.SHORT, "en-us", null, VideoPricing.FREE, VideoResolution.HD1080P, null, null, null, null);
        System.out.println("\r\nSearch videos for query \"Bellevue Trailer\" free, short and 1080p resolution");

        if (videoResults == null)
        {
            System.out.println("Didn't see any video result data..");
        }
        else
        {
            if (videoResults.value().size() > 0)
            {
                VideoObject firstVideoResult = videoResults.value().get(0);
                System.out.println(String.format("Video result count: %d", videoResults.value().size()));
                System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
                System.out.println(String.format("First video name: %s", firstVideoResult.name()));
                System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
            }
            else
            {
                System.out.println("Couldn't find video results!");
            }
        }
    }

    catch (ErrorResponseException ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
搜索热门视频。 验证 **bannerTiles** 和 **categories** 参数。
```
public static void VideoTrending(String subscriptionKey)
{
    VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);

    try
    {
        TrendingVideosInner trendingResults = client.trendings().list();
        System.out.println("\r\nSearch trending videos");

        if (trendingResults == null)
        {
            System.out.println("Didn't see any trending video data..");
        }
        else
        {
            // Verify the banner tiles
            if (trendingResults.bannerTiles().size() > 0)
            {
                TrendingVideosTile firstBannerTile = trendingResults.bannerTiles().get(0);
                System.out.println(
                        String.format("Banner tile count: {trendingResults.BannerTiles.Count}"));
                System.out.println(
                        String.format("First banner tile text: {firstBannerTile.Query.Text}"));
                System.out.println(
                        String.format("First banner tile url: {firstBannerTile.Query.WebSearchUrl}"));
            }
            else
            {
                System.out.println("Couldn't find banner tiles!");
            }

            // Verify the categories
            if (trendingResults.categories().size() > 0)
            {
                TrendingVideosCategory firstCategory = trendingResults.categories().get(0);
                System.out.println(
                        String.format("Category count: %d", trendingResults.categories().size()));
                System.out.println(
                        String.format("First category title: %s", firstCategory.title()));

                if (firstCategory.subcategories().size() > 0)
                {
                    TrendingVideosSubcategory firstSubCategory = firstCategory.subcategories().get(0);
                    System.out.println(
                            String.format("SubCategory count: %d", firstCategory.subcategories().size()));
                    System.out.println(
                            String.format("First sub category title: %s", firstSubCategory.title()));

                    if (firstSubCategory.tiles().size() > 0)
                    {
                        TrendingVideosTile firstTile = firstSubCategory.tiles().get(0);
                        System.out.println(
                                String.format("Tile count: %d", firstSubCategory.tiles().size()));
                        System.out.println(
                                String.format("First tile text: %s", firstTile.query().text()));
                        System.out.println(
                                String.format("First tile url: %s", firstTile.query().webSearchUrl()));
                    }
                    else
                    {
                        System.out.println("Couldn't find tiles!");
                    }
                }
                else
                {
                    System.out.println("Couldn't find subcategories!");
                }
            }
            else
            {
                System.out.println("Couldn't find categories!");
            }
        }
    }

    catch (ErrorResponseException ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }

}

```
搜索有关“Bellevue Trailer”的视频，然后搜索第一个视频结果的详细信息。
```
public static void VideoDetail(String subscriptionKey)
{
    VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);

    try
    {
        VideosInner videoResults = client.searchs().list("Bellevue Trailer");
        if (videoResults.value().size() > 0)
        {
            VideoObject firstVideo = videoResults.value().get(0);
            List<VideoInsightModule> modules = new ArrayList<VideoInsightModule>();
            modules.add(VideoInsightModule.ALL);
            VideoDetailsInner videoDetail = client.details().list("Bellevue Trailer", null, null, null, null, null,
                    firstVideo.videoId(), modules, "en-us", null, null, null, null, null);
                    //nc(query: "Bellevue Trailer", id: firstVideo.VideoId, modules: modules).Result;
            System.out.println(
                    String.format("\r\nSearch detail for video id={firstVideo.VideoId}, name=%s", firstVideo.name()));

            if (videoDetail != null)
            {
                if (videoDetail.videoResult() != null)
                {
                    System.out.println(
                            String.format("Expected video id: %s", videoDetail.videoResult().videoId()));
                    System.out.println(
                            String.format("Expected video name: %s", videoDetail.videoResult().name()));
                    System.out.println(
                            String.format("Expected video url: %s", videoDetail.videoResult().contentUrl()));
                }
                else
                {
                    System.out.println("Couldn't find expected video!");
                }

                if (videoDetail.relatedVideos().value().size() > 0)
                {
                    VideoObject firstRelatedVideo = videoDetail.relatedVideos().value().get(0);
                    System.out.println(
                            String.format("Related video count: %d", videoDetail.relatedVideos().value().size() ));
                    System.out.println(
                            String.format("First related video id: %s", firstRelatedVideo.videoId()));
                    System.out.println(
                            String.format("First related video name: %s", firstRelatedVideo.name()));
                    System.out.println(
                            String.format("First related video url: %s", firstRelatedVideo.contentUrl()));
                }
                else
                {
                    System.out.println("Couldn't find any related video!");
                }
            }
            else
            {
                System.out.println("Couldn't find detail about the video!");
            }
        }
        else
        {
            System.out.println("Couldn't find video results!");
        }
    }

    catch (ErrorResponseException ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}
```
将本文中描述的方法添加到一个类中，并为该类添加一个用于执行代码的主函数。
```
package videoSDK;
import com.microsoft.azure.cognitiveservices.videosearch.*;

public class VideoSDK {

    public static void main(String[] args) {
        
        
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
        VideoSDK.VideoSearchWithFilters("YOUR-SUBSCRIPTION-KEY");
        VideoSDK.VideoTrending("YOUR-SUBSCRIPTION-KEY");
        VideoSDK.VideoDetail("YOUR-SUBSCRIPTION-KEY");

    }

    // Include the methods described in this article.
}
```
## <a name="next-steps"></a>后续步骤

[认知服务 Java SDK 示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
