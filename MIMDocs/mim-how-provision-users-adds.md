---
title: Microsoft Identity Manager 2016 felhasználó üzembe helyezése az AD-ben | Microsoft Docs
description: A felhasználók az AD DS-ben, a Microsoft Identity Manager 2016 használatával való létrehozási folyamatának áttekintése
keywords: ''
author: billmath
ms.author: billmath
manager: mtillman
ms.date: 08/18/2017
ms.topic: article
ms.prod: microsoft-identity-manager
ms.assetid: ''
ms.openlocfilehash: 77f1eb5d8355472c7aee7bc9f389ca8b24ab76a9
ms.sourcegitcommit: 1ca298d61f6020623f1936f86346b47ec5105d44
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76256614"
---
# <a name="how-do-i-provision-users-to-ad-ds"></a>Felhasználók kiépítése az AD DS-ben<!--TEST-->

A következőre vonatkozik: Microsoft Identity Manager 2016 SP1 (MIM)

Az egyik alapvető követelmény az identitáskezelési rendszerekkel szemben, hogy az erőforrások kiépíthetők legyenek valamely külső rendszerre.

Ez az útmutató a felhasználók a Microsoft® Identity Manager (MIM) 2016 szolgáltatásról az Active Directory® Domain Services (AD DS) szolgáltatásba való kiépítési folyamatának főbb építőelemeiről nyújt áttekintést, és ismerteti, hogyan ellenőrizhető, hogy a forgatókönyv az elvárásoknak megfelelően működik-e. Az útmutató továbbá javaslatokkal szolgál az Active Directory-felhasználók a MIM 2016 használatával történő kezelésével kapcsolatban, valamint további információforrásokat is tartalmaz.

## <a name="before-you-begin"></a>Előkészületek


