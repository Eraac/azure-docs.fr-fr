---
title: Sortie et messages de runbook dans Azure Automation
description: Décrit comment créer et récupérer la sortie et les messages d’erreur à partir des Runbooks dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c129391c0830e0194c32a041853482f92340bbb9
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405791"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Sortie et messages de Runbook dans Azure Automation
La plupart des Runbooks Azure Automation ont une certaine forme de sortie. Cette sortie peut être un message d’erreur destiné à l’utilisateur ou un objet complexe que vous souhaitez utiliser avec un autre Runbook. Windows PowerShell fournit [plusieurs flux](/powershell/module/microsoft.powershell.core/about/about_redirection) pour l’envoi d’une sortie à partir d’un script ou d’un flux de travail. Azure Automation fonctionne différemment avec chaque flux. Vous devez suivre les meilleures pratiques d’utilisation lorsque vous créez un Runbook.

Le tableau suivant fournit une brève description de chacun des flux et de leur comportement dans le portail Azure pour les Runbooks publiés et lors du [test d’un Runbook](automation-testing-runbook.md). Pour plus de détails sur chaque flux, reportez-vous aux sections suivantes.

| Stream | Description | Publié | Test |
|:--- |:--- |:--- |:--- |
| Output |Objets destinés à être consommés par d’autres Runbooks. |Consignation dans l’historique des tâches. |Affichage dans le volet de sortie du test. |
| Avertissement |Message d’avertissement destiné à l’utilisateur. |Consignation dans l’historique des tâches. |Affichage dans le volet de sortie du test. |
| Error |Message d’erreur destiné à l’utilisateur. Contrairement à une exception, le Runbook se poursuit après un message d’erreur par défaut. |Consignation dans l’historique des tâches. |Affichage dans le volet de sortie du test. |
| Détaillé |Messages fournissant des informations générales ou de débogage. |Consignation dans l’historique des tâches uniquement si la journalisation documentée est activée pour le Runbook. |Affichage dans le volet de sortie du test uniquement si la préférence $VerbosePreference a la valeur Continue dans le Runbook. |
| Progression |Informations générées automatiquement avant et après chaque activité dans le Runbook. Le Runbook ne doit pas tenter de créer ses propres informations de progression dans la mesure où elles sont destinées à un utilisateur interactif. |Consignation dans l’historique des tâches uniquement si l’enregistrement de la progression est activé pour le Runbook. |Pas d’affichage dans le volet de sortie du test. |
| Déboguer |Messages destinés à un utilisateur interactif. Utilisation proscrite dans les Runbooks. |Pas de consignation dans l’historique des tâches. |Pas d’affichage dans le volet de sortie du test. |

