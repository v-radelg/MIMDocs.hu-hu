---
title: Rétegmodell PAM-környezetben | Microsoft Docs
description: Információk a rétegmodellről, amely a biztonsági kockázat alapján különíti el a rendszert.
keywords: ''
author: billmath
ms.author: billmath
manager: mtillman
ms.date: 08/30/2017
ms.topic: article
ms.prod: microsoft-identity-manager
ms.assetid: c6e3cd02-1e32-4194-a8ed-3a0b3d022a43
ms.reviewer: mwahl
ms.suite: ems
ms.openlocfilehash: 8e7b7217714f0ef74c1d959eb51dac07018d6e77
ms.sourcegitcommit: a4f77aae75a317f5277d7d2a3187516cae1e3e19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "64517845"
---
# <a name="tier-model-for-partitioning-administrative-privileges"></a>A rendszergazdai jogosultságok felosztásának rétegmodellje

Ebben a cikkben egy olyan biztonsági modellel ismerkedhet meg, amely a jogok kiterjesztéséből adódó támadásokkal szembeni védelmet a magas szintű jogosultságokat igénylő tevékenységek és a nagy kockázatú zónák elkülönítésével biztosítja. Ez a modell úgy tartja fenn a jó felhasználói élményt, hogy közben alkalmazza az ajánlott eljárásokat és biztonsági alapelveket.

## <a name="elevation-of-privilege-in-active-directory-forests"></a>A jogok kiterjesztése az Active Directory-erdőkben

A Windows Server Active Directory- (AD-) erdőkhöz állandó rendszergazdai jogosultságokkal rendelkező felhasználók, szolgáltatások és alkalmazások fiókjai komoly kockázatot jelentenek a szervezet céljaira és üzleti tevékenységeire nézve. Ezeket a fiókokat gyakran a támadók célozzák meg, mert ha vannak ilyenek, a támadó jogosult a tartományban lévő más kiszolgálókhoz vagy alkalmazásokhoz való kapcsolódásra.

A rétegmodell annak alapján választja szét egymástól a rendszergazdákat, hogy milyen erőforrásokat kezelnek. A felhasználói munkaállomások felett irányítással rendelkező rendszergazdák elkülönülnek azoktól, akik alkalmazásokat vagy vállalati identitásokat kezelnek. A modellel kapcsolatos további tudnivalók a [Securing privileged access reference material](http://aka.ms/tiermodel) (A rendszerjogosultságú hozzáférés biztonságossá tételének referenciaanyagai) című témakörben olvashatók.

## <a name="restricting-credential-exposure-with-logon-restrictions"></a>Hitelesítő adatok veszélyeztetettségének korlátozása bejelentkezési korlátozásokkal

A rendszergazdai fiókokhoz tartozó hitelesítő adatok ellopásának kockázata általában a rendszergazdák munkamódszereinek átalakításával csökkenthető, ezáltal korlátozható a támadóknak való kiszolgáltatottság. Első lépésként az alábbi intézkedéseket javasoljuk a szervezeteknek:

- Korlátozzák azoknak a gépeknek a számát, amelyeken elérhetők a rendszergazdai hitelesítő adatok.
- Korlátozzák a szerepkörök jogosultságait a lehető legkevesebbre.
- Semmiképpen ne végezzenek rendszergazdai feladatokat olyan gazdagépeken, amelyeket hagyományos felhasználói műveletekre (például levelezésre vagy böngészésre) használnak.

A következő lépés a bejelentkezési korlátozások megvalósítása, illetve a rétegmodell követelményeit kielégítő folyamatok és műveletek feltételeinek megteremtése. Ideális esetben a hitelesítő adatokhoz való hozzáférést is le kell csökkenteni az egyes rétegeken belül az adott szerepkörhöz szükséges legalacsonyabb jogosultsági szintre.

Bejelentkezési korlátozásokat kell érvényesíteni annak érdekében, hogy a kiemelt jogosultságokkal rendelkező fiókok ne férjenek hozzá a kevésbé biztonságos erőforrásokhoz. Példa:

- A tartományi rendszergazdák (0. szint) nem tudnak bejelentkezni a vállalati kiszolgálókra (1. szint) és a normál felhasználói munkaállomásokra (2. szint).
- A kiszolgálói rendszergazdák (1. szint) nem tudnak bejelentkezni a normál felhasználói munkaállomásokra (2. szint).

>[!NOTE]
> A kiszolgálói rendszergazdák ne tartozzanak a tartományi rendszergazdák csoportjához. A tartományvezérlőkért és a vállalati kiszolgálókért felelő személyzetnek külön fiókokat kell biztosítani.

A bejelentkezési korlátozásokat a következőkkel lehet érvényesíteni:

- Csoportházirendek bejelentkezési jogmegadásának korlátozása, beleértve:
    - A számítógép hálózati elérésének megtagadása
    - Kötegelt feladatként való bejelentkezés megtagadása
    - Szolgáltatásként való bejelentkezés megtagadása
    - Helyi bejelentkezés megtagadása
    - Távoli asztali beállításokkal való bejelentkezés megtagadása  
- Hitelesítési házirendek és silók alkalmazása Windows Server 2012 vagy újabb rendszer használata esetén
- Szelektív hitelesítés használata, ha a fiók dedikált rendszergazdai erdőben található

## <a name="next-steps"></a>További lépések

- A következő, a [Megerősített környezet tervezése](planning-bastion-environment.md) című cikkben a Microsoft Identity Manager rendszergazdai fiókjainak létrehozásához szükséges dedikált felügyeleti erdőinek létrehozásáról olvashat.
- A [jogosultsági szinttel rendelkező hozzáférési munkaállomások](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) dedikált operációs rendszert biztosítanak az internetes támadásokkal és a fenyegetésekkel kapcsolatos vektorok által védett bizalmas feladatokhoz.
