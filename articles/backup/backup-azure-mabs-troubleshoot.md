---
title: Résoudre les problèmes d’un serveur de sauvegarde Azure
description: Résolvez les problèmes d’installation et d’enregistrement du serveur de sauvegarde Azure, mais aussi de sauvegarde et de restauration des charges de travail applicatives.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: kasinh
ms.openlocfilehash: 06faed8ceca77edc20b67f73a76d885839aa7dbc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304336"
---
# <a name="troubleshoot-azure-backup-server"></a>Résoudre les problèmes d’un serveur de sauvegarde Azure

Résolvez les erreurs se produisant lors de l’utilisation d’un serveur de sauvegarde Azure à l’aide des informations figurant dans les tables suivantes.

## <a name="basic-troubleshooting"></a>Dépannage de base

Nous vous recommandons d’effectuer les validations ci-dessous avant de résoudre les problèmes liés à Microsoft Azure Backup Server (MABS) :

- [Vérifiez que l'agent Microsoft Azure Recovery Services (MARS) est à jour](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Vérifiez la connectivité réseau entre l’agent MARS et Azure](https://aka.ms/AB-A4dp50)
- Assurez-vous que Microsoft Azure Recovery Services est en cours d’exécution (dans la console de service). Si nécessaire, redémarrez le système et recommencez l’opération
- [Vérifiez qu’il existe entre 5 et 10 % d’espace de volume disponible à l’emplacement du dossier de travail](https://aka.ms/AB-AA4dwtt)
- En cas d'échec d'inscription, vérifiez si le serveur sur lequel vous essayez d’installer le serveur de sauvegarde Azure n’est pas déjà inscrit sur un autre coffre
- Si l’installation push échoue, vérifiez si l’agent DPM est déjà présent. Si c’est le cas, désinstallez l’agent et recommencez l’installation
- [Assurez-vous qu’aucun autre processus ou logiciel antivirus n’interfère avec la sauvegarde Azure](https://aka.ms/AA4nyr4)<br>
- Vérifiez que le service SQL Agent est en cours d'exécution et défini sur automatique dans le serveur MAB<br>


## <a name="invalid-vault-credentials-provided"></a>Informations d’identification du coffre fournies non valides

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Enregistrement dans un coffre | Informations d’identification du coffre fournies non valides. Cela signifie que le fichier est endommagé ou qu’il ne contient pas les dernières informations d’identification associées au service de récupération. | Action recommandée : <br> <ul><li> Téléchargez le dernier fichier d’informations d’identification du coffre et réessayez. <br>(OU)</li> <li> Si l’action précédente n’a pas fonctionné, essayez de télécharger les informations d’identification dans un répertoire local différent, ou créez un autre coffre. <br>(OU)</li> <li> Mettez à jour les paramètres de date et l’heure, comme expliqué dans [ce blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(OU)</li> <li> Vérifiez si le répertoire c:\windows\temp possède plus de 65000 fichiers. Déplacez les fichiers obsolètes vers un autre emplacement, ou supprimez les éléments figurant dans le dossier Temp. <br>(OU)</li> <li> Vérifiez l’état des certificats. <br> a. Sélectionnez **Gérer les certificats d’ordinateur** (dans le Panneau de configuration). <br> b. Développez le nœud **Personnel** et son nœud enfant appelé **Certificats**.<br> c.  Supprimez le certificat **Outils Windows Azure**. <br> d. Recommencez l’enregistrement dans le client de sauvegarde Azure. <br> (OU) </li> <li> Vérifiez si une stratégie de groupe est appliquée. </li></ul> |

## <a name="replica-is-inconsistent"></a>Le réplica est incohérent.

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Sauvegarde | Le réplica est incohérent. | Vérifiez que l’option de vérification automatique de la cohérence est activée dans l’Assistant Groupe de protection. Pour en savoir plus sur les causes d’incohérence du réplica et consulter des suggestions pertinentes, consultez l’article [Le réplica est incohérent](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> En cas de sauvegarde de l’état système/sauvegarde à des fins de récupération complète, vérifiez que la sauvegarde de Windows Server est installée sur le serveur protégé.</li><li> Recherchez d’éventuels problèmes liés au manque d’espace dans le pool de stockage DPM sur le serveur DPM/de sauvegarde Azure, et allouez le stockage selon les besoins.</li><li> Vérifiez l’état du service VVS (Volume Shadow Copy) sur le serveur protégé. S’il est désactivé, définissez la propriété pour démarrer manuellement. Démarrez le service sur le serveur. Ensuite, revenez à la console DPM/du serveur de sauvegarde Azure et démarrez la tâche de synchronisation avec vérification de la cohérence.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Échec de la création de points de récupération en ligne.

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Sauvegarde | Échec de la création de points de récupération en ligne. | **Message d’erreur** : Windows Azure Backup Agent n’a pas pu créer un instantané du volume sélectionné. <br> **Solution de contournement** : augmentez la taille de l’espace dans le volume du point de récupération et le réplica.<br> <br> **Message d’erreur** : Windows Azure Backup Agent ne peut pas se connecter au service OBEngine <br> **Solution de contournement** : vérifiez qu’OBEngine existe dans la liste des services en cours d’exécution sur l’ordinateur. Si le service OBEngine n’est pas en cours d’exécution, utilisez la commande « net start OBEngine » pour le démarrer. <br> <br> **Message d’erreur** : La phrase secrète de chiffrement de ce serveur n’est pas définie. Veuillez configurer une phrase secrète de chiffrement. <br> **Solution de contournement** : essayez de configurer une phrase secrète de chiffrement. En cas d’échec, suivez les étapes ci-dessous : <br> <ol><li>Vérifiez que l’emplacement temporaire existe. L’emplacement mentionné dans la clé de Registre **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config** qui porte le nom **ScratchLocation** doit exister.</li><li> Si l’emplacement temporaire existe, enregistrez à nouveau l’ancienne phrase secrète. *Chaque fois que vous configurez une phrase secrète de chiffrement, enregistrez-la dans un emplacement sécurisé.*</li><ol>|

## <a name="the-vault-credentials-provided-are-different-from-the-vault-the-server-is-registered"></a>Les informations d’identification du coffre fournies ne correspondent pas au coffre auprès duquel le serveur est enregistré.

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Restore | **Code d’erreur** : CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Message d’erreur** : Les informations d’identification du coffre fournies ne correspondent pas au coffre auprès duquel le serveur est enregistré. | **Cause** : ce problème se produit lorsque vous essayez de restaurer des fichiers vers un autre serveur que le serveur d’origine à l’aide de l'option de récupération DPM externe, et si le serveur en cours de récupération et le serveur d’origine à partir duquel les données sont sauvegardées ne sont pas associés au même coffre Recovery Services.<br/> <br/>**Solution de contournement** Pour résoudre ce problème, assurez-vous que le serveur d'origine et l'autre serveur sont enregistrés dans le même coffre.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Les tâches de création de points de récupération en ligne pour les machines virtuelles VMware échouent.

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Sauvegarde | Les tâches de création de points de récupération en ligne pour les machines virtuelles VMware échouent. DPM a rencontré une erreur à partir de VMware alors qu’il essayait de récupérer des informations de suivi des modifications. Code d’erreur : FileFaultFault (ID 33621) |  <ol><li> Réinitialisez le processus CTK sur VMware pour les machines virtuelles affectées.</li> <li>Vérifiez que le disque indépendant n’est pas en place sur VMware.</li> <li>Arrêtez la protection pour les machines virtuelles affectées et redémarrez-la avec le bouton **d’actualisation**. </li><li>Exécutez une vérification de cohérence pour les machines virtuelles affectées.</li></ol>|


## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>L’opération de l’agent a échoué en raison d’une erreur de communication avec le service Coordinateur d’agents DPM sur le serveur

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Envoi d’un ou de plusieurs agents à des serveurs protégés via une transmission de type push | L’opération de l’agent a échoué en raison d’une erreur de communication avec le service Coordinateur d’agents DPM sur \<ServerName>. | **Si l’action recommandée affichée dans le produit ne fonctionne pas, procédez comme suit** : <ul><li> Si vous joignez un ordinateur à partir d’un domaine non approuvé, suivez [ces étapes](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (OU) </li><li> Si vous joignez un ordinateur à partir d’un domaine approuvé, résolvez les problèmes à l’aide de la procédure décrite dans [ce blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(OU)</li><li> Essayez de désactiver l’antivirus. Si cela résout le problème, modifiez les paramètres de l’antivirus comme suggéré dans [cet article](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Le programme d’installation n’a pas pu mettre à jour les métadonnées du registre

| Opération | Détails de l’erreur | Solution de contournement |
|-----------|---------------|------------|
|Installation | Le programme d’installation n’a pas pu mettre à jour les métadonnées du registre. L’échec de cette mise à jour peut entraîner la surutilisation de la consommation du stockage. Pour éviter ce problème, mettez à jour l’entrée de registre ReFS Trimming. | Ajustez la clé de registre **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Définissez la valeur Dword sur 1. |
|Installation | Le programme d’installation n’a pas pu mettre à jour les métadonnées du registre. L’échec de cette mise à jour peut entraîner la surutilisation de la consommation du stockage. Pour éviter ce problème, mettez à jour l’entrée de registre Volume SnapOptimization. | Créez la clé de registre, **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds**, avec une valeur de chaîne vide. |

## <a name="registration-and-agent-related-issues"></a>Problèmes liés à l’enregistrement et aux agents

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Envoi d’un ou de plusieurs agents à des serveurs protégés via une transmission de type push | Les informations d’identification spécifiées pour le serveur ne sont pas valides. | **Si l’action recommandée dans le produit ne fonctionne pas, procédez comme suit** : <br> Installez manuellement l’agent de protection sur le serveur de production, comme indiqué dans [cet article](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Azure Backup Agent n'a pas pu se connecter au service Sauvegarde Azure (ID : 100050) | Azure Backup Agent n’a pas pu se connecter au service Sauvegarde Azure. | **Si l’action recommandée dans le produit ne fonctionne pas, procédez comme suit** : <br>1. Exécutez la commande suivante à partir d’une invite avec élévation de privilèges : **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Une fenêtre Internet Explorer s’ouvre. <br/> 2. Accédez à **Outils** > **Options Internet** > **Connexions** > **Paramètres réseau**. <br/> 3. Modifiez les paramètres pour utiliser un serveur proxy. Fournissez ensuite les détails du serveur proxy.<br/> 4. Si votre machine a un accès à Internet limité, assurez-vous que les paramètres du pare-feu sur la machine ou le proxy autorisent ces [URL](backup-configure-vault.md#verify-internet-access) et cette [adresse IP](backup-configure-vault.md#verify-internet-access).|
| L’installation d’Azure Backup Agent a échoué | L’installation de Microsoft Azure Recovery Services a échoué. Toutes les modifications apportées par l’installation de Microsoft Azure Recovery Services au système ont été restaurées. (ID : 4024) | Installez l’agent Azure manuellement.


## <a name="configuring-protection-group"></a>Configuration du groupe de protection

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Configuration des groupes de protection | DPM n’a pas pu énumérer le composant d’application sur l’ordinateur protégé (nom de l’ordinateur protégé). | Cliquez sur l’option **Actualiser** figurant sur l’écran de configuration du groupe de protection, au niveau du composant/de la source de données pertinents. |
| Configuration des groupes de protection | Configuration de la protection impossible. | Si le serveur protégé est de type SQL Server, vérifiez si les autorisations du rôle sysadmin ont été fournies au compte système (NTAuthority\System) sur l’ordinateur protégé, comme indiqué dans [cet article](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Configuration des groupes de protection | Le pool de stockage présente un espace disponible insuffisant pour ce groupe de protection. | Les disques ajoutés au pool de stockage [ne doivent pas contenir de partitions](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Supprimez les volumes existants sur les disques. Puis ajoutez-les au pool de stockage.|
| Modification de la stratégie |Impossible de modifier la stratégie de sauvegarde. Error: L'opération en cours a échoué en raison d'une erreur de service interne [0x29834]. Veuillez réessayer l’opération après un certain temps. Si le problème persiste, contactez le support technique Microsoft. | **Cause :**<br/>Cette erreur se produit dans certaines conditions : lorsque les paramètres de sécurité sont activés, que vous essayez de réduire la durée de rétention sur une valeur inférieure aux valeurs minimales spécifiées précédemment et que vous utilisez une version non prise en charge (antérieure à la version Microsoft Azure Backup Server 2.0.9052 et update 1). <br/>**Action recommandée :**<br/> Pour effectuer les mises à jour liées aux stratégies, définissez la période de conservation sur une valeur supérieure à la période de conservation minimale spécifiée. (la période de conservation minimale quotidienne est de sept jours, hebdomadaire de quatre semaines, mensuelle de trois semaines et annuelle d’une année). <br><br>Sinon, vous pouvez mettre à jour l’agent de sauvegarde et le serveur de sauvegarde Azure pour tirer parti de toutes les mises à jour de sécurité. |

## <a name="backup"></a>Sauvegarde

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Sauvegarde | Une erreur inattendue s’est produite pendant l’exécution de la tâche. Le périphérique n’est pas prêt. | **Si l’action recommandée dans le produit ne fonctionne pas, procédez comme suit :** <br> <ul><li>Définissez l’espace de stockage des clichés instantanés sur Illimité pour les éléments du groupe de protection, puis exécutez la vérification de la cohérence.<br></li> (OU) <li>Essayez de supprimer le groupe de protection existant et de créer plusieurs groupes. Chaque nouveau groupe de protection doit contenir un élément individuel.</li></ul> |
| Sauvegarde | Si vous sauvegardez uniquement l’état du système, vérifiez que l’ordinateur présente un espace disponible suffisant pour stocker cette sauvegarde. | <ol><li>Vérifiez que la sauvegarde Windows Server est installée sur l’ordinateur protégé.</li><li>Vérifiez que l’ordinateur protégé dispose d’un espace suffisant pour l’état du système. Pour ce faire, le plus simple est d’accéder à l’ordinateur protégé, d’ouvrir la sauvegarde Windows Server, de cliquer sur les sélections, puis de sélectionner la récupération complète. L’interface utilisateur vous indique ensuite l’espace requis. Ouvrez la **sauvegarde Windows Server** >  **, puis cliquez sur Sauvegarde locale** > **Planification de la sauvegarde** > **Sélectionner la configuration de sauvegarde** > **Serveur complet** (la taille est affichée). Utilisez cette taille à des fins de vérification.</li></ol>
| Sauvegarde | Échec de la sauvegarde à des fins de récupération complète | Si la taille de la récupération complète est importante, déplacez certains fichiers d’application vers le disque du système d’exploitation et recommencez. |
| Sauvegarde | L’option de renouvellement de la protection de machine virtuelle VMware sur un nouveau serveur de sauvegarde Azure n’est pas répertoriée comme disponible. | Les propriétés VMware sont pointés vers une ancienne instance du serveur de sauvegarde Microsoft Azure mise hors service. Pour résoudre ce problème :<br><ol><li>Dans VCenter (équivalent SC-VMM), accédez à l’onglet **Résumé**, puis **Attributs personnalisés**.</li>  <li>Supprimez le nom de l’ancien serveur de sauvegarde Microsoft Azure de la valeur **DPMServer**.</li>  <li>Revenez au nouveau serveur de sauvegarde Microsoft Azure et modifiez le groupe de protection.  Après un clic sur le bouton **Actualiser**, la machine virtuelle comporte une case à cocher indiquant qu’elle peut être ajoutée à la protection.</li></ol> |
| Sauvegarde | Erreur lors de l’accès aux dossiers/fichiers partagés. | Essayez de modifier les paramètres d’antivirus comme indiqué dans l’article TechNet [Exécution d’un logiciel antivirus sur le serveur DPM](https://technet.microsoft.com/library/hh757911.aspx).|


## <a name="change-passphrase"></a>Modification de la phrase secrète

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Modification de la phrase secrète |Le code PIN de sécurité saisi est incorrect. Entrez le code PIN de sécurité correct pour exécuter cette opération. |**Cause :**<br/> Cette erreur se produit lorsque vous entrez un code PIN de sécurité non valide ou qui a expiré lors d’une opération critique (par exemple, la modification d’un mot de passe). <br/>**Action recommandée :**<br/> Pour exécuter l’opération, vous devez entrer un code PIN de sécurité valide. Pour obtenir le code PIN, connectez-vous au portail Azure, puis recherchez le coffre Recovery Services. Ensuite, accédez à **Paramètres** > **Propriétés** > **Générer un code PIN de sécurité**. Utilisez ce code PIN pour modifier la phrase secrète. |
| Modification de la phrase secrète |L’opération a échoué. ID : 120002 |**Cause :**<br/>Cette erreur se produit lorsque les paramètres de sécurité sont activés ou lorsque vous tentez de modifier la phrase secrète sur une version non prise en charge.<br/>**Action recommandée :**<br/> Pour modifier la phrase secrète, vous devez d’abord mettre à jour l’agent de sauvegarde vers la version minimale (la version 2.0.9052). Vous devez également mettre à jour le serveur de sauvegarde Azure vers la version update 1 au minimum, puis entrer le code PIN de sécurité valide. Pour obtenir le code PIN, connectez-vous au portail Azure, puis recherchez le coffre Recovery Services. Ensuite, accédez à **Paramètres** > **Propriétés** > **Générer un code PIN de sécurité**. Utilisez ce code PIN pour modifier la phrase secrète. |


## <a name="configure-email-notifications"></a>Configurer les notifications par e-mail

| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Configuration des notifications par e-mail à l’aide d’un compte Office 365 |ID d'erreur : 2013| **Cause :**<br> Tentative d’utiliser le compte Office 365 <br>**Action recommandée :**<ol><li> La première chose à vérifier est que l’option permettant d’autoriser un relais anonyme sur un connecteur de réception pour votre serveur DPM est configurée sur Exchange. Pour plus d’informations sur cette configuration, consultez [Autoriser le relais anonyme sur un connecteur de réception](https://technet.microsoft.com/library/bb232021.aspx) sur TechNet.</li> <li> Si vous ne pouvez pas utiliser un relais SMTP interne et devez effectuer la configuration à l’aide de votre serveur Office 365, vous pouvez configurer IIS en tant que relais. Configurez le serveur DPM pour [relayer SMTP vers O365 en utilisant IIS](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **IMPORTANT :** veillez à utiliser le format user\@domain.com et *non pas* le format domaine\utilisateur.<br><br><li>Faites en sorte que DPM utilise le nom du serveur local en guise de serveur SMTP, port 587. Ensuite, faites-lui utiliser l’adresse e-mail d’utilisateur dont doivent provenir les e-mails.<li> Le nom d’utilisateur et le mot de passe dans la page d’installation de DPM SMTP doivent correspondre à un compte de domaine rattaché au domaine où se trouve DPM. </li><br> **REMARQUE** : lorsque vous changez l'adresse du serveur SMTP, une fois les modifications effectuées, fermez la zone des paramètres, puis rouvrez-la pour vérifier qu'elle reflète la nouvelle valeur.  Se limiter à changer et à tester ne suffit pas toujours pour que les nouveaux paramètres soient pris en compte. C’est pourquoi effectuer un test de cette façon est une bonne pratique.<br><br>Au cours de ce processus, vous pouvez à tout moment supprimer ces paramètres en fermant la console DPM et en modifiant les clés de Registre suivantes : **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Supprimer les clés SMTPPassword et SMTPUserName**. Vous pouvez les rajouter à l’interface utilisateur quand vous la relancez.
