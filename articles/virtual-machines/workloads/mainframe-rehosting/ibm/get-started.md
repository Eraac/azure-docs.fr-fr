---
title: Charges de travail IBM dans Azure | Microsoft Docs
description: Utilisez un émulateur mainframe et d’autres services de partenaires Microsoft pour réhéberger vos charges de travail IBM z/OS en utilisant Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 4acf2fe1bc63061f17f90d6737f586408520dc13
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621319"
---
# <a name="ibm-workloads-on-azure"></a>Charges de travail IBM dans Azure

Nombreuses sont les charges de travail mainframe IBM basées sur z/OS à pouvoir être répliquées dans Azure sans aucune perte de fonctionnalité et sans même que les utilisateurs ne remarquent la moindre modification dans leurs systèmes sous-jacents. En réhébergeant des applications dans Azure, vous bénéficiez des fonctionnalités de type mainframe dont vous avez besoin ainsi que de l’élasticité, de la disponibilité et des économies potentielles du cloud.

Azure prend en charge l’intégration avec les environnements mainframe IBM existants, ce qui vous permet de migrer les applications nécessaires, d’exécuter éventuellement des solutions hybrides et de migrer au fil du temps. Même si vous avez la possibilité de réécrire entièrement les programmes mainframe existants pour Azure, la pratique la plus courante consiste à les réhéberger. La réécriture a pour effet d’accroître les coûts, la complexité et la durée des projets de migration. Avec le réhébergement, vous pouvez :

- Déplacer des applications dans un émulateur cloud.

- Migrer la base de données dans une base de données cloud.

- Remplacer des modules et du code à l’aide de moteurs de transformation de code.

Par ailleurs, les logiciels IBM, notamment WebSphere et MQ, se trouvent désormais dans la Place de marché Azure. Avec une licence pour logiciel IBM, vous pouvez bénéficier de la mise à l’échelle à la demande de l’infrastructure proposée par Azure pour démarrer rapidement une machine virtuelle.

Un écosystème de partenaires étendu se tient à votre disposition pour vous aider à migrer des systèmes mainframe IBM sur Azure. La plupart suivent une approche pragmatique qui consiste à réutiliser autant que possible l’existant avant de se lancer dans un déploiement progressif avec réécriture et remplacement d’applications. Obtenez des conseils et une aide supplémentaires auprès de partenaires dans le [centre de migration mainframe Azure](https://azure.microsoft.com/migration/mainframe/).

**Étapes suivantes**

- [Migration d’ordinateurs mainframe : mythes et réalités](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Installer l’environnement de développement/test IBM zD&T sur Azure](./install-ibm-z-environment.md)
- [Configurer une instance ADCD (Application Developers Controlled Distribution) dans IBM zD&T v1](./demo.md)
- [IBM DB2 pureScale sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
