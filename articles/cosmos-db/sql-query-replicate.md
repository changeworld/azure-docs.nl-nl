---
title: REPLICEREN in Azure Cosmos DB query taal
description: Meer informatie over de functie voor het REPLICEREN van SQL-functies in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302182"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICEREN (Azure Cosmos DB)
 Herhaalt een tekenreekswaarde een opgegeven aantal keren.
  
## <a name="syntax"></a>Syntaxis
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.
  
*num_expr*  
   Een numerieke expressie is. Als *num_expr* negatief of niet eindig is, is het resultaat niet gedefinieerd.
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een tekenreeksexpressie.
  
## <a name="remarks"></a>Opmerkingen
  De maximale lengte van het resultaat is 10.000 tekens, dat wil zeggen (length (*str_expr*) * *num_expr*) < = 10.000.

## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u `REPLICATE` gebruikt in een query.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Hier volgt de resultatenset.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
