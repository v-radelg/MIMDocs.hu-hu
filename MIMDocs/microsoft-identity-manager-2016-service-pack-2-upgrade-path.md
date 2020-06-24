---
title: Frissítés a FIM 2010 R2 és a (z) 2016 SP2 rendszerről Microsoft Identity Manager 2016 Service Pack 2 verzióra | Microsoft Docs
description: Megtudhatja, hogyan frissítheti a FIM 2010 R2 vagy a rendszer 2016 SP2 összetevőit, majd telepítheti a webszolgáltatási 2016-ban új összetevőket.
keywords: ''
author: EugeneSergeev
ms.author: esergeev
manager: aashiman
ms.date: 09/16/2019
ms.topic: article
ms.prod: microsoft-identity-manager
ms.assetid: 9471ccc1-bafe-46ee-b169-1464262380e1
ms.reviewer: markwahl-msft
ms.suite: ems
ms.openlocfilehash: bdf34be4841b1a911fdb61673e5a3855e66e7320
ms.sourcegitcommit: a4f77aae75a317f5277d7d2a3187516cae1e3e19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "73568054"
---
# <a name="mim-2016-sp2-upgrade--from-forefront-identity--or-microsoft-identity-manager"></a>A webhelyről 2016 SP2 frissítése a Forefront Identity vagy Microsoft Identity Manager<!--test-->

A szervezetek a Microsoft Identity Manager vagy a Forefront Identity Manager korábbi verzióiról Microsoft Identity Manager 2016 SP2-re frissíthetnek.  A cikk minden szakasza egy támogatott frissítési útvonalra terjed ki.

Több frissítési lehetőség is rendelkezésre áll. Ha már 2016 futtatta a SCSM-t, és nem kell frissítenie a mögöttes platformot (Windows Server, SQL, SharePoint, DW), vagy egy csoportosan felügyelt szolgáltatásfiókok használatával szeretné futtatni a SAML-szolgáltatásokat, és nem használja a felhasználói felület nyelvi csomagjait, akkor a legegyszerűbb lehetőség a helyben történő frissítés/gyorsjavítás (. msp) telepítése. Ellenkező esetben a teljes telepítés ajánlott.

