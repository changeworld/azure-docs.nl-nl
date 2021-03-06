---
title: Aangepaste claims aanvragen (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het aanvragen van aangepaste claims.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 44158296faaf238fd72f2360149d3d93f68c5ba0
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085601"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Procedure: aangepaste claims aanvragen met MSAL voor iOS en macOS

Met OpenID Connect Connect kunt u optioneel het retour neren van afzonderlijke claims aanvragen van het user info-eind punt en/of in het ID-token. Een claim aanvraag wordt weer gegeven als een JSON-object dat een lijst met aangevraagde claims bevat. Zie [OpenID Connect Connect Core 1,0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) voor meer informatie.

Met de micro soft Authentication Library (MSAL) voor iOS en macOS kunt u specifieke claims aanvragen in zowel interactieve als Silent-verwervings scenario's. Dit doet u door de para meter `claimsRequest`.

Er zijn meerdere scenario's waarin dit nodig is. Bijvoorbeeld:

- Claims aanvragen buiten de standaardset voor uw toepassing.
- Specifieke combi Naties van de standaard claims aanvragen die niet kunnen worden opgegeven met scopes voor uw toepassing. Als een toegangs token bijvoorbeeld wordt afgewezen als gevolg van ontbrekende claims, kan de toepassing de ontbrekende claims aanvragen met behulp van MSAL.

> [!NOTE]
> Met MSAL wordt de cache van het toegangs token omzeild wanneer een claim aanvraag wordt opgegeven. Het is belang rijk om alleen `claimsRequest` para meter op te geven wanneer er aanvullende claims nodig zijn (in plaats van altijd dezelfde `claimsRequest`-para meter in elke API-aanroep van MSAL te leveren).

`claimsRequest` kunnen worden opgegeven in `MSALSilentTokenParameters` en `MSALInteractiveTokenParameters`:

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` kan worden samengesteld uit een NSStringe weer gave van een JSON-claim aanvraag. 

Doel-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Het kan ook worden gewijzigd door aanvullende specifieke claims aan te vragen:

Doel-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest` moet vervolgens worden ingesteld in de token parameters en worden opgegeven voor een van de MSAL-Api's voor het ophalen van tokens:

Doel-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md)
