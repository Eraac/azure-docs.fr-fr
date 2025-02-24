---
title: Exécuter des commandes dans des conteneurs en cours d’exécution dans Azure Container Instances
description: Découvrez comment exécuter une commande dans un conteneur qui est en cours d’exécution dans Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: 577e2386c352798bc21a2c78b22726128ac7cf0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60579744"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Exécuter une commande dans une instance de conteneur Azure en cours d’exécution

Azure Container Instances prend en charge l’exécution d’une commande dans un conteneur en cours d’exécution. L’exécution d’une commande dans un conteneur que vous avez déjà démarré est particulièrement utile pendant le développement d’applications et la résolution de problèmes. L’utilisation la plus courante de cette fonctionnalité consiste à lancer un shell interactif afin de déboguer des problèmes dans un conteneur en cours d’exécution.

## <a name="run-a-command-with-azure-cli"></a>Exécuter une commande avec Azure CLI

Exécutez une commande dans un conteneur en cours d’exécution avec [az container exec][az-container-exec] dans [Azure CLI][azure-cli] :

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Par exemple, pour lancer un shell Bash dans un conteneur Nginx :

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Dans l’exemple ci-dessous, le shell Bash est lancé dans un conteneur Linux en cours d’exécution, fournissant un terminal dans lequel `ls` est exécuté :

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Dans cet exemple, l’invite de commandes est lancée dans un conteneur Nanoserver en cours d’exécution :

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Groupes de plusieurs conteneurs

Si votre [groupe de conteneurs](container-instances-container-groups.md) contient plusieurs conteneurs, comme un conteneur d’applications et une journalisation annexe, spécifiez le nom du conteneur dans lequel exécuter la commande avec `--container-name`.

Par exemple, le groupe de conteneurs *mynginx* contient deux conteneurs, *nginx-app* et *logger*. Pour lancer un shell sur le conteneur *nginx-app* :

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Restrictions

Azure Container Instances prend actuellement en charge le lancement d’un processus unique avec [az container exec][az-container-exec], et vous ne pouvez pas passer des arguments de commande. Par exemple, vous ne pouvez pas chaîner des commandes comme dans `sh -c "echo FOO && echo BAR"`, ni exécuter `echo FOO`.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les autres outils de résolution des problèmes et les problèmes de déploiement courants dans [Résolution des problèmes liés à un conteneur et au déploiement dans les Azure Container Instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure