---
title: Snabb start för Java-klient bibliotek Entitetssökning i Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: f69b9b989a93949f9a0441676c81af7480fb968f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "87375068"
---
Använd den här snabb starten för att börja söka efter entiteter med Entitetssökning i Bing klient bibliotek för Java. Även om Entitetssökning i Bing har en REST API som är kompatibel med de flesta programmeringsspråk, är klient biblioteket ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Förutsättningar

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Entitetssökning i Bing klient bibliotek för Java

Installera Entitetssökning i Bing klient biblioteks beroenden med hjälp av maven, Gradle eller ett annat beroende hanterings system. Maven POM-filen kräver deklarationen:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. Skapa en variabel för din prenumerationsnyckel

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Skapa en sökklient

1. Implementera klienten `dominantEntityLookup`, som kräver din API-slutpunkt och en instans av klassen `ServiceClientCredentials`. Du kan använda den globala slut punkten nedan eller den [anpassade slut domänen](../../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Så här implementerar du `ServiceClientCredentials`:

   1. Åsidosätt funktionen `applyCredentialsFilter()` med ett `OkHttpClient.Builder`-objekt som parameter. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. I `applyCredentialsFilter()` anropar du `builder.addNetworkInterceptor()`. Skapa ett nytt `Interceptor`-objekt och åsidosätt dess `intercept()`-metod för att ta emot ett `Chain`-avbrottsobjekt.

       ```java
       //...
       builder.addNetworkInterceptor(
           new Interceptor() {
               @Override
               public Response intercept(Chain chain) throws IOException {
               //...    
               }
           });
       ///...
       ```

   3. Skapa variabler för din begäran i funktionen `intercept`. Använd `Request.Builder()` till att bygga din begäran. Lägg till prenumerationsnyckeln i `Ocp-Apim-Subscription-Key`-rubriken och returnera `chain.proceed()` i begärandeobjektet.
            
       ```java
       //...
       public Response intercept(Chain chain) throws IOException {
           Request request = null;
           Request original = chain.request();
           Request.Builder requestBuilder = original.newBuilder()
                   .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
           request = requestBuilder.build();
           return chain.proceed(request);
       }
       //...
       ```
## <a name="send-a-request-and-receive-a-response"></a>Skicka en begäran och ta emot ett svar

1. Skapa en ny instans av sökklienten med din prenumerationsnyckel. Använd `client.entities().search()` till att skicka en sökbegäran för sökfrågan `satya nadella` och få ett svar. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Om några entiteter returneras konverterar du dem till en lista. Gå igenom dem och skriv ut den dominerande entiteten.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entries = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entries) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API för entitetsökning i Bing?](../../overview.md)
