---
title: A PAM-összetevők ismertetése | Microsoft Docs
description: A Privileged Access Management rendelkezik saját összetevőkkel is, de bizonyos összetevői megegyeznek a MIM összetevőivel. Ismerje meg, ezek hogyan működnek együtt.
keywords: ''
author: billmath
ms.author: billmath
manager: mtillman
ms.date: 09/13/2017
ms.topic: article
ms.prod: microsoft-identity-manager
ms.assetid: 6498f68f-36d3-448c-8fe6-649ad5a7f97d
ms.reviewer: mwahl
ms.suite: ems
ms.openlocfilehash: b0a101a06acfdd5b95deb576a7fddfda124a3853
ms.sourcegitcommit: a4f77aae75a317f5277d7d2a3187516cae1e3e19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "64518329"
---
# <a name="understand-the-components-of-pam"></a>A PAM-összetevők megismerése

A Privileged Access Management a rendszergazdai hozzáférést a napi szintű felhasználói fiókoktól elkülönítve kezeli. Ez a megoldás párhuzamos erdőkre támaszkodik:

- *CORP*: Ez az általános célú vállalati erdő, amely egy vagy több tartományt tartalmaz. Előfordulhat, hogy vállalata több erdővel rendelkezik, de a cikkben szereplő példákban az egyszerűség kedvéért egyetlen erdőt és ezen belül egyetlen tartományt feltételeztünk.  
- *PRIV*: Dedikált erdő, kifejezetten ehhez a PAM-forgatókönyvhöz létrehozva. Ez az erdő egyetlen tartományt tartalmaz, amely az egy vagy több CORP tartományból származó rendszerjogosultságú csoportokat és fiókokat kezeli.

A PAM-hez konfigurált MIM megoldás a következő összetevőket tartalmazza:  

- **MIM szolgáltatás**: biztosítja az identitás- és hozzáférés-kezelési feladatok végrehajtásához szükséges üzleti logikát, beleértve a rendszerjogosultságú fiókok felügyeletét és az emelt szintű jogosultsági kérelmek kezelését.
- **MIM portál**: SharePoint-alapú, a SharePoint 2013 által üzemeltetett portál, amely lehetővé teszi a rendszergazdák kezelését, és kezelőfelületet biztosít a konfigurációhoz.
- **MIM szolgáltatás adatbázisa**: SQL Server 2012 vagy 2014 verzión tárolt adatbázis, mely tartalmazza a MIM szolgáltatás számára szükséges azonosítóadatokat és metaadatokat.
- **PAM figyelőszolgáltatás** és **PAM összetevő-szolgáltatás**: két olyan szolgáltatás, amely kezeli a rendszerjogosultságú fiókok életciklusát, és segíti a PRIV AD működését a csoporttagság életciklusa alatt.
- **PowerShell-parancsmagok**: a MIM szolgáltatás és a PRIV AD CORP erdőbeli PAM-rendszergazdákhoz és rendszergazdai fiókokhoz a szükséges időben történő (JIT) hozzáférési jogosultságot kérelmező végfelhasználókhoz tartozó felhasználókkal és csoportokkal való feltöltésére szolgálnak.
- **PAM REST API és mintaportál**: a MIM megoldást a PAM-forgatókönyvbe integráló, jogosultságszint-emelést kérelmező egyéni ügyfelekkel rendelkező fejlesztők részére készült összetevők, melyekhez a PowerShell vagy a SOAP használata nincs szükség. A REST API használatát egy minta webalkalmazás mutatja be.

A telepítést és a konfigurálást követően minden, a PRIV erdőben az áttelepítési eljárás során létrehozott csoport egy SIDHistory-alapú biztonsági csoportmásolat (vagy a Windows Server vNext egy újabb frissítésében egy külső rendszercsoport) lesz, amely az eredeti CORP erdőbeli SID-csoportot tükrözi. Mindemellett amikor a MIM szolgáltatás tagokat vesz fel ezekbe a csoportokba a PRIV erdőben, az adott tagságok időkorlátosak lesznek.

Emiatt, amikor egy felhasználó a PowerShell-parancsmagok használatával jogosultságszint-emelést kér, és a kérelmet jóváhagyja a rendszer, a MIM szolgáltatás a felhasználó fiókját hozzáadja a PRIV erdő egyik csoportjához. Amikor a felhasználó bejelentkezik az emelt jogosultságú fiókkal, a felhasználó Kerberos-jogkivonata tartalmazni fog egy biztonsági azonosítót (SID), mely megegyezik a CORP erdőbeli csoport biztonsági azonosítójával. Mivel a CORP és a PRIV erdő között megbízhatósági kapcsolat van beállítva, a CORP erdőben lévő erőforrások eléréséhez használt rendszerjogosultságú fiók – a Kerberos-csoporttagságot ellenőrző erőforrás szempontjából – az adott erőforrás biztonsági csoportjának tagjává válik. Ezt a Kerberos erdők közötti hitelesítése biztosítja.

Mindemellett ezek a tagságok időkorlátosak, így az előre beállított idő elteltével a felhasználó rendszergazdai fiókja már nem lesz tagja a továbbiakban a PRIV erdőbeli csoportnak. Ennek eredményeképpen a fiók nem használható többé a további erőforrások eléréséhez.
