---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201946"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Binding-extensies registreren

Met uitzonde ring van HTTP-en timer triggers worden bindingen geïmplementeerd als uitbreidings pakketten. Voer de volgende [DotNet-opdracht add package](/dotnet/core/tools/dotnet-add-package) uit in het Terminal venster om het opslag extensie pakket toe te voegen aan uw project.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Nu kunt u de opslag-uitvoer binding toevoegen aan uw project.  
::: zone-end  