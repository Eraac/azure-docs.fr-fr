---
title: Ce démarrage rapide montre comment approvisionner un appareil X.509 simulé auprès du service Azure IoT Hub à l’aide de C | Microsoft Docs
description: Ce démarrage rapide utilise des inscriptions individuelles. Dans ce démarrage rapide, vous allez créer et approvisionner un appareil X.509 simulé à l’aide du kit de développement logiciel (SDK) de l’appareil C pour le service Azure IoT Hub Device Provisioning Service.
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ae5a1708b56db45bec38a4482b5dfd936bdedd4a
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901239"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Démarrage rapide : Provisionner un appareil X.509 simulé avec le SDK Azure IoT pour C

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Dans ce guide de démarrage rapide, vous allez apprendre à créer et à exécuter un simulateur d’appareil X.509 sur une machine de développement Windows. Vous allez configurer cet appareil simulé à affecter à un hub IoT à l’aide d’une inscription auprès d’une instance de service Device Provisioning. Un exemple de code du [Kit de développement logiciel (SDK) Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) est utilisé pour simuler une séquence de démarrage de l’appareil. L’appareil est reconnu en fonction de l’inscription auprès du service d’approvisionnement et affecté au hub IoT.

Si vous ne connaissez pas le processus de provisionnement automatique, veuillez consulter [Concepts de provisionnement automatique](concepts-auto-provisioning.md). Vérifiez également que vous avez suivi la procédure décrite dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md) avant de poursuivre ce démarrage rapide. 

