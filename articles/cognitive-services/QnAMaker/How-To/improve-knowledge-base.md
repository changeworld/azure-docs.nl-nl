---
title: Knowledge Base-QnA Maker verbeteren
titleSuffix: Azure Cognitive Services
description: Verbeter de kwaliteit van uw kennis basis met actief onderwijs. Beoordeling, accepteren of afwijzen, toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: diberry
ms.openlocfilehash: dea2bf3b34ca336f3932dd85bf587184ab6881db
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220686"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Actief leren gebruiken om uw Knowledge Base te verbeteren

Met [actief leren](../Concepts/active-learning-suggestions.md) kunt u de kwaliteit van uw kennis basis verbeteren door alternatieve vragen te stellen, op basis van de gebruikers inzendingen, naar uw vraag en antwoord paar. U kunt deze suggesties bekijken, ofwel toevoegen aan bestaande vragen of afwijzen.

Uw kennis database wordt niet automatisch gewijzigd. Als u een wijziging wilt door voeren, moet u de suggesties accepteren. Deze suggesties Voeg vragen toe, maar u kunt geen bestaande vragen wijzigen of verwijderen.


## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Upgrade uw runtime versie om actief leren te gebruiken

Actief leren wordt ondersteund in runtime versie 4.4.0 en hoger. Als uw Knowledge Base is gemaakt in een eerdere versie, moet u [de runtime upgraden](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) om deze functie te gebruiken.

## <a name="turn-on-active-learning-to-see-suggestions"></a>Actief leren inschakelen om suggesties te bekijken

