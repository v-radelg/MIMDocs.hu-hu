---
title: A PAM szoftverkövetelményei | Microsoft Docs
description: A Privileged Access Management sikeres üzembe helyezéséhez szükséges hardver- és szoftverkövetelmények
keywords: ''
author: billmath
ms.author: billmath
manager: mtillman
ms.date: 09/06/2017
ms.topic: article
ms.prod: microsoft-identity-manager
ms.assetid: 82a9085c-9667-4b3b-8079-657eab1d1e58
ms.reviewer: mwahl
ms.suite: ems
ms.openlocfilehash: 4838e9e8a495866902a78e713bb3b226eaf9def1
ms.sourcegitcommit: a4f77aae75a317f5277d7d2a3187516cae1e3e19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "64518835"
---
# <a name="hardware-and-software-requirements"></a>Hardver- és szoftverkövetelmények

A mögöttes szoftverplatformok rendszerkövetelményein kívül a Privileged Access Management nem rendelkezik további hardverkövetelményekkel. Ügyeljen rá, hogy rendelkezésre álljon elegendő memória vagy lemezterület, és hogy elérhető legyen a hálózati kapcsolat.

> [!IMPORTANT]
> Ez a cikk az alapszintű telepítéshez szükséges minimális követelményeket ismerteti. Nem alkalmas a teljesítmény, a méretezhetőség és a magas rendelkezésre állás bemutatására. A nagyméretű vállalatok vagy éles környezetek esetében nem ajánlott központi telepítési topológiát jelent.

## <a name="installing-from-software-packages"></a>Telepítés szoftvercsomagokból

A következő szoftver letölthető a TechNet Evaluation Center vagy az MSDN webhelyéről:

- Microsoft Identity Manager 2016
  - Szolgáltatás és portál: tartalmazza a MIM szolgáltatáshoz és a MIM-portálhoz, illetve a PAM-telepítéshez szükséges telepítőfájlokat
  - Beépülő modulok és bővítmények: tartalmazzák a kérelmező PowerShell-parancsmagok telepítőjét

A következő szoftver letölthető a GitHub webhelyről:

- [PAMSamplePortal](https://github.com/Azure/identity-management-samples): minta webalkalmazást tartalmaz a REST API

## <a name="required-software"></a>Szükséges szoftverek

- Windows Server 2012 R2
- Windows 10 Enterprise
- SQL Server 2012 Service Pack 1 vagy SQL Server 2014

## <a name="evaluation-software"></a>Próbaszoftver

Ha Ön nem rendelkezik Windows-, SQL Server- vagy Windows Server-licenccel, letöltheti ezek próbaverzióit.

### <a name="technet-evaluation-center"></a>TechNet Evaluation Center

- [Windows Server 2012 R2](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2)
- [Windows 10 Enterprise](https://www.microsoft.com/evalcenter/evaluate-windows-10-enterprise)

### <a name="microsoft-download-center"></a>Microsoft letöltőközpont

- [SQL Server](https://www.microsoft.com/download/details.aspx?id=29066)  
- [A SharePoint Foundation 2013 SP1 és előfeltételei](https://www.microsoft.com/download/details.aspx?id=42039)

## <a name="hardware-requirements"></a>Hardverkövetelmények

Minden PAM-összetevő esetében tekintse át a szoftverek rendszerkövetelményeit.

A CORPDC géphez:

- [Windows Server 2012 R2](https://technet.microsoft.com/library/dn303418.aspx) vagy újabb

A CORPWKSTN géphez:

- [Windows 10](https://technet.microsoft.com/windows/dn798752.aspx)

A PRIVDC géphez:

- [Windows Server 2012 R2](https://technet.microsoft.com/library/dn303418.aspx)

A PAMSRV géphez:

- [Windows Server 2012 R2](https://technet.microsoft.com/library/dn303418.aspx)
- [SQL Server 2012](https://msdn.microsoft.com/library/ms143506(sql.110).aspx) vagy [SQL Server 2014](https://msdn.microsoft.com/library/ms143506(v=sql.120).aspx)
