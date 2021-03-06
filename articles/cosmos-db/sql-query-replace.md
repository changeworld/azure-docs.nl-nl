---
title: VERVANGEN in Azure Cosmos DB query taal
description: Meer informatie over de functie vervangen van SQL-systeem functies in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302199"
---
# <a name="replace-azure-cosmos-db"></a>VERVANGEN (Azure Cosmos DB)
 Vervangt alle exemplaren van een opgegeven tekenreekswaarde door een andere tekenreekswaarde.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is de teken reeks expressie die moet worden doorzocht.  
  
*str_expr2*  
   Is de teken reeks expressie die moet worden gevonden.  
  
*str_expr3*  
   Is de teken reeks expressie voor het vervangen van exemplaren van *str_expr2* in *str_expr1*.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u `REPLACE` gebruikt in een query.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
