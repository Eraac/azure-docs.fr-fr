---
title: Schéma de rôle Azure Cloud Services | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 481301333ada39297bf2813bbea5f096c2abd3ad
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360660"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schéma de rôle de configuration d’Azure Cloud Services

L’élément `Role` du fichier de configuration de service spécifie le nombre d’instances de rôle à déployer pour chaque rôle dans le service, les valeurs des paramètres de configuration et les empreintes des certificats associés à un rôle.

Pour en savoir plus sur le schéma de configuration de service Azure, consultez la section [Schéma de configuration de services cloud (classique)](schema-cscfg-file.md). Pour en savoir plus sur le schéma de définition de service Azure, consultez la section relative au [schéma de définition de service cloud (classique)](schema-csdef-file.md).

##  <a name="Role"></a> Élément de rôle
L’exemple suivant illustre l’élément `Role` et ses éléments enfants.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

La table suivante décrit les attributs de l’élément `Role`.

| Attribut | Description |
| --------- | ----------- |
| Nom   | Requis. Indique le nom du rôle. Il doit correspondre au nom spécifié pour le rôle dans le fichier de définition de service.|
| vmName | facultatif. Indique le nom DNS d’une machine virtuelle. Ce nom ne peut pas comprendre plus de 10 caractères.|

La table suivante décrit les éléments enfants de l’élément `Role`.

| Élément | Description |
| ------- | ----------- |
| Instances | Requis. Spécifie le nombre d’instances à déployer pour le rôle. Ce nombre est défini par un entier pour l’attribut `count`.|
| Paramètre   | facultatif. Spécifie un nom de paramètre et une valeur dans une collection de paramètres pour un rôle. Le nom du paramètre est défini par une chaîne pour l’attribut `name`, et la valeur du paramètre est définie par une chaîne pour l’attribut `value`.|
| Certificat | facultatif. Spécifie le nom, l’empreinte et l’algorithme d’un certificat de service qui doit être associé au rôle. Le nom du certificat est défini par une chaîne pour l’attribut `name`. L’empreinte de certificat est défini par une chaîne de caractères hexadécimaux ne comportant aucun espace pour l’attribut `thumbprint`. Les caractères hexadécimaux doivent être représentés à l’aide des chiffres et de caractères alphanumériques en majuscules. L’algorithme du certificat est défini par une chaîne pour l’attribut `thumbprintAlgorithm`.|

## <a name="see-also"></a>Voir aussi
[Schéma de configuration de services cloud (classique)](schema-cscfg-file.md)