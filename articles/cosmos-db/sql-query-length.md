---
title: LENGTE in Azure Cosmos DB query taal
description: Meer informatie over de lengte van de SQL-systeem functie in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303712"
---
# <a name="length-azure-cosmos-db"></a>LENGTE (Azure Cosmos DB)
 Retourneert het aantal tekens van de opgegeven tekenreeksexpressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is de teken reeks expressie die moet worden geëvalueerd.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  Het volgende voorbeeld retourneert de lengte van een tekenreeks.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
