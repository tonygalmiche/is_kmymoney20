# Module Odoo 18 kMyMoney

## Description
Ce module permet de gérer les comptes bancaires et les opérations financières dans Odoo 18, en s'intégrant avec le logiciel kMyMoney.

## Structure du module

### Modèles principaux

#### kmn_account_type
- **Description** : Types de comptes
- **Champs** :
  - `name` : Nom du type de compte

#### kmn_accounts
- **Description** : Comptes bancaires
- **Champs** :
  - `name` : Nom du compte
  - `institution_id` : Institution bancaire
  - `parent_id` : Compte parent
  - `account_number` : Numéro de compte
  - `account_type_id` : Type de compte
  - `bal_solde` : Solde du compte (calculé)
  - `last_post_date` : Date du dernier mouvement
  - `active` : Compte actif ou non
  - `solde_all_account` : Solde de tous les comptes (calculé)
  - `percent_all_account` : Pourcentage du solde sur ce compte (calculé)
  - `nb` : Nombre d'opérations (calculé)

#### kmn_account_move
- **Description** : Opérations bancaires
- **Champs** :
  - `payee_id` : Tiers
  - `reconcile_date` : Date de rapprochement
  - `action` : Action
  - `reconcile_flag` : Flag de rapprochement
  - `value` : Montant
  - `debit` : Débit (calculé)
  - `credit` : Crédit (calculé)
  - `solde` : Solde (calculé)
  - `memo` : Note
  - `account1_id` : Compte 1
  - `account2_id` : Compte 2
  - `account_id` : Compte (calculé)
  - `check_number` : Numéro de chèque
  - `post_date` : Date de l'opération
  - `date_creation` : Date de création
  - `date_modification` : Date de modification
  - `state` : État (Brouillon/Validé)

## Fonctionnalités principales

- Gestion des types de comptes
- Gestion des comptes bancaires
- Enregistrement des opérations bancaires
- Calcul automatique des soldes
- Rapports financiers
- Intégration avec les partenaires Odoo

## Vues principales

- Liste des comptes
- Formulaire de compte
- Liste des opérations
- Formulaire d'opération
- Vue pivot des opérations

## Sécurité

- Gestion des droits d'accès via `ir.model.access.csv`

## Assets

- Feuille de style CSS
- Script JavaScript pour les graphiques

## Dépendances

- Module `base` d'Odoo