Actief leren is standaard uitgeschakeld. Schakel deze in om voorgestelde vragen te bekijken. Nadat u actief leren hebt ingeschakeld, moet u gegevens van de client-app naar QnA Maker verzenden. Zie [de architectuur stroom voor het gebruik van GenerateAnswer en Train api's van een bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)voor meer informatie.

1. Selecteer **publiceren** om de Knowledge Base te publiceren. Actieve leer query's worden alleen verzameld van het GenerateAnswer API prediction-eind punt. De query's naar het test venster in de QnA Maker Portal hebben geen invloed op actief leren.

1. Als u actief leren wilt inschakelen in de QnA Maker Portal, gaat u naar de rechter bovenhoek en selecteert u uw **naam**. Ga naar [**Service-instellingen**](https://www.qnamaker.ai/UserSettings).

    ![Schakel de voorgestelde vraag van het actieve leer proces in op de pagina Service-instellingen. Selecteer uw gebruikers naam in het menu rechtsboven en selecteer vervolgens Service-instellingen.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Zoek de QnA Maker-service en schakel vervolgens **actief leren**in.

    > [!div class="mx-imgBorder"]
    > [![op de pagina Service-instellingen de functie actief leren in of uit. Als u de functie niet kunt in-of uitschakelen, moet u mogelijk een upgrade van uw service uitvoeren.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > De exacte versie van de voor gaande afbeelding wordt alleen weer gegeven als voor beeld. Uw versie kan afwijken.

    Zodra **actief leren** is ingeschakeld, worden met de Knowledge Base regel matig nieuwe vragen voorgesteld op basis van door de gebruiker ingediende vragen. U kunt **actief leren** uitschakelen door de instelling opnieuw in te scha kelen.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Een voor stel voor een actieve learning in de Knowledge Base accepteren

Met actief onderwijs wijzigt u de Knowledge Base of Search Service na het goed keuren van de suggestie en vervolgens op te slaan en te trainen. Als u de suggestie goedkeurt, wordt deze toegevoegd als een alternatieve vraag.

1. Als u de voorgestelde vragen wilt bekijken, selecteert u op de pagina Knowledge Base **bewerken** de optie **weergave opties**en selecteert u **actieve Learning suggesties weer geven**.

    [Selecteer ![in het gedeelte bewerken van de Portal de optie suggesties weer geven om de nieuwe vragen van het actieve leer proces te bekijken.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filter de Knowledge Base met vraag-en antwoord paren om alleen suggesties weer te geven door **filteren op suggesties**te selecteren.

    [![gebruik de wissel knop filteren op suggesties om alleen de voorgestelde suggesties van het actieve leer proces weer te geven.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Elk QnA paar suggereert de nieuwe vraag alternatieven met een vinkje, `✔`, om de vraag te accepteren of een `x` om de suggesties af te wijzen. Schakel het selectie vakje in om de vraag toe te voegen.

    [![suggesties van de voorgestelde vraag van het actieve leer proces selecteren of afwijzen door het groene vinkje of rode markering voor verwijderen te selecteren.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    U kunt _alle suggesties_ toevoegen of verwijderen door alles **toevoegen** of **Alles negeren** te selecteren in de contextuele werk balk.

1. Selecteer **opslaan en trainen** om de wijzigingen in de Knowledge Base op te slaan.

1. Selecteer **publiceren** om de wijzigingen beschikbaar te maken vanuit de [GenerateAnswer-API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Wanneer 5 of meer soort gelijke query's worden geclusterd, wordt elke 30 minuten QnA Maker suggesties voor de alternatieve vragen die u kunt accepteren of afwijzen.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Architectuur stroom voor het gebruik van GenerateAnswer en Train Api's vanuit een bot

Een bot of andere client toepassing moet de volgende architectuur stroom gebruiken om actief leren te gebruiken:

* Bot [haalt het antwoord op uit de Knowledge Base](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) met de GENERATEANSWER-API met behulp van de eigenschap `top` om een aantal antwoorden op te halen.
* Bot bepaalt expliciete feedback:
    * Met uw eigen [aangepaste bedrijfs logica](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)filtert u de lage scores.
    * In de bot of client-toepassing geeft u een lijst met mogelijke antwoorden op de gebruiker weer en het geselecteerde antwoord van de gebruiker ophalen.
* Bot [stuurt het geselecteerde antwoord terug naar QnA Maker](#bot-framework-sample-code) met de [trein-API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Gebruik de eigenschap Top in de GenerateAnswer-aanvraag om verschillende overeenkomende antwoorden te verkrijgen

Bij het indienen van een vraag om QnA Maker voor een antwoord, stelt de eigenschap `top` van de JSON-hoofd tekst het aantal antwoorden in dat moet worden geretourneerd.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Gebruik de eigenschap Score samen met bedrijfs logica om een lijst met antwoorden te krijgen voor weer geven gebruiker

Wanneer de client toepassing (zoals een chat-bot) het antwoord ontvangt, worden de belangrijkste drie vragen geretourneerd. Gebruik de eigenschap `score` om de nabijheid van de scores te analyseren. Dit proximity-bereik wordt bepaald door uw eigen bedrijfs logica.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Opvolging van client toepassing wanneer vragen vergelijk bare scores hebben

Uw client toepassing geeft de vragen weer met een optie voor de gebruiker om _de enige vraag_ te selecteren die de bedoeling het meest vertegenwoordigt.

Zodra de gebruiker een van de bestaande vragen selecteert, stuurt de client toepassing de keuze van de gebruiker als feedback met behulp van de Train API van QnA Maker. Met deze feedback wordt de actieve lus voor het leren van feedback voltooid.

## <a name="train-api"></a>API trainen

Actieve Learning feedback wordt verzonden naar QnA Maker met de trein-API POST-aanvraag. De API-hand tekening is:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Eigenschap HTTP-aanvraag|Naam|Type|Doel|
|--|--|--|--|
|URL-route parameter|Knowledge Base-ID|tekenreeks|De GUID voor uw Knowledge Base.|
|Aangepast subdomein|Resource naam QnAMaker|tekenreeks|De resource naam wordt gebruikt als het aangepaste subdomein voor uw QnA Maker. Dit is beschikbaar op de pagina instellingen nadat u de Knowledge Base hebt gepubliceerd. Het wordt weer gegeven als de `host`.|
|Header|Content-Type|tekenreeks|Het media type van de hoofd tekst die naar de API is verzonden. De standaard waarde is: `application/json`|
|Header|Autorisatie|tekenreeks|Uw eindpunt sleutel (EndpointKey XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX).|
|Bericht tekst|JSON-object|JSON|De trainings feedback|

De JSON-hoofd tekst heeft verschillende instellingen:

|Eigenschap van JSON-hoofd tekst|Type|Doel|
|--|--|--|--|
|`feedbackRecords`|matrix|Lijst met feedback.|
|`userId`|tekenreeks|De gebruikers-ID van de persoon die de voorgestelde vragen accepteert. De indeling van de gebruikers-ID is Maxi maal. Een e-mail adres kan bijvoorbeeld een geldige gebruikers-ID zijn in uw architectuur. Optioneel.|
|`userQuestion`|tekenreeks|De exacte tekst van de query van de gebruiker. Vereist.|
|`qnaID`|getal|De ID van de vraag die in het [GenerateAnswer-antwoord](metadata-generateanswer-usage.md#generateanswer-response-properties)is gevonden. |

Een voor beeld van een JSON-bericht ziet er als volgt uit:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Een geslaagde reactie retourneert de status 204 en geen JSON-antwoord tekst.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Batch-veel feedback records in één aanroep

In de toepassing aan de client zijde, zoals een bot, kunt u de gegevens opslaan en vervolgens veel records in één JSON-hoofd tekst in de `feedbackRecords` matrix verzenden.

Een voor beeld van een JSON-bericht ziet er als volgt uit:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Voorbeeld code voor bot Framework

Uw bot-Framework code moet de Train API aanroepen als de query van de gebruiker moet worden gebruikt voor actief leren. Er zijn twee stukjes code die u kunt schrijven:

* Bepalen of de query moet worden gebruikt voor actief leren
* Query terugsturen naar Train API van QnA Maker voor actief leren

In het voor [beeld van Azure bot](https://aka.ms/activelearningsamplebot)zijn beide activiteiten geprogrammeerd.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Voorbeeld C# code voor Train API met bot Framework 4. x

De volgende code laat zien hoe u gegevens kunt terugsturen naar QnA Maker met de Train API. Dit [volledige code voorbeeld](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) is beschikbaar op github.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Voor beeld van node. js-code voor Train API with bot Framework 4. x

De volgende code laat zien hoe u gegevens kunt terugsturen naar QnA Maker met de Train API. Dit [volledige code voorbeeld](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) is beschikbaar op github.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Actief leren wordt opgeslagen in de geëxporteerde kennis basis

Wanneer voor uw app actief leren is ingeschakeld en u de app exporteert, worden in de `SuggestedQuestions` kolom in het TSV-bestand de actieve leer gegevens bewaard.

De `SuggestedQuestions` kolom is een JSON-object met informatie over impliciete, `autosuggested`en expliciete `usersuggested` feedback. Een voor beeld van dit JSON-object voor één door de gebruiker ingediende vraag van `help` is:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

U kunt ook de API voor het downloaden van wijzigingen gebruiken om deze aanpassingen te controleren met behulp van REST of een van de op taal gebaseerde Sdk's:
* [REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Wanneer u deze app opnieuw importeert, gaat het actieve leer proces verder met het verzamelen van informatie en worden suggesties voor uw Knowledge Base aanbevolen.



## <a name="best-practices"></a>Aanbevolen procedures

Zie [Aanbevolen procedures](../Concepts/best-practices.md#active-learning)voor aanbevolen procedures voor het gebruik van actief leren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meta gegevens gebruiken met GenerateAnswer-API](metadata-generateanswer-usage.md)