Ez a szakasz a dokumentum hatókörével kapcsolatos információkat tartalmaz. A gyakorlati útmutatók általában olyan olvasóknak szólnak, akik már rendelkeznek alapvető ismertekkel az objektumok a MIM szolgáltatással való, [Az első lépéseket ismertető útmutatókban](https://go.microsoft.com/FWLink/p/?LinkId=190486) foglaltak szerinti szinkronizálásáról.

### <a name="audience"></a>Közönség


Jelen útmutató olyan informatikai (IT) szakembereknek szól, akik már rendelkeznek alapvető ismeretekkel a MIM szinkronizálási folyamatának működéséről, és akik gyakorlati tapasztalatokra szeretnének szert tenni, valamint a konkrét forgatókönyvekkel kapcsolatos további fogalmi információkhoz szeretnének jutni.

### <a name="prerequisite-knowledge"></a>Ismeretekre vonatkozó előfeltételek


A dokumentumban feltételezzük, hogy rendelkezik a MIM egy futó példányával, és hogy rendelkezik tapasztalattal az alábbi dokumentumokban felvázolt egyszerű szinkronizálási forgatókönyvek konfigurálásában:

-   [Bevezetés a bejövő szinkronizálásba](https://go.microsoft.com/FWLink/p/?LinkId=189652)

-   [Bevezetés a kimenő szinkronizálásba](https://go.microsoft.com/FWLink/p/?LinkId=189653)

A dokumentumban ismertetett tartalom a bevezető dokumentumok kiegészítésére szolgál.

### <a name="scope"></a>Hatókör


A dokumentumban ismertetett forgatókönyvet egyszerűsítettük annak érdekében, hogy megfeleljen az egyszerű tesztkörnyezetek követelményeinek. A fő cél az, hogy ismertessük a tárgyalt fogalmakat és technológiákat.

A dokumentum segít egy olyan megoldás kidolgozásában, amely magában foglalja a csoportok MIM segítségével, az AD DS szolgáltatásban való kezelését.

### <a name="time-requirements"></a>Időigény


A dokumentumban szereplő eljárások végrehajtása mintegy 90-120 percet igényel.

Az időtartamra vonatkozó becslés feltételezi, hogy a tesztkörnyezet konfigurálására már sor került, így nem tartalmazza a tesztkörnyezet beállításához szükséges időt.

### <a name="getting-support"></a>Támogatás igénybevétele


Ha kérdése merülne fel a dokumentum tartalmával kapcsolatban, vagy valamilyen általános visszajelzést szeretne megtárgyalni, bármikor közzétehet üzenetet a [Forefront Identity Manager 2010 fórumán](https://go.microsoft.com/FWLink/p/?LinkId=189654).

## <a name="scenario-description"></a>Forgatókönyv leírása


A Fabrikam, egy fiktív cég azt tervezi, hogy a MIM használatával fogja kezelni a felhasználói fiókokat a cég AD DS szolgáltatásában. A folyamat részeként a Fabrikam cégnek felhasználókat kell kiépítenie az AD DS szolgáltatásban. A kezdeti tesztelés elindításához a Fabrikam egy egyszerű, a MIM és az AD DS szolgáltatásból álló tesztkörnyezet telepítését végezte el.
A Fabrikam egy olyan forgatókönyvet tesztel ebben a tesztkörnyezetben, amelyben egyetlen, a MIM-portálon manuálisan létrehozott felhasználó található. A forgatókönyv célja, hogy a felhasználót olyan engedélyezett felhasználóvá építse ki, aki előre meghatározott jelszóval rendelkezik az AD DS szolgáltatáshoz.

## <a name="scenario-design"></a>A forgatókönyv felépítése


Az útmutató használatához három architekturális összetevőre van szükség:

-   Active Directory-tartományvezérlő

-   A FIM szinkronizálási szolgáltatást futtató számítógép
-   A FIM-portált futtató számítógép

A következő ábra a szükséges környezetet vázolja fel.

![Szükséges környezet](media/how-provision-users-adds/image001.png)


Az összes összetevőt futtathatja egyetlen számítógépen is.

> [!NOTE]
> A MIM beállításáról további információt a [FIM telepítési útmutatójában](https://go.microsoft.com/FWLink/p/?LinkId=165845) találhat.

## <a name="scenario-components-list"></a>A forgatókönyv összetevőinek listája


A következő táblázatban az útmutatóban ismertetett forgatókönyv részét képező összetevők szerepelnek.

| ![Szervezeti egység](media/how-provision-users-adds/image005.jpg)   | Szervezeti egység                | MIM-objektumok – A kiépített felhasználók számára célként használt szervezeti egység (OU).                                                       |
|----------------------------------------|------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| ![Felhasználói fiókok](media/how-provision-users-adds/image006.jpg)   | Felhasználói fiókok                      | &#183; **ADMA** – Az AD DS-hez való kapcsolódáshoz megfelelő jogosultságokkal rendelkező Active Directory-beli felhasználói fiók.<br/> &#183; **FIMMA** – A MIM szolgáltatáshoz való kapcsolódáshoz megfelelő jogosultságokkal rendelkező Active Directory-beli felhasználói fiók.
                                                                 |
| ![Kezelőügynökök és futtatási profilok](media/how-provision-users-adds/image007.jpg)  | Kezelőügynökök és futtatási profilok | & #183; **Fabrikam ADMA** – Az AD DS-sel adatokat cserélő kezelőügynök. <br/> &#183; Fabrikam FIMMA – A MIM szolgáltatással adatokat cserélő kezelőügynök.                                                                                 |
| ![Szinkronizálási szabályok](media/how-provision-users-adds/image008.jpg)  | Szinkronizálási szabályok              | A Fabrikam csoport kimenő szinkronizálási szabálya – Az AD DS-be felhasználókat kiépítő kimenő szinkronizálási szabály.                                     |
| ![Készletek](media/how-provision-users-adds/image009.jpg)   | Készletek                               | Összes alvállalkozó – Dinamikus tagsággal rendelkező készlet, mely minden Alvállalkozó értékű EmployeeType attribútummal rendelkező objektumot tartalmaz.                                |
| ![Munkafolyamatok](media/how-provision-users-adds/image010.jpg)  | Munkafolyamatok                          | AD-kiépítési munkafolyamat – A MIM-felhasználót az AD kimenő szinkronizálási szabályának hatókörébe vonó munkafolyamat.                                |
| ![Felügyeletiházirend-szabályok](media/how-provision-users-adds/image011.jpg)   | Felügyeletiházirend-szabályok            | Az AD-kiépítés felügyeletiházirend-szabálya – Egy felügyeletiházirend-szabály (MPR), amely akkor lép érvénybe, ha az adott erőforrás az Összes alvállalkozó készlet tagjává válik. |
| ![MIM-felhasználók](media/how-provision-users-adds/image012.jpg) | MIM-felhasználók                          | Britta Simon – Az AD DS-ben kiépítendő MIM-felhasználó.                                                                                             |



## <a name="scenario-steps"></a>A forgatókönyv lépései


Az útmutatóban ismertetett forgatókönyv az alábbi ábrán bemutatott építőelemekből áll.

![A forgatókönyv lépései](media/how-provision-users-adds/image013.png)


## <a name="configuring-the-external-systems"></a>A külső rendszerek konfigurálása


Ebben a szakaszban azokra a MIM-környezeten kívüli erőforrásokra vonatkozó utasításokat találja, melyeket létre kell hozni.

### <a name="step-1-create-the-ou"></a>1\. lépés: A szervezeti egység létrehozása


A szervezeti egységre a kiépített mintafelhasználó tárolójaként van szükség. A szervezeti egységek létrehozásáról további információt a [Create a New Organizational Unit](https://go.microsoft.com/FWLink/p/?LinkId=189655) (Új szervezeti egység létrehozása) című témakörben találhat.

Hozzon létre egy szervezeti egységet az AD DS-ben MIMObjects néven.

### <a name="step-2-create-the-active-directory-user-accounts"></a>2\. lépés: Az Active Directory-beli felhasználói fiókok létrehozása

Az útmutatóban ismertetett forgatókönyv esetében két Active Directory-beli felhasználói fiókra van szükség:

- **ADMA** – Az Active Directory-kezelőügynök használja.

- **FIMMA** – A FIM szolgáltatás kezelőügynöke használja.

Mindkét esetben elegendő normál felhasználói fiókot létrehozni. A két fiókra vonatkozó konkrét követelményekről további információt a dokumentum későbbi részében talál. A felhasználók létrehozásáról további információt a [Create a New User Account](https://go.microsoft.com/FWLink/p/?LinkId=189656) (Új felhasználói fiók létrehozása) című témakörben talál.


## <a name="configuring-the-fim-synchronization-service"></a>A FIM szinkronizálási szolgáltatás konfigurálása


A szakaszban ismertetett konfigurációs lépésekhez el kell indítania a FIM Synchronization Service Managert.

### <a name="creating-the-management-agents"></a>A kezelőügynökök létrehozása

Az útmutatóban ismertetett forgatókönyvhöz két kezelőügynököt kell létrehoznia:

-   **Fabrikam ADMA** – Az AD DS kezelőügynöke.

-   **Fabrikam FIMMA** –  A FIM szolgáltatás kezelőügynöke.

### <a name="step-3-create-the-fabrikam-adma-management-agent"></a>3\. lépés: A Fabrikam ADMA kezelőügynök létrehozása

Kezelőügynökök az AD DS-hez való konfigurálásakor azt a fiókot kell megadnia, amelyet a kezelőügynök az AD DS-sel való adatcsere során használ majd. Ehhez normál felhasználói fiókot célszerű használni. Ha azonban adatokat szeretne importálni az AD DS-ről, a fióknak rendelkeznie kell jogosultsággal ahhoz, hogy módosításokat kérhessen le a DirSync vezérlőből. Ha azt szeretné, hogy a kezelőügynök adatokat exportáljon az AD DS-be, a fiók számára elegendő jogosultságot kell biztosítania a célként megadott szervezeti egységben. A témakörről további tudnivalókat a [Configuring the ADMA Account](https://go.microsoft.com/FWLink/p/?LinkId=189657) (Az ADMA-fiók konfigurálása) című témakörben találhat.

Ha felhasználót szeretne létrehozni az AD DS-ben, továbbítania kell az objektum megkülönböztető nevét. Továbbá célszerű továbbítani a kereszt- és az utónevet, illetve a megjelenített nevet is annak biztosítása érdekében, hogy az objektumok észlelhetőek legyenek.

Az AD DS-ben továbbra is gyakori, hogy a felhasználók a sAMAccountName attribútumot használva jelentkeznek be a címtárszolgáltatásba. Ha nem ad meg értéket ehhez az attribútumhoz, a címtárszolgáltatás véletlenszerű értéket állít be hozzá. Ezek a véletlenszerű értékek azonban nem felhasználóbarátok, és emiatt képezi általában az AD DS-be való exportálás részét az attribútum felhasználóbarát változata. Ha engedélyezni szeretné a felhasználók számára az AD DS-be való bejelentkezést, szükség lesz az unicodePwd attribútum használatával, az exportlogikában létrehozott jelszóra is.

> [!Note]
> Győződjön meg arról, hogy az unicodePwd attribútumként megadott érték megfelel a célként megadott AD DS jelszóházirendjeinek.

Amikor jelszót állít be AD DS-fiókok számára, létre kell hoznia egy engedélyezett fiókként beállított fiókot is. Ezt a userAccountControl attribútum beállításával végezheti el. A userAccountControl attribútumról további információt a [Using FIM to Enable or Disable Accounts in Active Directory](https://go.microsoft.com/FWLink/p/?LinkId=189658) (A FIM használata a fiókok az Active Directoryban való engedélyezéséhez vagy letiltásához) című témakörben találhat.

Az alábbi táblázatban azok a forgatókönyvre jellemző legfontosabb beállítások szerepelnek, amelyek konfigurálását el kell végeznie.

| A kezelőügynök tervezőoldala                          | Configuration                                                  |
|---------------------------------------------------------|----------------------------------------------------------------|
| Kezelőügynök létrehozása                                 | 1. **felügyeleti ügynök a következőhöz:** AD DS  <br/> 2. **név:** Fabrikam ADMA |
| Kapcsolódás az Active Directory-erdőhöz                      | 1. **válassza ki** a címtárpartíciók: "DC = FABRIKAM, DC = com"   <br/>   2. kattintson a **tárolók** elemre a **tárolók kiválasztása** párbeszédpanel megnyitásához, és győződjön meg arról, hogy a **MIMObjects** az egyetlen kiválasztott szervezeti egység.        |
| Objektumtípusok kiválasztása                                     | A már kijelölt objektumtípusok mellett jelölje ki a **user** (felhasználó) elemet. |
| Attribútumok kiválasztása                                       | 1. kattintson **az összes megjelenítése** elemre. <br/>   2. Válassza ki a következő attribútumokat: <br/> &nbsp;&nbsp;&nbsp;&#176; **displayName** <br/> &nbsp;&nbsp;&nbsp;&#176; **givenName** <br/> &nbsp;&nbsp;&nbsp;&#176;  **sn** <br/> &nbsp;&nbsp;&nbsp;&#176;  **SamAccountName** <br/> &nbsp;&nbsp;&nbsp;&#176;  **unicodePwd** <br/> &nbsp;&nbsp;&nbsp;&#176;  **userAccountControl**     

További információ a Súgó következő témaköreiben olvasható:
- Create a Management Agent (Kezelőügynök létrehozása)
- Connect to an Active Directory Forest (Kapcsolódás az Active Directory-erdőhöz)
- Using the Management Agent for Active Directory (Az Active Directoryhoz készült kezelőügynök használata)
- Könyvtárpartíciók konfigurálása

> [!Note]
> Győződjön meg arról, hogy rendelkezik az ExpectedRulesList attribútumhoz konfigurált importálási attribútumfolyam-szabállyal.

### <a name="step-4-create-the-fabrikam-fimma-management-agent"></a>4\. lépés: A Fabrikam FIMMA kezelőügynök létrehozása

A FIM szolgáltatás kezelőügynökének konfigurálásákor azt a fiókot kell megadnia, amelyet a kezelőügynök a FIM szolgáltatással való adatcsere során használ majd.

Ehhez normál felhasználói fiókot célszerű használni. A fióknak azonosnak kell lennie azzal a fiókkal, amelyet a MIM telepítése során adott meg. A beállítás során megadott FIMMA-fiók nevének meghatározásához használt szkriptről, illetve annak ellenőrzéséről, hogy a fiók még érvényes-e, további információt a Using Windows PowerShell to Do a [FIM MA Account Configuration Quick Test](https://go.microsoft.com/FWLink/p/?LinkId=189659) (A Windows PowerShell használata a FIM MA-fiók konfigurációs gyorstesztjének elvégzéséhez) című témakörben találhat.

Az alábbi táblázatban azok a forgatókönyvre jellemző legfontosabb beállítások szerepelnek, amelyek konfigurálását el kell végeznie. Hozza létre a kezelőügynököt az alábbi táblázatban szereplő információk alapján.  

| A kezelőügynök tervezőoldala | Configuration |
|------------|------------------------------------|
| Kezelőügynök létrehozása | 1. **felügyeleti ügynök:** FIM Service Management agent <br/> 2. **név** : fabrikam FIMMA |
| Kapcsolódás az adatbázishoz     | Használja a következő beállításokat: <br/> &#183; **Server** (Kiszolgáló): localhost <br/> &#183; **Database** (Adatbázis): FIMService <br/> &#183;**FIM szolgáltatás alapcíme:** http://localhost:5725 <br/> <br/> A kezelőügynökhöz létrehozott fiók adatainak megadása |
| Objektumtípusok kiválasztása                                     | A már kijelölt objektumtípusok mellett jelölje ki a **Person** (Személy) elemet.   |
| Objektumtípus-leképezések konfigurálása                          | A már meglévő objektumtípus-leképezések mellett adja hozzá Person (Személy) **Data Source Object Type** (Adatforrás objektumtípusa) a **Metaverse** (Metaverzum) Objektumtípusú személyekre való leképezését. |
| Attribútumfolyam konfigurálása                                | A már meglévő attribútumfolyam-leképezések mellett adja hozzá a következő attribútumfolyam-leképezéseket is: <br/><br/> ![Attribútumfolyam](media/how-provision-users-adds/image018.jpg) |




További információt a súgó következő témaköreiben talál:
-   Create a Management Agent (Kezelőügynök létrehozása)

-   Connect to an Active Directory Database (Kapcsolódás az Active Directory-adatbázishoz)

-   Using the Management Agent for Active Directory (Az Active Directoryhoz készült kezelőügynök használata)

-   Könyvtárpartíciók konfigurálása

> [!NOTE]
>  Győződjön meg arról, hogy rendelkezik az ExpectedRulesList attribútumhoz konfigurált importálási attribútumfolyam-szabállyal.

### <a name="step-5-create-the-run-profiles"></a>5\. lépés: Futtatási profilok létrehozása

A következő táblázatban azok a futtatási profilok szerepelnek, amelyeket az útmutatóban ismertetett forgatókönyvhöz létre kell hoznia.

| Kezelőügynök  | Futtatási profil     |
|-------------------|--------------------------------------|
| Fabrikam ADMA     | 1. teljes importálás  <br/> 2. teljes szinkronizálás <br/> 3. különbözeti importálás <br/> 4. különbözeti szinkronizálás <br/> 5. exportálás                                                                    |
| Fabrikam FIMMA   | 1. teljes importálás <br> 2. teljes szinkronizálás <br/> 3. különbözeti importálás <br/> 4. különbözeti szinkronizálás <br/> 5. exportálás|                                                                                                                                                                                   

Hozza létre az egyes kezelőügynökökhöz tartozó futtatási profilokat a fenti táblázat szerint.


> [!Note]
> További információt a MIM súgójának Create a Management Agent Run Profile (Kezelőügynök futtatási profiljának létrehozása) című témakörében találhat.                                                                                                                  
> 
> 
> [!Important]
>  Ellenőrizze, hogy a kiépítés engedélyezett-e a környezetben. Ezt a szkript futtatásával végezheti el a Windows PowerShell használatával a kiépítés engedélyezéséhez (https://go.microsoft.com/FWLink/p/?LinkId=189660).


## <a name="configuring-the-fim-service"></a>A FIM szolgáltatás konfigurálása


Az útmutatóban felvázolt forgatókönyvhöz az alábbi ábrán látható módon kell kiépítési szabályzatot konfigurálnia.

![Telepítési házirend](media/how-provision-users-adds/image019.png)

A kiépítési szabályzat célja, hogy csoportokat vonjon be az AD-felhasználó kimenő szinkronizálási szabályának hatókörébe. Azáltal, hogy bevonja az erőforrást a szinkronizálási szabály hatókörébe, engedélyezi a szinkronizáló vezérlő számára, hogy a konfigurációjának megfelelően építse ki az erőforrást az AD DS-ben.

A FIM szolgáltatás konfigurálásához navigáljon a Windows Internet Explorer® http://localhost/identitymanagement. A kiépítési szabályzat létrehozásához a MIM-portál oldalán lépjen az Adminisztráció terület kapcsolódó oldalaira. A konfiguráció ellenőrzéséhez futtassa a [Using Windows PowerShell to document your provisioning policy configuration](https://go.microsoft.com/FWLink/p/?LinkId=189661) (A Windows PowerShell használata a kiépítési szabályzat konfigurálásának dokumentálására) című oldalon található szkriptet.

### <a name="step-6-create-the-synchronization-rule"></a>6\. lépés: A szinkronizálási szabály létrehozása

Az alábbi táblázatokban a Fabrikam számára szükséges kiépítési szinkronizálási szabály konfigurációja látható. Az alábbi táblázatokban szereplő adatoknak megfelelően hozza létre a szinkronizálási szabályt.

| A szinkronizálási szabály konfigurációja                                                                         |                                                                             |                                                           
|------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|-----------------------------------------------------------|
| Név                                                                                                       | Az Active Directory-felhasználóra vonatkozó kimenő szinkronizálási szabály                         |                                                          
| Description                                                                                               |                                                                             |                                                           
| Prioritás                                                                                                | 2                                                                           |                                                           
| Adatfolyam iránya   | Kimenő             |       
| Függőség       |         |                                         


| Hatókör |                                                                             |                                                           
|--------|-------|
| Metaverzum erőforrástípusa | személy |                                                         
| Külső rendszer                   |Fabrikam ADMA                                                               |                                                       
| Külső rendszer erőforrástípusa                                                                              | felhasználó      



| Relationship ||
|------------|---------|
| Erőforrás létrehozása külső rendszerben                                                                         | Igaz                                                                        |                                                           
| Megszüntetés engedélyezése                                                                                      | Hamis                                                                       |                                                           

| Kapcsolati feltételek                                                                                      | |
|------------|----------|
| ILM-attribútum     | Adatforrás-attribútum                                                       |
| Adatforrás-attribútum         | sAMAccountName    |

| Kezdeti kimenő attribútumfolyamok        | |                                                             |
|-------------------|---------------------- |---------------|
| Null értékek engedélyezése                 | Cél                                                                 | Forrás                                                    |
| hamis                       | megkülönböztető név                                                                          | \+(„CN=”,displayName,„,OU=MIMObjects,DC=fabrikam,DC=com”) |
| hamis                       | userAccountControl                                                          | **Constant** (Állandó): 512                                         |
| hamis                                                                     | unicodePwd                    | Constant (Állandó): P\@\$\$W0rd                                    |

| Állandó kimenő attribútumfolyamok  |                                                                     |                                                           |
|--------------------------------------|---------------------------------------------------------------------|-----------------------------------------------------------|
| Null értékek engedélyezése                                                                                                | Cél                                                                 | Forrás                                                    |
| hamis                                                                                                      | sAMAccountName                                                              | accountName                                               |
| hamis                                                                                                      | displayName                                                                 | displayName                                               |
| hamis                                                                                                      | givenName                                                                   | firstName                                                 |
| hamis                                                                                                      | sn                                                                          | lastName                                                  |



> [!NOTE]
>  Fontos Győződjön meg arról, hogy bejelölte az Initial Flow Only (Csak kezdeti folyam) lehetőséget az attribútumfolyamnál, melynek céljaként a megkülönböztető név van megadva.                                                                          

### <a name="step-7-create-the-workflow"></a>7\. lépés: A munkafolyamat létrehozása

Az AD-kiépítési munkafolyamat célja a Fabrikam kiépítési szinkronizálási szabályának erőforráshoz való hozzáadása. A konfiguráció az alábbi táblázatokban látható.  A munkafolyamatot az alábbi táblázatban szereplő adatoknak megfelelően hozza létre.

| Munkafolyamat-konfiguráció               |                                                                 |
|--------------------------------------|-----------------------------------------------------------------|
| Név                                 | Az Active Directory-felhasználó kiépítési munkafolyamata                     |
| Description                          |                                                                 |
| Munkafolyamat típusa                        | Művelet                                                          |
| Futtatás szabályzatfrissítéskor                 | Hamis                                                           |

| Szinkronizálási szabály                 |                                                                 |
|--------------------------------------|-----------------------------------------------------------------|
| Név                                 | Az Active Directory-felhasználóra vonatkozó kimenő szinkronizálási szabály             |
| Művelet                               | Hozzáadás                                                             |




### <a name="step-8-create-the-mpr"></a>8\. lépés: Az MPR létrehozása

A szükséges MPR Set Transition (Készletváltás) típusú, és akkor aktiválódik, ha egy adott erőforrás az Összes alvállalkozó készlet tagjává válik. A konfiguráció az alábbi táblázatokban látható.  Az MPR-t az alábbi táblázatokban szereplő adatoknak megfelelően hozza létre.

| MPR-konfiguráció                    |                                                             |
|--------------------------------------|-------------------------------------------------------------|
| Név                                 | AD-felhasználó kiépítésére vonatkozó felügyeletiházirend-szabály                 |
| Description                          |                                                             |
| Típus                                 | Set Transition (Készletváltás)                                              |
| Engedélyeket biztosít                   | Hamis                                                       |
| Letiltva                             | Hamis                                                       |

| A váltás meghatározása                |                                                             |
|--------------------------------------|-------------------------------------------------------------|
| Váltás típusa                      | Váltás itt:                                               |
| Váltási készlet                       | Összes alvállalkozó                                             |

| Szabályzat-munkafolyamatok                     |                                                             |
|--------------------------------------|-------------------------------------------------------------|
| Típus                                 | Művelet                                                      |
| Megjelenített név                         | Az Active Directory-felhasználó kiépítési munkafolyamata                 |




## <a name="initializing-your-environment"></a>A környezet inicializálása


Az inicializálási fázis céljai a következők:

-   A szinkronizálási szabály beemelése a metaverzumba.

-   Az Active Directory-struktúra beemelése az Active Directory-összekötőtérbe.

### <a name="step-9-run-the-run-profiles"></a>9\. lépés: A futtatási profilok futtatása

A következő táblázatban az inicializálási fázis részét képező futtatási profilok szerepelnek.  Futtassa a futtatási profilokat az alábbi táblázatban leírtak szerint.

| Futtatás                                                                                                           | Kezelőügynök                                      | Futtatási profil          |
|---------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|----------------------|
| 1                                                                                                             | Fabrikam FIMMA                                        | Teljes importálás          |
| 2                                                                                                             |                                                       | Teljes szinkronizálás |
| 3                                                                                                             |                                                       | Exportálás               |
| 4                                                                                                             |                                                       | Különbözeti importálás         |
|                                                                                                               |                                                       |                      |
| 5                                                                                                             | Fabrikam ADMA                                         | Teljes importálás          |
| 6                                                                                                             |                                                       | Teljes szinkronizálás |



> [!NOTE]
> Ellenőrizze, hogy sikeresen lezajlott-e a kimenő szinkronizálási szabály leképezése a metaverzumba.

## <a name="testing-the-configuration"></a>A konfiguráció tesztelése


Ezen szakasz célja a tényleges konfiguráció tesztelése. A konfiguráció teszteléséhez a következőket kell elvégeznie:

1.  Mintafelhasználó létrehozása a FIM-portálon.

2.  A mintafelhasználóra vonatkozó kiépítési követelmények ellenőrzése.

3.  A mintafelhasználó kiépítése az AD DS-ben.

4.  Annak ellenőrzése, hogy a felhasználó létezik az AD DS-ben.

### <a name="step-10-create-a-sample-user-in-mim"></a>10. lépés: Mintafelhasználó létrehozása a MIM szolgáltatásban


Az alábbi táblázatban a mintafelhasználó tulajdonságai szerepelnek. Hozzon létre egy minta felhasználót az alábbi táblázatban szereplő adatértékek szerint.

| Attribútum                              | Érték                                                          |
|----------------------------------------|----------------------------------------------------------------|
| Utónév                             | Britta                                                         |
| Vezetéknév                              | Simon                                                          |
| Megjelenített név                           | Britta Simon                                                   |
| Fióknév                           | BSimon                                                         |
| Tartomány                                 | Fabrikam                                                       |
| Alkalmazott típusa                          | Alvállalkozó                                                     |



### <a name="verify-the-provisioning-requisites-of-the-sample-user"></a>A mintafelhasználóra vonatkozó kiépítési követelmények ellenőrzése


A mintafelhasználó AD DS-ben való kiépítéséhez két előfeltételnek kell teljesülnie:

1.  A felhasználónak az Összes alvállalkozó készlet tagjának kell lennie.

2.  A készletbeli felhasználónak a kimenő szinkronizálási szabály hatókörébe kell tartoznia.

### <a name="step-11-verify-the-user-is-a-member-of-all-contractors"></a>11. lépés: Annak ellenőrzése, hogy a felhasználó az Összes alvállalkozó készlet tagja-e

Annak ellenőrzéséhez, hogy a felhasználó az Összes alvállalkozó készlet tagja-e, nyissa meg a készletet, majd kattintson a View Members (Tagok megtekintése) elemre.

![Annak ellenőrzése, hogy a felhasználó az Összes alvállalkozó készlet tagja-e](media/how-provision-users-adds/image022.jpg)


### <a name="step-12-verify-the-user-is-in-the-scope-of-the-outbound-synchronization-rule"></a>12. lépés: Annak ellenőrzése, hogy a felhasználó a kimenő szinkronizálási szabály hatókörében van-e

Annak ellenőrzéséhez, hogy a felhasználó a szinkronizálási szabály hatókörében van-e, nyissa meg a felhasználó tulajdonságlapját, és tekintse át a kiépítési lapon a várt szabályok listázása attribútumot. A várt szabályok listája attribútumnak fel kell sorolnia az AD-felhasználót

kimenő szinkronizálási szabálynak. Az alábbi képernyőfelvételen a várt szabályok listája attribútum látható.

![A szinkronizálási szabály állapota](media/how-provision-users-adds/image023.jpg)

A folyamat ezen pontján a szinkronizálási szabály függő állapotban van. Ez annyit jelent, hogy a szinkronizálási szabály még nem lett alkalmazva a felhasználóra.



### <a name="step-13-synchronize-the-sample-group"></a>13. lépés: A mintacsoport szinkronizálása


A tesztobjektum első szinkronizálási ciklusának elindítása előtt érdemes nyomon követni az objektum várható állapotát az adott tesztelési tervben futtatott egyes futtatási profilok után. A tesztelési tervben az objektum általános állapota (létrehozott, frissített vagy törölt) mellett szerepelnie kell a várható attribútumértékeknek is.
A tesztelési terv alapján ellenőrizze a tesztelési tervvel szembeni követelményeket. Ha egy adott lépés nem a várható eredményeket adja vissza, csak akkor folytassa a következő lépéssel, ha már feloldotta a várható és a tényleges eredmény közötti eltérést.

Az elvárások ellenőrzéséhez első indikátorként használhatja a szinkronizálási statisztikát. Ha például új objektumok előkészítése várható az adott összekötőtérben, ám az importálási statisztika nem ad vissza „Hozzáadásokat”, minden bizonnyal valami nem a várt módon működik a környezetben.

![Szinkronizálási statisztika](media/how-provision-users-adds/image024.jpg)

Bár a szinkronizálási statisztika elsőként adhat jelzést arról, hogy a forgatókönyv a várt módon működik-e, a várható attribútumértékek ellenőrzéséhez célszerű a Synchronization Service Manager Keresési összekötőterét és Metaverzumbeli keresési funkcióját használni.

A felhasználó AD DS-hez való szinkronizálásához végezze el a következőket:

1.  A felhasználó importálása a FIM MA-összekötőtérbe.

2.  A felhasználó leképezése a metaverzumba.

3.  A felhasználó kiépítése az Active Directory összekötőterében.

4.  Az állapotinformációk exportálása a FIM-be.

5.  A felhasználó exportálása az AD DS-be.

6.  A felhasználó létrehozásának megerősítése.

A fenti feladatok elvégzéséhez futtassa a következő futtatási profilokat.

| Kezelőügynök | Futtatási profil  |
|------------------|--------------|
| Fabrikam FIMMA   | 1. különbözeti importálás <br/> 2. különbözeti szinkronizálás <br/> 3. exportálás <br/> 4. különbözeti importálás |
| Fabrikam FIMMA   | 1. exportálás <br/> 2. különbözeti importálás       |


A FIM szolgáltatás adatbázisából való importálás után a Britta Simon és a ExpectedRuleEntry objektum, amely a Britta-t az AD-felhasználó kimenő szinkronizálási szabályához csatolja, a fabrikam FIMMA-összekötő területének megfelelően történik. Ha áttekinti a Britta tulajdonságait az összekötő területén, a FIM-portálon konfigurált attribútumérték mellett érvényes hivatkozást is talál a várt szabálykészlet objektumra. Az alábbi képernyőképen egy példa látható.

![Összekötőtér-objektum tulajdonságai](media/how-provision-users-adds/image025.jpg)

A Fabrikam FIMMA ügynökön futó különbözeti szinkronizálás célja, hogy többféle műveletet lehessen végrehajtani:

-   Leképezés – Az új felhasználói, valamint az ahhoz kapcsolódó várhatószabály-bejegyzési objektumot leképezi a rendszer a metaverzumba.

-   Kiépítés – Az újonnan leképezett Britta Simon objektumot kiépíti a rendszer a Fabrikam ADMA összekötőterében.

-   Exportálási attribútumfolyamok – Exportálási attribútumfolyamok mennek végbe mindkét kezelőügynökön. A Fabrikam ADMA feltölti az újonnan kiépített Britta Simon objektumot új attribútumértékekkel. A Fabrikam FIMMA a már létező Britta Simon objektumot és a kapcsolódó ExpectedRuleEntry objektumot frissíti a leképezés eredményeként kapott attribútumértékekkel.

![Szinkronizálási statisztika](media/how-provision-users-adds/image026.jpg)

Ahogy azt már a szinkronizálási statisztika is jelezte, a Fabrikam ADMA összekötőterében kiépítési tevékenység ment végbe. Amikor áttekinti Britta Simon metaverzumbeli objektumának tulajdonságait, azt fogja látni, hogy ezt a tevékenységet az érvényes hivatkozással feltöltött ExpectedRulesList attribútum eredményezte.

![a metaverzumbeli objektum tulajdonságai](media/how-provision-users-adds/image027.jpg)

A Fabrikam FIMMA által végrehajtott ezt követő exportálás során a Britta Simonra vonatkozó szinkronizálási szabályt a rendszer a Függő állapotról az Alkalmazott állapotra módosítja, ami azt jelzi, hogy a kimenő szinkronizálási szabály ettől kezdve aktív metaverzumban található objektumon.

![Alkalmazott szinkronizálási szabály](media/how-provision-users-adds/image028.jpg)

Mivel új objektum lett kiépítve az ADMA összekötőterében, ezen a kezelőügynökön egyetlen függő exportálás-hozzáadási elemnek kell lennie. 

![A kezelőügynök függőben lévő exportálásai](media/how-provision-users-adds/image029.jpg)

A FIM-ben az egyes exportálási futtatások őket követő különbözeti szinkronizálást igényelnek az exportálási művelet végrehajtásához. Az azt megelőző exportálás futtatása után futtatott különbözeti szinkronizálást jóváhagyási importálásnak nevezik. Jóváhagyási importálások szükségesek ahhoz, hogy a FIM szinkronizálási szolgáltatás megfelelő frissítési követelményeket tudjon létrehozni az egymást követő szinkronizálási futtatások során.


Az ebben a szakaszban ismertetett utasítások szerint futtassa a futtatási profilokat.

> [!IMPORTANT]
> Az egyes futtatásiprofil-futtatásoknak hiba nélkül kell befejeződniük.

### <a name="step-14-verify-the-provisioned-user-in-ad-ds"></a>14. lépés: A kiépített felhasználó ellenőrzése az AD DS-ben

A mintafelhasználó AD DS-ben való kiépítésének sikerességét a FIMObjects szervezeti egység megnyitásával ellenőrizheti. Britta Simonnak a FIMObjects szervezeti egységben kell lennie.

![annak ellenőrzése, hogy a felhasználó a FIMObjects szervezeti egységben található-e](media/how-provision-users-adds/image033.jpg)

<a name="summary"></a>Összefoglalás
=======

Jelen dokumentum célja, hogy megismertesse Önnel a felhasználók a MIM-ben, az AD DS segítségével való szinkronizálásához szükséges építőelemeket. A kezdeti tesztelés során először az adott feladat végrehajtásához szükséges minimális attribútumokkal kell kezdenie, majd további attribútumokat kell hozzáadnia a forgatókönyvhöz, amennyiben az általános lépések az várt módon működnek. A bonyolultság minimális szinten tartása egyszerűbbé teszi a hibák elhárításának folyamatát.

A konfiguráció tesztelésekor igen nagy a valószínűsége annak, hogy törölnie kell az új tesztobjektumokat, majd újra létre kell hoznia őket. Feltöltött ExpectedRulesList attribútummal

rendelkező objektumok esetében ez árva ERE-objektumokat eredményezhet.
Arról, hogyan távolíthatja el ezeket az objektumokat a tesztkörnyezetből, az [A Method to Remove Orphaned ExpectedRuleEntry Objects from Your Environment](https://go.microsoft.com/FWLink/p/?LinkId=189667) (Módszer az árva ExpectedRuleEntry objektumok környezetből való eltávolításához) című témakörben találhat további információt.

Az AD DS-t szinkronizálási célként tartalmazó tipikus szinkronizálási forgatókönyvekben a MIM nem mérvadó az objektumok összes attribútuma esetében. Ha például a FIM segítségével kezel felhasználói objektumokat az AD DS-ben, legalább a domain és az objectSID attribútumot az AD DS kezelőügynökének kell biztosítania.
Az adott felhasználó FIM-portálra való bejelentkezésének engedélyezéséhez a fióknévre, a tartományra és az objectSID attribútumra van szükség. Ha az AD DS-ből szeretné feltölteni ezeket az attribútumokat, egy további bejövő szinkronizálási szabály szükséges az AD DS-összekötőtérhez. Ha az attribútumértékek több forrásával kezel objektumokat, gondoskodni kell az attribútumfolyamok sorrendjének megfelelő konfigurálásáról. Ha az attribútumfolyamok sorrendje nem a megfelelő módon van konfigurálva, a szinkronizáló vezérlő letiltja az attribútumértékek feltöltését. Az attribútumfolyamok sorrendjéről további információt az [About Attribute Flow Precedence](https://go.microsoft.com/FWLink/p/?LinkId=189675) (Az attribútumfolyamok sorrendjének ismertetése) című témakörben találhat.

<a name="see-also"></a>Lásd még
=========

<a name="other-resources"></a>Egyéb források
---------------

[Using FIM to Enable or Disable Accounts in Active Directory](https://go.microsoft.com/FWLink/p/?LinkId=189670) (A FIM használata a fiókok az Active Directoryban való engedélyezéséhez vagy letiltásához)

[A hivatkozási attribútumok feldolgozásának ismertetése](https://go.microsoft.com/FWLink/p/?LinkId=189671)

[A FIM MA-fiók kezelése](https://go.microsoft.com/FWLink/p/?LinkId=189672)

[Nem mérvadó fiókok észlelése – 1. rész: tervezés](https://go.microsoft.com/FWLink/p/?LinkId=189673)

[Összekötők észlelése](https://go.microsoft.com/FWLink/p/?LinkId=189674)

[Az ADMA-fiók konfigurálása](https://go.microsoft.com/FWLink/p/?LinkId=189657)

[About Attribute Flow Precedence](https://go.microsoft.com/FWLink/p/?LinkId=189675) (Az attribútumfolyamok sorrendjének ismertetése)

[Az export ismertetése](https://social.technet.microsoft.com/wiki/contents/articles/1861.understanding-exports-in-ilm-2007.aspx)
