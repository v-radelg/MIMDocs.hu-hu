---
title: '3\. lépés: Az SQL konfigurálása'
description: Ez a cikk a Privileged Identity Manager parancsfájlokkal történő konfigurálást ismertető sorozat 3. tagja, amely az SQL Server konfigurálásának lépéseit írja le.
keywords: ''
author: billmath
ms.author: billmath
manager: mtillman
ms.date: 08/18/2017
ms.topic: article
ms.prod: microsoft-identity-manager
ms.assetid: 4b524ae7-6610-40a0-8127-de5a08988a8a
ms.reviewer: ''
ms.suite: ems
ms.openlocfilehash: 69f86c5366f7b662f3a11fa4ac8d44159421f909
ms.sourcegitcommit: a4f77aae75a317f5277d7d2a3187516cae1e3e19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "64518154"
---
# <a name="step-3-configuring-sql"></a>3\. lépés: Az SQL konfigurálása

> [!div class="step-by-step"]
> [« 2. lépés](sp1-step2-configuring-corp-domain.md)
> [4. lépés »](sp1-step4-configuring-sharepoint.md)

Mielőtt folytatná a következő lépésekkel, győződjön meg róla, hogy az SQL Server 2012 SP1 vagy újabb verziót, illetve az SQL Server 2014 verziót használja. Tartományhoz csatlakoztatott kiszolgálók esetében jelentkezzen be a MIMAdmin-fiók használatával, más esetben jelentkezzen be helyi rendszergazdaként.
1. A PowerShell futtatása rendszergazdaként
2. cd $env:SYSTEMDRIVE\PAM
3. .\PAMDeployment.ps1
4. A 3. menüelem kiválasztása (SQL Server beállítása)

   Ha a kiszolgáló még nem lett csatlakoztatva egy PRIV-tartományhoz, kérni fogja a hitelesítő adatokat és a kiszolgáló csatlakoztatását a tartományhoz.
   A tartományhoz csatlakozás után a gép újraindul. Sikeres újraindítás után jelentkezzen be a kiszolgálón a MIMAdmin-fiókkal.

5. A PowerShell futtatása rendszergazdaként
6. cd $env:SYSTEMDRIVE\PAM
7. .\PAMDeployment.ps1
8. A 3. menüelem kiválasztása (SQL Server beállítása)

Ha szükséges, adja meg a MIMAdmin-szolgáltatásfiókhoz tartozó jelszót, és engedélyezze a telepítés folytatását. Ha elkészült, folytassa a 4. lépéssel.

> [!div class="step-by-step"]
> [« 2. lépés](sp1-step2-configuring-corp-domain.md)
> [4. lépés »](sp1-step4-configuring-sharepoint.md)
