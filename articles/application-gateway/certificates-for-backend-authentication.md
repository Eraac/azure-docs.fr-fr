---
title: Certificats nécessaires pour mettre des serveurs back-end en liste verte dans Azure Application Gateway
description: Cet article fournit des exemples sur la manière de convertir un certificat SSL en certificat d’authentification et en certificat racine approuvé, lesquels sont nécessaires pour mettre des instances back-end en liste verte dans Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831586"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Créer des certificats pour mettre un serveur back-end en liste verte dans Azure Application Gateway

Pour permettre l’utilisation du protocole SSL de bout en bout, Application Gateway impose la condition suivante : vous devez ajouter les instances back-end à la liste verte en chargeant les certificats d’authentification/certificats racine approuvés. Dans le cas de la référence SKU v1, les certificats d’authentification sont requis, tandis que dans le cas de la référence SKU v2, les certificats racines approuvés sont requis pour mettre en liste verte les certificats

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> - Exporter un certificat d’authentification à partir d’un certificat de serveur back-end (pour le SKU v1)
> - Exporter un certificat racine approuvé à partir d’un certificat de serveur back-end (pour le SKU v2)

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’un certificat de serveur back-end existant pour générer les certificats d’authentification ou les certificats racine approuvés nécessaires à la création de listes vertes d’instances back-end auprès d’Application Gateway. Le certificat de serveur back-end peut être identique au certificat SSL ou être différent pour une sécurité accrue. Application Gateway ne fournit aucun mécanisme permettant de créer ou d’acheter un certificat SSL. À des fins de test, vous pouvez créer un certificat auto-signé, mais vous ne devez pas l’utiliser pour les charges de travail de production. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exporter le certificat d’authentification (pour le SKU v1)

Un certificat d’authentification est nécessaire pour mettre les instances back-end en liste verte dans le SKU v1 d’Application Gateway. Le certificat d’authentification est la clé publique des certificats de serveur back-end au format X.509 avec codage base 64 (.CER). Dans cet exemple, nous allons utiliser un certificat SSL pour le certificat de serveur back-end et exporter sa clé publique afin qu’elle soit utilisée en tant que certification d’authentification. De plus, dans cet exemple, nous allons utiliser l’outil Windows Certificate Manager pour exporter les certificats nécessaires. Vous pouvez choisir d’utiliser un autre outil à votre convenance.

À partir de votre certificat SSL, exportez le fichier .cer de clé publique (et non la clé privée). Les étapes suivantes vous aident à exporter le fichier .cer au format X.509 avec codage base 64 (.CER) pour votre certificat :

1. Pour obtenir un fichier .cer du certificat, ouvrez **Gérer les certificats utilisateur**. Recherchez le certificat, généralement dans « Certificats - Utilisateur actuel\Personnel\Certificats », puis cliquez dessus avec le bouton droit. Cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**. Si vous ne trouvez pas le certificat sous Current User\Personal\Certificates, il est possible que vous ayez ouvert par erreur « Certificats – Ordinateur local » et non « Certificats – Utilisateur actuel ». Si vous voulez ouvrir le Gestionnaire de certificats dans le champ d’application utilisateur actuel en utilisant PowerShell, tapez *certmgr* dans la fenêtre de la console.

   ![Exportation](./media/certificates-for-backend-authentication/export.png)

2. Dans l’assistant, cliquez sur **Suivant**.

   ![Exportation du certificat](./media/certificates-for-backend-authentication/exportwizard.png)

3. Sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.

   ![N’exportez pas la clé privée](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base 64 X.509 (.cer).** , puis cliquez sur **Suivant**.

   ![Encodage en base 64](./media/certificates-for-backend-authentication/base64.png)

5. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.

   ![Parcourir](./media/certificates-for-backend-authentication/browse.png)

6. Cliquez sur **Terminer** pour exporter le certificat.

   ![Finish](./media/certificates-for-backend-authentication/finish.png)

7. Votre certificat est correctement exporté.

   ![Succès](./media/certificates-for-backend-authentication/success.png)

   Le certificat exporté ressemble à ceci :

   ![Exporté](./media/certificates-for-backend-authentication/exported.png)

8. Si vous ouvrez le certificat exporté à l’aide du Bloc-notes, vous verrez quelque chose de similaire à cet exemple. La section en bleu contient les informations chargées vers Application Gateway. Si vous ouvrez votre certificat avec le Bloc-notes et qu’il ne ressemble pas à celui de l’exemple, cela signifie généralement que vous ne l’avez pas exporté au format X.509 de base 64 (.cer). De plus, si vous voulez utiliser un autre éditeur de texte, notez que certains éditeurs peuvent ajouter une mise en forme non souhaitée en arrière-plan. Cela peut créer des problèmes quand vous chargez le texte de ce certificat sur Azure.

   ![Ouverture du fichier avec le Bloc-notes](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exporter le certificat racine approuvé (pour le SKU v2)

Un certificat racine approuvé est nécessaire pour mettre les instances back-end en liste verte dans le SKU v2 d’Application Gateway. Le certificat racine est un certificat racine au format X.509 avec codage base 64 (.CER) provenant des certificats de serveur back-end. Dans cet exemple, nous allons utiliser un certificat SSL pour le certificat de serveur back-end, nous allons exporter sa clé publique, puis le certificat racine de l’autorité de certification approuvée à partir de la clé publique au format de codage base64 afin d’obtenir le certificat racine approuvé. 

Les étapes suivantes vous aident à exporter le fichier .cer de votre certificat :

1. Utilisez les étapes 1 à 9 mentionnées dans la section **Exporter un certificat d’authentification à partir d’un certificat de serveur back-end (pour le SKU v1)** ci-dessus afin d’exporter la clé publique à partir de votre certificat de serveur back-end.

2. Une fois la clé publique exportée, ouvrez le fichier.

   ![certificat Open Authorization](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![à propos du certificat](./media/certificates-for-backend-authentication/general.png)

3. Accédez à la vue du chemin de certification pour voir l’autorité de certification.

   ![détails du certificat](./media/certificates-for-backend-authentication/certdetails.png)

4. Sélectionnez le certificat racine, puis cliquez sur **Afficher le certificat**.

   ![chemin du certificat](./media/certificates-for-backend-authentication/rootcert.png)

   Vous devez pouvoir voir les détails du certificat racine.

   ![informations du certificat](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Accédez à la vue **Détails**, puis cliquez sur **Copier dans un fichier**.

   ![copier le certificat racine](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. À ce stade, vous avez extrait les détails du certificat racine à partir du certificat de serveur back-end. Vous voyez s’ouvrir l’**Assistant Exportation du certificat**. Utilisez à présent les étapes 2 à 9 mentionnées dans la section **Exporter un certificat d’authentification à partir d’un certificat de serveur back-end (pour le SKU v1)** ci-dessus afin d’exporter le certificat racine approuvé au format X.509 avec codage base 64 (.CER).

## <a name="next-steps"></a>Étapes suivantes

Vous disposez maintenant du certificat d’authentification/certificat racine approuvé au format X.509 avec codage base 64 (.CER). Vous pouvez l’ajouter à la passerelle d’application afin de mettre vos serveurs back-end en liste verte pour le chiffrement SSL de bout en bout. Consultez [Comment configurer le chiffrement SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).