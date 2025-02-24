---
title: Format des journaux d’audit SQL Database | Microsoft Docs
description: Comprenez comment les journaux d’audit SQL Database sont structurés.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61417386"
---
# <a name="sql-database-audit-log-format"></a>Format des journaux d’audit SQL Database

L’[audit Azure SQL Database](sql-database-auditing.md) permet de suivre les événements de base de données et de les écrire dans un journal d’audit dans votre compte de stockage Azure, ou de les envoyer à Event Hub ou Log Analytics à des fins de traitement et d’analyse en aval.

## <a name="naming-conventions"></a>Conventions de nommage

### <a name="blob-audit"></a>Audit d’objets blob

Les journaux d’audit stockés dans Stockage Blob le sont dans un conteneur nommé `sqldbauditlogs` dans le compte de stockage Azure. La hiérarchie des répertoires au sein du conteneur prend la forme `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. Le format du nom de fichier des objets blob est `<CreationTime>_<FileNumberInSession>.xel`, où `CreationTime` est au format UTC `hh_mm_ss_ms` et `FileNumberInSession` est un index en cours d’exécution au cas où les journaux de la session s’étendent sur plusieurs fichiers d’objets blob.

Par exemple, pour la base de données `Database1` sur `Server1`, un chemin valide possible est le suivant :

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Event Hub

Les événements d’audit sont écrits dans l’espace de noms et le hub d’événements définis pendant la configuration de l’audit. Ils sont capturés dans le corps des événements [Apache Avro](https://avro.apache.org/) et stockés au format JSON avec encodage UTF-8. Pour lire les journaux d’audit, vous pouvez utiliser les [outils Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) ou des outils similaires qui traitent ce format.

### <a name="log-analytics"></a>Log Analytics

Les événements d’audit sont écrits dans l’espace de travail Log Analytics défini pendant la configuration de l’audit, dans la table `AzureDiagnostics` avec la catégorie `SQLSecurityAuditEvents`. Pour plus d’informations sur le langage et les commandes de recherche Log Analytics, consultez [Référence sur les recherches Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Champs de journal d’audit

| Nom (objet blob) | Nom (Event Hubs/Log Analytics) | Description | Type d’objet blob | Type Event Hubs/Log Analytics |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID de l’action | varchar(4) | chaîne |
| action_name | action_name_s | Nom de l’action | N/A | chaîne |
| additional_information | additional_information_s | Éventuelles informations supplémentaires sur l’événement, stockées au format XML | nvarchar(4000) | chaîne |
| affected_rows | affected_rows_d | Nombre de lignes affectées par la requête | bigint | int |
| application_name | application_name_s| Nom de l’application cliente | nvarchar(128) | chaîne |
| audit_schema_version | audit_schema_version_d | Toujours 1 | int | int |
| class_type | class_type_s | Type d’entité pouvant être auditée sur laquelle se produit l’audit | varchar(2) | chaîne |
| class_type_desc | class_type_description_s | Description de l’entité pouvant être auditée sur laquelle se produit l’audit | N/A | chaîne |
| client_ip | client_ip_s | Adresse IP source de l’application cliente | nvarchar(128) | chaîne |
| connection_id | N/A | ID de la connexion dans le serveur | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | Types des informations et étiquettes de sensibilité renvoyées par la requête auditée, en fonction des colonnes classifiées dans la base de données. En savoir plus sur [la découverte et la classification des données Azure SQL Database](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | chaîne |
| database_name | database_name_s | Contexte de base de données dans lequel l’action s’est produite | sysname | chaîne |
| database_principal_id | database_principal_id_d | ID du contexte d’utilisateur de base de données dans lequel l’action est effectuée | int | int |
| database_principal_name | database_principal_name_s | Nom du contexte d’utilisateur de base de données dans lequel l’action est effectuée | sysname | chaîne |
| duration_milliseconds | duration_milliseconds_d | Durée d’exécution de la requête en millisecondes | bigint | int |
| event_time | event_time_t | Date et heure du déclenchement de l’action pouvant être auditée | datetime2 | Datetime |
| host_name | N/A | Nom d’hôte du client | chaîne | N/A |
| is_column_permission | is_column_permission_s | Indicateur précisant s’il s’agit d’une autorisation au niveau de la colonne. 1 = true, 0 = false | bit | chaîne |
| N/A | is_server_level_audit_s | Indicateur précisant si cet audit se situe au niveau du serveur | N/A | chaîne |
| object_ id | object_id_d | ID de l’entité sur laquelle l’audit s’est produit. Sont inclus les objets de serveur, les bases de données, les objets de base de données et les objets de schéma. 0 si l’entité est le serveur lui-même ou si l’audit n’est pas effectué au niveau d’un objet | int | int |
| object_name | object_name_s | Nom de l’entité sur laquelle l’audit s’est produit. Sont inclus les objets de serveur, les bases de données, les objets de base de données et les objets de schéma. 0 si l’entité est le serveur lui-même ou si l’audit n’est pas effectué au niveau d’un objet | sysname | chaîne |
| permission_bitmask | permission_bitmask_s | Si applicable, présente les autorisations qui ont été octroyées, refusées ou révoquées | varbinary(16) | chaîne |
| response_rows | response_rows_d | Nombre de lignes renvoyées dans le jeu de résultats | bigint | int |
| schema_name | schema_name_s | Contexte du schéma dans lequel l’action s’est produite. NULL pour les audits qui se produisent en dehors d’un schéma | sysname | chaîne |
| N/A | securable_class_type_s | Objet sécurisable mappé au class_type audité | N/A | chaîne |
| sequence_group_id | sequence_group_id_g | Identificateur unique | varbinary | GUID |
| sequence_number | sequence_number_d | Effectue le suivi de la séquence des enregistrements au sein d’un enregistrement d’audit unique qui était trop volumineux pour tenir dans le tampon d’écriture des audits | int | int |
| server_instance_name | server_instance_name_s | Nom de l’instance du serveur où l’audit s’est produit | sysname | chaîne |
| server_principal_id | server_principal_id_d | ID du contexte de connexion dans lequel l’action est effectuée | int | int |
| server_principal_name | server_principal_name_s | Connexion actuelle | sysname | chaîne |
| server_principal_sid | server_principal_sid_s | SID de la connexion actuelle | varbinary | chaîne |
| session_id | session_id_d | ID de la session sur laquelle l’événement s’est produit | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Principal de serveur pour la session | sysname | chaîne |
| statement | statement_s | Instruction T-SQL qui a été exécutée (le cas échéant) | nvarchar(4000) | chaîne |
| succeeded | succeeded_s | Indique si l’action qui a déclenché l’événement a réussi. Pour les événements autres que la connexion et le traitement par lot, ce champ indique uniquement si la vérification des autorisations a réussi ou échoué, et non l’opération. 1 = succès, 0 = échec | bit | chaîne |
| target_database_principal_id | target_database_principal_id_d | Principal de base de données sur lequel l’opération GRANT/DENY/REVOKE est effectuée. 0 si non applicable | int | int |
| target_database_principal_name | target_database_principal_name_s | Utilisateur cible de l’action. NULL si non applicable | chaîne | chaîne |
| target_server_principal_id | target_server_principal_id_d | Principal de serveur sur lequel l’opération GRANT/DENY/REVOKE est effectuée. Retourne 0 si non applicable | int | int |
| target_server_principal_name | target_server_principal_name_s | Connexion cible de l’action. NULL si non applicable | sysname | chaîne |
| target_server_principal_sid | target_server_principal_sid_s | SID de la connexion cible. NULL si non applicable | varbinary | chaîne |
| transaction_id | transaction_id_d | SQL Server uniquement (à partir de la version 2016) - 0 pour Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Id d’événement défini par l’utilisateur transmis en tant qu’argument à sp_audit_write. NULL pour les événements système (valeur par défaut) et différent de zéro pour un événement défini par l’utilisateur. Pour plus d’informations, consultez [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql). | smallint | int |
| user_defined_information | user_defined_information_s | Informations définies par l’utilisateur transmises en tant qu’argument à sp_audit_write. NULL pour les événements système (valeur par défaut) et différent de zéro pour un événement défini par l’utilisateur. Pour plus d’informations, consultez [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql). | nvarchar(4000) | chaîne |

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur l’[audit Azure SQL Database](sql-database-auditing.md).