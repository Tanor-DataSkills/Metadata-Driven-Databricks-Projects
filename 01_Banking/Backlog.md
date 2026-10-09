 


# 🎯 Sprint 1 : Socle technique, Métadonnées & Governance

## User Stories & Tasks

**✅ US 1.1 : Modélisation et création de la base de métadonnées (SQL Server / Azure SQL DB)**

Créer les tables de configuration : pipeline_config (source, destination, format, clé primaire, fréquence, statut d'activation) et pipeline_audit (start_time, end_time, rows_read, rows_written, status, error_message).   

Insérer les configurations initiales pour les 6 tables (branches, customers, accounts, transactions, credit_bureau_report, payment_gateway_logs).   

**✅ US 1.2 : Configuration de Unity Catalog & volumes Databricks**
Créer les catalogues/schémas dans Unity Catalog (bronze, silver, gold) et gérer les droits d'accès.   
Créer le Volume Unity Catalog pour accueillir les fichiers credit_bureau_report et payment_gateway_logs déposés manuellement.   

**✅ US 1.3 : Connexions réseau & Secrets Management**
Configurer les crédentiels JDBC dans Databricks Secret Scope pour la connexion sécurisée à SQL Server.


# 🎯 Sprint 2 : Couche Bronze (Ingestion générique Metadata-driven)

## User Stories & Tasks

**✅ US 2.1 : Moteur d'ingestion générique SQL Server (JDBC & Spark)**
Développer un notebook Spark paramétrable (lisant la table de métadonnées) pour ingérer les tables branches, customers, accounts, transactions en mode Full/Incremental via JDBC.   

Écrire les données brutes au format Delta Lake dans la couche Bronze (ex: bronze.customers) en conservant le schéma d'origine + métadonnées d'ingestion (_ingested_at, _source_file_or_table).  

**✅ US 2.2 : Moteur d'ingestion générique des fichiers (Auto Loader sur Volume Databricks)**
Développer un notebook Spark utilisant Auto Loader (cloudFiles) paramétré par les métadonnées pour ingérer automatiquement les fichiers déposés dans le Volume Databricks (credit_bureau_report, payment_gateway_logs).   

Écrire les flux dans la couche Bronze au format Delta Lake.   

**✅ US 2.3 : Intégration de l'Audit & Logging**
Capturer le nombre de lignes lues/écrites et logger le statut (SUCCESS / FAILED) dans la table pipeline_audit à la fin de chaque ingestion.   

# 🎯 Sprint 3 : Couche Silver (Nettoyage, Validation & Jointures)

## User Stories & Tasks

**✅ US 3.1 : Framework de règles de qualité & dédoublonnage (Metadata-driven)**

Développer un notebook de transformation Silver générique ou spécifique appliquant le dédoublonnage (ex: sur customer_id ou transaction_id), le typage des colonnes et la gestion des valeurs nulles.

**✅ US 3.2 : Traitement des données bancaires transactionnelles & partenaires**
Nettoyer et standardiser la table silver.customers et y intégrer/joindre les données nettoyées du rapport de bureau de crédit (credit_bureau_report).   Nettoyer la table silver.transactions et joindre les logs de la passerelle de paiement (payment_gateway_logs) pour enrichir le statut des transactions.

Standardiser les tables de référence silver.branches et silver.accounts.   

# 🎯 Sprint 4 : Couche Gold & Restitution (Analytics & AI)

## User Stories & Tasks

**✅ US 4.1 : Modélisation décisionnelle Gold (Star Schema)**
Créer les dimensions : dim_customers (profil + bureau de crédit), dim_accounts, dim_branches.
Créer la table de faits : fact_transactions (enrichie des logs de paiement, agrégations montants/frais).

**✅ US 4.2 : Création des Databricks Dashboards**
Concevoir un tableau de bord exécutif : volume des transactions par agence, profil de risque client (credit score), taux d'échec des paiements en ligne.   

**✅ US 4.3 : Activation de Databricks Genie**
Configurer l'espace Databricks Genie sur les tables du schéma Gold avec la documentation/descriptions des colonnes sous Unity Catalog pour permettre les requêtes métiers en langage naturel.   

# 🎯 Sprint 5 : Orchestration, Alerte & Industrialisation

## User Stories & Tasks

**✅ US 5.1 : Orchestration dynamique avec Databricks Workflows (Jobs)**
Créer un Databricks Job qui lit la configuration, orchestre l'exécution séquentielle/parallèle des notebooks (Bronze -> Silver -> Gold).   

**✅ US 5.2 : Système d'alertes par Email personnalisées**
Intégrer un module de notification par email (via SMTP ou service cloud) en cas d'échec d'un Job ou de non-respect des critères de qualité de données.   
**✅ US 5.3 : Tests d'intégration de bout en bout & Documentation**
Effectuer un test de bout en bout (injection de données SQL Server + dépôt de fichier dans le Volume Databricks -> Ingestion -> Silver -> Gold -> Dashboard/Genie).   