## <a name="output-stream"></a>Flux de sortie
Le flux de sortie est destiné à la sortie des objets créés par un script ou un flux de travail lorsqu’il s’exécute correctement. Dans Azure Automation, ce flux est principalement utilisé pour les objets destinés à être consommés par les [Runbooks parents qui appellent le Runbook actuel](automation-child-runbooks.md). Lorsque vous [appelez un Runbook en ligne](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) à partir d’un Runbook parent, il retourne les données au parent à partir du flux de sortie. Utilisez le flux de sortie uniquement pour retourner des informations générales à l’utilisateur si vous savez que le Runbook n’est jamais appelé par un autre Runbook. Cependant, nous vous recommandons d’utiliser le [flux des commentaires](#verbose-stream) pour communiquer des informations générales à l’utilisateur.

Vous pouvez écrire des données dans le flux de sortie à l’aide de [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) ou en plaçant l’objet sur sa propre ligne dans le Runbook.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Sortie à partir d’une fonction
Lorsque vous écrivez dans le flux de sortie, dans une fonction qui est incluse dans votre Runbook, la sortie est renvoyée au Runbook. Si le Runbook affecte cette sortie à une variable, elle n’est pas écrite dans le flux de sortie. Toute écriture dans d’autres flux à partir de la fonction se fait dans le flux correspondant du Runbook.

Examinez l’exemple de Runbook suivant :

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Le flux de sortie pour la tâche du Runbook serait :

```output
Output inside of function
Output outside of function
```

Le flux des commentaires pour la tâche du Runbook serait :

```output
Verbose outside of function
Verbose inside of function
```

Une fois que vous avez publié le Runbook et avant de démarrer, vous devez activer la journalisation détaillée dans les paramètres du Runbook pour obtenir la sortie de flux détaillé.

### <a name="declaring-output-data-type"></a>Déclaration du type de données de sortie
Un flux de travail peut spécifier le type de données de sa sortie à l’aide de l’ [attribut OutputType](https://technet.microsoft.com/library/hh847785.aspx). Cet attribut n’a aucun effet lors de l’exécution, mais il fournit une indication à l’auteur du Runbook sur la sortie attendue du Runbook au moment de la conception. Alors que l’ensemble d’outils dédiés aux Runbooks continue d’évoluer, la déclaration des types de données de sortie au moment de la conception gagne en importance. Par conséquent, il est recommandé d’inclure cette déclaration dans tous les Runbooks que vous créez.

Voici une liste d’exemples de types de sortie :

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

L’exemple de Runbook suivant génère un objet String et inclut une déclaration de son type de sortie. Si votre Runbook génère un tableau d’un certain type, vous devez toujours spécifier le type, par opposition à un tableau du type.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Pour déclarer un type de sortie dans les Runbooks graphiques ou PowerShell Workflow graphiques, vous pouvez sélectionner l’option de menu **Entrée et sortie** et taper le nom du type de sortie. Nous vous recommandons d’utiliser le nom de classe .NET complet pour le rendre facilement identifiable lorsque vous y faites référence dans un Runbook parent. Cela expose toutes les propriétés de la classe au bus de données du Runbook et offre une grande souplesse lors de leur utilisation pour la logique conditionnelle, la journalisation et le référencement en tant que valeurs pour les autres activités du Runbook.<br> ![Option Entrée et sortie de Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

Dans l’exemple suivant, vous avez deux Runbooks graphiques illustrant cette fonctionnalité. Si le modèle de conception Runbook modulaire est appliqué, l’un des Runbooks est utilisé en tant que *modèle Runbook d’authentification* et gère l’authentification auprès d’Azure à l’aide du compte d’identification. Le deuxième Runbook, qui effectue normalement les opérations logiques principales pour automatiser un scénario donné, exécute alors le *modèle Runbook d’authentification* et affiche les résultats dans le volet de sortie **Test** . Dans des circonstances normales, ce Runbook effectue ses activités par rapport à une ressource utilisant la sortie du Runbook enfant.

Voici la logique de base du Runbook **AuthenticateTo-Azure**.<br> ![Exemple de modèle Runbook d’authentification](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Il inclut le type de sortie *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, qui retourne les propriétés de profil d’authentification.<br> ![Exemple de type de sortie Runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Ce Runbook est simple, mais l’un des éléments de configuration nécessite une attention particulière. La dernière activité exécute l’applet de commande **Write-Output** et écrit les données de profil dans une variable $_ à l’aide d’une expression PowerShell pour le paramètre **Inputobject**, qui est requis pour cette applet de commande.  

Pour le deuxième Runbook de cet exemple, nommé *Test-ChildOutputType*, vous n’avez que deux activités.<br> ![Exemple de type de sortie enfant Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

La première activité appelle le Runbook **AuthenticateTo-Azure**, et la seconde exécute l’applet de commande **Write-Verbose** avec la **source de données** de la **sortie de l’activité**. La valeur du **chemin du champ** est **Context.Subscription.SubscriptionName** et spécifie la sortie de contexte du Runbook **AuthenticateTo-Azure**.<br> ![Source de données du paramètre d’applet de commande Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Le résultat obtenu est le nom de l’abonnement.<br> ![Résultats du Runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Une remarque sur le comportement de la commande Type de sortie. Lorsque vous entrez une valeur dans le champ Type de sortie dans le panneau des propriétés Entrée et sortie, vous devez cliquer en dehors de la commande après avoir entré la valeur, afin que votre entrée soit reconnue par la commande.  

## <a name="message-streams"></a>Flux de messages
Contrairement au flux de sortie, les flux de messages sont destinés à la communication des informations à l’utilisateur. Il existe plusieurs flux de messages pour les différents types d’informations, et chacun d’eux est traité différemment par Azure Automation.

### <a name="warning-and-error-streams"></a>Flux d’erreurs et d’avertissements
Les flux d’erreurs et d’avertissements sont conçus pour l’enregistrement des problèmes qui se produisent dans un Runbook. Ils sont consignés dans l’historique des tâches quand un Runbook est exécuté et sont inclus dans le volet de sortie du test dans le portail Azure quand un Runbook est testé. Par défaut, l’exécution du Runbook se poursuit après un avertissement ou une erreur. Vous pouvez spécifier l’interruption du Runbook en cas d’avertissement ou d’erreur en définissant une [variable de préférence](#preference-variables) dans le Runbook avant de créer le message. Par exemple, pour déclencher l’interruption du Runbook en cas d’erreur, comme dans le cas d’une exception, affectez la valeur Stop à **$ErrorActionPreference** .

Créez un message d’avertissement ou un message d’erreur à l’aide de l’applet de commande [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) ou [Write-Error](https://technet.microsoft.com/library/hh849962.aspx). Les activités peuvent également écrire dans ces flux.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>flux des commentaires
Ce flux est utilisé pour les informations générales sur le fonctionnement du Runbook. Dans la mesure où le [flux de débogage](#debug-stream) n’est pas disponible dans un Runbook, les messages documentés doivent être utilisés pour les informations de débogage. Par défaut, les messages documentés provenant de Runbooks publiés ne sont pas stockés dans l’historique des tâches. Pour stocker les messages documentés, configurez les Runbooks publiés de façon à enregistrer les informations de commentaires sous l’onglet de configuration du Runbook dans le portail Azure. Pour des raisons de performances, il est généralement recommandé de conserver le paramètre par défaut (les informations de commentaires ne sont pas enregistrées pour le Runbook). Activez cette option uniquement pour résoudre les problèmes liés à un Runbook ou déboguer un Runbook.

Lors du [test d’un Runbook](automation-testing-runbook.md), les messages documentés ne s’affichent pas, même si le Runbook est configuré pour enregistrer les informations de commentaires. Pour afficher les messages documentés lors du [test d’un Runbook](automation-testing-runbook.md), vous devez affecter la valeur Continue à la variable $VerbosePreference. Dans ce cas, les messages documentés sont affichés dans le volet de sortie du test dans le portail Azure.

Créez un message documenté à l’aide de l’applet de commande [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) .

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>flux de débogage
Le flux de débogage est destiné à être utilisé avec un utilisateur interactif et ne doit pas être utilisé dans les Runbooks.

## <a name="progress-records"></a>Informations de progression
Si vous configurez un runbook pour enregistrer les informations de progression (sous l’onglet de configuration du runbook, dans le Portail Azure), un enregistrement est consigné dans l’historique des tâches avant et après l’exécution de chaque activité. Afin d’optimiser les performances, il est généralement recommandé de conserver le paramètre par défaut (les informations de progression ne sont pas enregistrées pour le Runbook). Activez cette option uniquement pour résoudre les problèmes liés à un Runbook ou déboguer un Runbook. Lors du test d’un Runbook, les messages de progression ne s’affichent pas, même si le Runbook est configuré pour enregistrer les informations de progression.

La cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) n’est pas valide dans un Runbook, dans la mesure où cette cmdlet est destinée à un utilisateur interactif.

## <a name="preference-variables"></a>Variables de préférence
Windows PowerShell utilise des [variables de préférence](https://technet.microsoft.com/library/hh847796.aspx) pour déterminer comment répondre aux données envoyées aux différents flux de sortie. Vous pouvez définir ces variables dans un Runbook pour contrôler comment il répond aux données envoyées dans différents flux.

Le tableau suivant répertorie les variables de préférence qui peuvent être utilisées dans des Runbooks, ainsi que leurs valeurs valides et valeurs par défaut. Ce tableau inclut uniquement les valeurs valides dans un Runbook. D’autres valeurs sont valides pour les variables de préférence lorsqu’elles sont utilisées dans Windows PowerShell en dehors d’Azure Automation.

| Variable | Valeur par défaut | Valeurs valides |
|:--- |:--- |:--- |
| WarningPreference |Continue |Arrêter<br>Continue<br>SilentlyContinue |
| ErrorActionPreference |Continue |Arrêter<br>Continue<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Arrêter<br>Continue<br>SilentlyContinue |

Le tableau suivant indique le comportement associé aux valeurs des variables de préférence valides dans les Runbooks.

| Valeur | Comportement |
|:--- |:--- |
| Continue |Enregistre le message et poursuit l’exécution du Runbook. |
| SilentlyContinue |Poursuit l’exécution du Runbook sans enregistrer le message. Le message est alors ignoré. |
| Arrêter |Enregistre le message et interrompt le Runbook. |

## <a name="runbook-output"></a>Récupération de la sortie et des messages de Runbook
### <a name="azure-portal"></a>Portail Azure
Vous pouvez afficher les détails d’une tâche de Runbook dans le Portail Azure à partir de l’onglet Tâches d’un Runbook. Le résumé de la tâche affiche les paramètres d’entrée et le [flux de sortie](#output-stream) en plus des informations générales sur la tâche et des exceptions, le cas échéant. L’historique inclut les messages du [flux de sortie](#output-stream) et des [flux d’avertissements et d’erreurs](#warning-and-error-streams) en plus du [flux de commentaires](#verbose-stream) et des [informations de progression](#progress-records) si le Runbook est configuré pour enregistrer les informations de commentaires et de progression.

### <a name="windows-powershell"></a>Windows PowerShell
Dans Windows PowerShell, vous pouvez récupérer la sortie et les messages d’un Runbook à l’aide de l’applet de commande [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput). Cette applet de commande requiert l’ID de la tâche. Elle inclut le paramètre Stream qui vous permet de spécifier le flux à renvoyer. Vous pouvez spécifier la valeur **Any** pour renvoyer tous les flux de la tâche.

Dans l’exemple suivant, un exemple de Runbook est démarré et exécuté. À l’issue de l’exécution du Runbook, son flux de sortie est collecté à partir de la tâche.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>Création graphique
Pour les runbooks graphiques, une journalisation supplémentaire est disponible sous la forme de suivi au niveau de l’activité. Il existe deux niveaux de suivi : de base et détaillé. Dans le suivi de base, vous pouvez voir l’heure de début et de fin de chaque activité dans le runbook, ainsi que des informations relatives à toute nouvelle tentative de l’activité. Par exemple, le nombre de tentatives et l’heure de début de l’activité. Dans le suivi détaillé, vous obtenez le suivi de base, ainsi que des données d’entrée et de sortie pour chaque activité. Actuellement, les enregistrements de suivi sont écrits à l’aide du flux de commentaires détaillé. Vous devez activer la journalisation détaillée lorsque vous activez le suivi. Pour les runbooks graphiques avec le suivi activé, il est inutile de journaliser les informations de progression. Le suivi de base a le même objectif et fournit des informations plus détaillées.

![Vue du flux de travail de création graphique](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

La capture d’écran précédente illustre le fait que lorsque vous activez la journalisation détaillée et le suivi pour les graphiques, vous obtenez beaucoup plus d’informations dans la vue du flux de travail de production. Ces informations supplémentaires peuvent être essentielles pour résoudre les problèmes de production pour un runbook. Il est donc conseillé de l’activer uniquement à cet effet et pas de manière générale. Les enregistrements de suivi peuvent être particulièrement nombreux. Avec le suivi de runbook graphique, vous pouvez obtenir entre deux et quatre enregistrements par activité selon que vous avez configuré le suivi de base ou le suivi détaillé. À moins que vous n’ayez besoin de ces informations pour suivre la progression d’un runbook à des fins de dépannage, vous pouvez choisir de désactiver le suivi.

**Pour activer le suivi au niveau de l’activité, procédez comme suit :**

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Sous **Automatisation de processus**, cliquez sur **Runbooks** pour ouvrir la liste des runbooks.
3. Dans la page Runbooks, cliquez sur un runbook graphique pour le sélectionner dans la liste des runbooks.
4. Sous **paramètres**, cliquez sur **journalisation et le suivi**.
5. Dans la page Journalisation et suivi, sous Journaliser les enregistrements détaillés, cliquez sur **Activé** pour activer la journalisation détaillée. Sous Suivi au niveau de l’activité, définissez le niveau de suivi sur **De base** ou **Détaillé** en fonction du niveau de suivi requis.<br>
   
   ![Page Journalisation et suivi de la création graphique](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Journaux Microsoft Azure Monitor
Automation peut envoyer un état de tâche du runbook et des flux de tâches à votre espace de travail Log Analytics. Avec les journaux Azure Monitor, vous pouvez :

* Obtenir des informations sur vos travaux Automation 
* Déclencher un e-mail ou une alerte basé(e) sur le statut de votre travail de runbook (par exemple, échec ou état suspendu) 
* Écrire des requêtes avancées sur vos flux de travail 
* Mettre en corrélation des travaux sur différents comptes Automation 
* Visualiser l’historique de vos travaux dans le temps    

Pour savoir comment configurer l’intégration aux journaux Azure Monitor dans le but de collecter, de mettre en corrélation et de modifier des données de travail, consultez [Transférer l’état d’un travail et des flux de travail d’Automation vers les journaux Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et d’autres détails techniques, consultez [Suivre une tâche de runbook](automation-runbook-execution.md)
* Pour comprendre comment créer et utiliser des Runbooks enfants, consultez [Runbooks enfants dans Azure Automation](automation-child-runbooks.md)