> [!IMPORTANT]
> A következő témakörben található frissített [szoftverek előfeltételeinek](prepare-server-ws2016.md#software-prerequisites) ellenőrzése a webalkalmazás 2016 SP2 telepítése előtt

## <a name="upgrade-from-fim-2010-r2-sp1-or-later-fim-builds"></a>Frissítés FIM 2010 R2 SP1 vagy újabb FIM-buildről

> [!NOTE]
> A Forefront Identity Manager minimális támogatott verziója, amely közvetlenül a be2016 SP2-re frissíthető, FIM 2010 R2 SP1 (Build 4.1.3419.0). A rendszer nem támogatja a a FIM korábbi verzióiból származó, a 2016-es verzióra történő közvetlen frissítést. Ha a 4.1.3419.0-nél korábbi FIM-buildeket futtat, akkor a következőre kell frissítenie a FIM 2010 R2 SP1 verzióra, mielőtt a rendszer a következőre 2016 frissítené a következőt:-

1. **1. lehetőség: teljes telepítés meglévő adatbázisok használatával**
    1. Készítsen biztonsági másolatot a FIMSynchronizationService és a FIMService adatbázisairól.
    1. Exportálja a FIM Service összes RCDC objektumát és a RCDC által végrehajtott összes erőforrás-karakterláncot.
    1. A szinkronizálási szolgáltatás titkosítási kulcsainak exportálása.
    1. A Backup miisserver. exe. config, a szinkronizációs kiszolgáló bővítmények mappája, a Microsoft. ResourceManagement. Service. exe. config as címtárszolgáltatás-telepítő felülírhatja a konfigurációs fájlokban végrehajtott egyéni módosításokat.
    1. Távolítsa el **az összes** FIM-összetevőt, beleértve a nyelvi csomagokat is (először el kell távolítani).
    1. Nem *kötelező:* FIM-adatbázisok áthelyezése másik SQL Server-kiszolgálóra. Azt javasoljuk, hogy hozzon létre SQL-aliasokat a fakiszolgálón, és használja ezeket az aliasokat a valós SQL Server-név helyett, hogy a rendszer megkönnyítse a webkiszolgálói adatbázisok áttelepítését
    1. Telepítse a 2016 SP2-et az. ISO telepítési adathordozóról a (z) vagy egy másik kiszolgálón a következő, a rendelkezésre álló adatbázisokhoz tartozó [telepítési útmutató](microsoft-identity-manager-deploy.md)alapján, válassza a meglévő adatbázisok használata, amikor a rendszer kéri, adja meg a korábban exportált szinkronizációs szolgáltatás
    1. Tekintse át a miisserver. exe. config és a Microsoft. ResourceManagement. Service. exe. config fájlt a hiányzó .net-átirányításokhoz vagy a hozzáadott egyéni akták megtekintéséhez.
    1. Szükség esetén telepítse a 2016 SP2 nyelvi csomagokat.
    1. Testreszabások újbóli elküldése a RCDC-objektumokhoz, valamint szükség esetén RCDC az erőforrás-karakterláncok és a honosítások.
    1. Frissítse a FIM-bővítményeket és a jelszó-visszaállítási ügyfeleket, adja meg a rendszerállapot-szolgáltatás új kiszolgálójának nevét, ha a rendszer megváltoztatta a
    
## <a name="upgrade-from-previous-mim-2016-builds"></a>Frissítés az előző webszolgáltatási 2016-buildekről
1. Készítsen biztonsági másolatot a FIMSynchronizationService és a FIMService adatbázisairól.
1. Exportálja a RCDC objektumok és a RCDC erőforrás-karakterláncok összes egyéni FIM-szolgáltatásának honosítását.
1. A szinkronizálási szolgáltatás titkosítási kulcsainak exportálása.
1. A Backup miisserver. exe. config, a szinkronizációs kiszolgáló bővítmények mappája, a Microsoft. ResourceManagement. Service. exe. config as címtárszolgáltatás-telepítő felülírhatja a konfigurációs fájlokban végrehajtott egyéni módosításokat.
1. Ha használatban van, távolítsa el a rendszer nyelvi csomagjait
1. A címtárszolgáltatás-szolgáltatások leállítása.
1. **1. lehetőség: helyben történő frissítés – gyorsjavítás telepítése**
    1. A webalkalmazás-2016 SP2 szinkronizációs szolgáltatásának alkalmazása [gyorsjavítás](https://www.microsoft.com/download/details.aspx?id=100412)
    1. A webszolgáltatási csomag 2016 SP2 szervizcsomagjának alkalmazása [gyorsjavítás](https://www.microsoft.com/download/details.aspx?id=100412)
    1. Tekintse át a miisserver. exe. config és a Microsoft. ResourceManagement. Service. exe. config fájlt a hiányzó .net-átirányításokhoz vagy bármely olyan egyéni szakaszt, amelyet hozzá kell adni.
    1. Szükség esetén telepítse a 2016 SP2 nyelvi csomagokat.
    1. Testreszabások újbóli elküldése a RCDC-objektumokhoz, valamint szükség esetén RCDC az erőforrás-karakterláncok és a honosítások.
    1. A webkiszolgáló 2016 bővítmények és a jelszó-visszaállítási ügyfelek frissítése.
1. **2. lehetőség: teljes telepítés meglévő adatbázisok használatával**
    1. Távolítsa el **az összes** rendszerelem-összetevőt.
    1. Nem *kötelező:* FIM-adatbázisok áthelyezése másik SQL Server-kiszolgálóra. Azt javasoljuk, hogy hozzon létre SQL-aliasokat a fakiszolgálón, és használja ezeket az aliasokat a valós SQL Server-név helyett, hogy a rendszer megkönnyítse a webkiszolgálói adatbázisok áttelepítését
    1. Telepítse a 2016 SP2-et az. ISO telepítési adathordozóról a (z) vagy egy másik kiszolgálón a következő, a rendelkezésre álló adatbázisokhoz tartozó [telepítési útmutató](microsoft-identity-manager-deploy.md)alapján, válassza a meglévő adatbázisok használata, amikor a rendszer kéri, adja meg a korábban exportált szinkronizációs szolgáltatás
    1. Tekintse át a miisserver. exe. config és a Microsoft. ResourceManagement. Service. exe. config fájlt a hiányzó .net-átirányításokhoz vagy bármely olyan egyéni szakaszt, amelyet hozzá kell adni.
    1. Szükség esetén telepítse a 2016 SP2 nyelvi csomagokat.
    1. Testreszabások újbóli elküldése a RCDC-objektumokhoz, valamint szükség esetén RCDC az erőforrás-karakterláncok és a honosítások.
    1. A webszolgáltatások frissítése 2016 bővítmény-és jelszó-visszaállítási ügyfelek esetén adja meg az új rendszerállapot-szolgáltatási kiszolgáló nevét, ha a rendszer megváltoztatta a következőt:

> [!NOTE]
> A nyelvi csomagok frissítései a 2016 SP2 után gyorsjavításként (. msp fájlok) lesznek terjesztve, így nincs szükség a nyelvi csomagok eltávolítására/újratelepítésére.

A frissítéssel és az adatbázisokkal kapcsolatos biztonsági mentési eljárásokkal kapcsolatos részletesebb információkat a [frissítés a fim 2010 R2](https://docs.microsoft.com/previous-versions/mim/jj134291%28v%3dws.10%29) -re című cikkben talál, amely a FIM-vagy a rendszerállapot-frissítési folyamatokra is érvényes.