Le service Azure IoT Device Provisioning prend en charge deux types d’inscriptions :
- [Groupes d’inscription](concepts-service.md#enrollment-group) : utilisés pour inscrire plusieurs appareils connexes.
- [Inscriptions individuelles](concepts-service.md#individual-enrollment) : utilisées pour inscrire un seul appareil.

Cet article présente les inscriptions individuelles.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 ou version ultérieure avec la charge de travail [« Développement Desktop en C++ »](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) activée.
* Dernière version de [Git](https://git-scm.com/download/) installée.


<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Préparer un environnement de développement pour le Kit de développement logiciel (SDK) Azure IoT pour C

Dans cette section, vous allez préparer un environnement de développement pour générer le [Kit de développement logiciel (SDK) Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) qui inclut l’exemple de code pour la séquence de démarrage X.509.

1. Téléchargez le [système de génération CMake](https://cmake.org/download/).

    Il est important que les composants requis Visual Studio (Visual Studio et la charge de travail « Développement Desktop en C++ ») soient installés sur votre machine, **avant** de commencer l’installation de l’élément `CMake`. Une fois les composants requis en place et le téléchargement effectué, installez le système de génération de CMake.

2. Ouvrez une invite de commandes ou l’interpréteur de commandes Git Bash. Exécutez la commande suivante pour cloner le référentiel GitHub du [Kit de développement logiciel (SDK) Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) :
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Attendez-vous à ce que cette opération prenne plusieurs minutes.


3. Créez un sous-répertoire `cmake` dans le répertoire racine du référentiel Git et accédez à ce dossier. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. L’exemple de code utilise un certificat X.509 pour fournir l’attestation via l’authentification X.509. Exécutez la commande suivante qui génère une version du SDK propre à votre plateforme cliente de développement. Une solution Visual Studio pour l’appareil simulé est générée dans le répertoire `cmake`. 

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    Si `cmake` ne trouve pas votre compilateur C++, vous obtiendrez peut-être des erreurs de build lors de l’exécution de la commande ci-dessus. Si cela se produit, essayez d’exécuter cette commande dans [l’invite de commandes de Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Une fois la génération terminée, les dernières lignes de sortie doivent ressembler à la sortie suivante :

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```



<a id="portalenroll"></a>

## <a name="create-a-self-signed-x509-device-certificate"></a>Créer un certificat auto-signé d’appareil X.509

Dans cette section, vous allez utiliser un certificat X.509 auto-signé. Il est important de garder à l’esprit les points suivants :

* Les certificats auto-signés sont destinés aux tests uniquement et ne doivent pas être utilisés en production.
* La date d’expiration par défaut d’un certificat auto-signé est d’un an.

Vous allez utiliser l’exemple de code du Kit de développement logiciel (SDK) Azure IoT pour C pour créer le certificat à utiliser avec l’entrée d’inscription individuelle de l’appareil simulé.

1. Lancez Visual Studio et ouvrez le nouveau fichier solution nommé `azure_iot_sdks.sln`. Ce fichier solution se trouve dans le dossier `cmake` que vous avez créé précédemment à la racine du référentiel Git azure-iot-sdk-c.

2. Dans le menu Visual Studio, sélectionnez **Génération** > **Générer la solution** pour générer tous les projets dans la solution.

3. Dans la fenêtre *Explorateur de solutions* de Visual Studio, accédez au dossier **Provision\_Tools**. Cliquez avec le bouton droit sur le projet**dice\_device\_enrollment** et sélectionnez **Définir comme projet de démarrage**. 

4. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Exécuter sans débogage** pour exécuter la solution. Dans la fenêtre Sortie, entrez **i** pour l’inscription individuelle lorsque vous y êtes invité. 

    La fenêtre Sortie affiche un certificat X.509 auto-signé généré localement pour votre appareil simulé. Dans le Presse-papiers, copiez la sortie débutant par **-----BEGIN CERTIFICATE-----** et se terminant par **-----END CERTIFICATE-----** , en veillant à inclure également ces deux lignes. Vous n’avez besoin que du premier certificat dans la fenêtre Sortie.
 
5. Dans un éditeur de texte, enregistrez le certificat dans un nouveau fichier nommé **_X509testcert.pem_** . 


## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Créer une entrée d’inscription d’appareil dans le portail

1. Connectez-vous au portail Azure, cliquez sur le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre instance Device Provisioning Service.

2. Sélectionnez l’onglet **Gérer les inscriptions**, puis cliquez sur le bouton **Ajouter une inscription individuelle** dans la partie supérieure. 

3. Dans **Ajouter une inscription**, entrez les informations suivantes, puis cliquez sur le bouton **Enregistrer**.

    - **Mécanisme :** Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
    - **Fichier .pem ou .cer du certificat principal :** Cliquez sur **Sélectionner un fichier** pour sélectionner le fichier de certificat, X509testcert.pem, que vous avez créé précédemment.
    - **ID de l’appareil IoT Hub :** Entrez **test-docs-cert-device** pour donner un ID à l’appareil.

      [![Ajouter une inscription individuelle pour l’attestation X.509 dans le portail](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Lorsque l’inscription aboutit, votre appareil X.509 apparaît en tant que **riot-device-cert** sous la colonne *ID d’inscription* dans l’onglet *Inscriptions individuelles*. 



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simuler la première séquence de démarrage de l’appareil

Dans cette section, mettez à jour l’exemple de code pour envoyer la séquence de démarrage de l’appareil vers votre instance de service Device Provisioning. Cette séquence de démarrage entraîne la reconnaissance et l’affectation de l’appareil à un hub IoT lié à l’instance de service Device Provisioning.



1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de votre service Device Provisioning et notez les valeurs de **_Étendue de l’ID_** .

    ![Extraction des informations de point de terminaison du service Device Provisioning à partir du panneau du Portail](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Dans la fenêtre *Explorateur de solutions* de Visual Studio, accédez au dossier **Provision\_Samples**. Développez l’exemple de projet nommé **prov\_dev\_client\_sample**. Développez **Fichiers sources**, puis ouvrez **prov\_dev\_client\_sample.c**.

3. Recherchez la constante `id_scope` et remplacez la valeur par la valeur **Étendue de l’ID** que vous avez copiée précédemment. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Recherchez la définition de la fonction `main()` dans le même fichier. Assurez-vous que la variable `hsm_type` est définie sur `SECURE_DEVICE_TYPE_X509` au lieu de `SECURE_DEVICE_TYPE_TPM` comme indiqué ci-dessous.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Cliquez avec le bouton droit sur le projet **prov\_dev\_client\_sample** et sélectionnez **Définir comme projet de démarrage**. 

6. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Exécuter sans débogage** pour exécuter la solution. Dans l’invite pour régénérer le projet, cliquez sur **Oui** pour régénérer le projet avant l’exécution.

    La sortie suivante illustre un exemple d’approvisionnement d’un client d’appareil, son démarrage réussi, sa connexion à une instance de service Device Provisioning pour obtenir des informations sur le hub IoT et son inscription :

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device    
    ```

7. Dans le portail, accédez au hub IoT lié à votre service d’approvisionnement, puis cliquez sur l’onglet **Appareils IoT**. En cas de réussite de l’approvisionnement de l’appareil X.509 simulé sur le Hub, son ID d’appareil s’affiche sur le panneau **IoT Device**, avec un *ÉTAT* **activé**. Vous devrez peut-être cliquer sur le bouton **Actualiser** dans la partie supérieure. 

    ![L’appareil est inscrit avec le hub IoT](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer à manipuler et explorer l’exemple de client d’appareil, ne nettoyez pas les ressources créées lors de ce démarrage rapide. Sinon, procédez aux étapes suivantes pour supprimer toutes les ressources créées lors de ce démarrage rapide.

1. Fermez la fenêtre de sortie de l’exemple de client d’appareil sur votre machine.
1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre service Device Provisioning. Ouvrez **Gérer les inscriptions** pour votre service, puis cliquez sur l’onglet **Inscriptions individuelles**. Sélectionnez l’*ID D’INSCRIPTION* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure. 
1. À partir du menu de gauche, dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre IoT Hub. Ouvrez **Appareils IoT** pour votre hub, sélectionnez *l’ID D’APPAREIL* de l’appareil que vous avez inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un appareil X.509 simulé sur un ordinateur Windows. Vous l’avez également approvisionné vers votre hub IoT à l’aide du service Azure IoT Hub Device Provisioning figurant sur le portail. Pour savoir comment inscrire un appareil X.509 au moyen d’un programme, poursuivez avec le démarrage rapide correspondant. 

> [!div class="nextstepaction"]
> [Démarrage rapide d’Azure : Inscrire des appareils X.509 auprès du service Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
