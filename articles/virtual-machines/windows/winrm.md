---
title: Configurer l’accès WinRM pour une machine virtuelle Azure | Microsoft Docs
description: Configurez l’accès WinRM à utiliser avec une machine virtuelle Azure créée à l’aide du modèle de déploiement Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 2a106c639eb72d3793b0df8f4ddf36a4724f7418
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707876"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configuration de l’accès WinRM pour les machines virtuelles dans Azure Resource Manager

Voici les étapes à suivre pour configurer une machine virtuelle avec une connectivité WinRM

1. Créer un coffre de clés
2. Créer un certificat auto-signé
3. Charger votre certificat auto-signé dans Key Vault
4. Obtenir l’URL de votre certificat auto-signé dans le coffre de clés
5. Référencer les URL de vos certificats auto-signés lors de la création d’une machine virtuelle

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-create-a-key-vault"></a>Étape 1 : Créer un coffre de clés
Vous pouvez utiliser la commande ci-dessous pour créer le coffre de clés

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Étape 2 : Créer un certificat auto-signé
Vous pouvez créer un certificat auto-signé à l’aide de ce script PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Étape 3 : Charger votre certificat auto-signé dans Key Vault
Avant de charger le certificat dans le coffre de clés créé à l’étape 1, vous devez le convertir dans un format que le fournisseur de ressources Microsoft.Compute comprend. Le script PowerShell ci-dessous vous permettra de le faire

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Étape 4 : Obtenir l’URL de votre certificat auto-signé dans le coffre de clés
Le fournisseur de ressources Microsoft.Compute a besoin de l’URL de la clé secrète dans le coffre de clés lors de l’approvisionnement de la machine virtuelle. Ainsi, le fournisseur de ressources Microsoft.Compute peut télécharger la clé secrète et créer le certificat équivalent sur la machine virtuelle.

> [!NOTE]
> L’URL de la clé secrète doit également inclure la version. Un exemple d’URL se présente comme suit https:\//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Modèles
Vous pouvez obtenir le lien vers l’URL dans le modèle à l’aide du code ci-dessous

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Vous pouvez obtenir cette URL à l’aide de la commande PowerShell ci-dessous

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Étape 5 : Référencer les URL de vos certificats auto-signés lors de la création d’une machine virtuelle
#### <a name="azure-resource-manager-templates"></a>Modèles Azure Resource Manager
Lorsque vous créez une machine virtuelle par le biais de modèles, le certificat est référencé dans la section des clés secrètes et la section winRM comme suit :

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Vous trouverez un exemple de modèle pour machine virtuelle dans [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Pour obtenir le code source pour ce modèle, consultez [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Étape 6 : Se connecter à la machine virtuelle
Avant de vous connecter à la machine virtuelle, vous devrez vous assurer que votre machine est configurée pour la gestion à distance WinRM. Démarrez PowerShell en tant qu’administrateur et exécutez la commande ci-dessous pour vérifier que l’installation est terminée.

    Enable-PSRemoting -Force

> [!NOTE]
> Vous devrez peut-être vous assurer que le service WinRM est en cours d’exécution si la commande ci-dessus ne fonctionne pas. Vous pouvez le faire à l’aide de `Get-Service WinRM`
> 
> 

Une fois l’installation terminée, vous pouvez vous connecter à la machine virtuelle à l’aide de la commande ci-dessous

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
