# Migration is_kmymoney18 → is_kmymoney20

## Déjà fait

- Suppression du code mis en commentaire (py, xml, css).
- Modèles : `ir.model.access.csv` → `ir.access.csv`, `_sql_constraints` → `models.Constraint`, `self._context` → `self.env.context`, `fields.datetime.now()` → `fields.Datetime.now()`, `env.ref('is_kmymoney18.…')` → `is_kmymoney20`.
- Vues : réactivation, `button_box` et groupement de recherche au format v20, `many2one_clickable` → `widget="many2one"`.
- Suppression de `graph_no_legend.js` (non chargé, obsolète en v20) et des classes `o_graph_no_legend` / `o_graph_load_all`.

## Reste à faire

### Bugs latents (déjà présents en v18)

1. [x] **`_get_post_date` plante hors d'un compte** (`models/is_kmymoney.py`) : lit `self.env.context["active_id"]` → `KeyError` si on crée une opération depuis le menu « Opérations ». Utiliser `.get("active_id")` avec un repli sur la date du jour.
2. [x] **`jour` dépend de la langue du serveur** (`models/is_suivi_sante.py`) : `strftime('%A')` renvoie « lundi » uniquement si le serveur est en `fr_FR` ; en anglais « Monday » → valeur hors sélection. Calculer avec `obj.name.weekday()` et `_JOURS`.
3. [x] **`return` dans la boucle de `_set_debit` / `_set_credit`** (`models/is_kmymoney.py`) : en édition multiple, les lignes suivant un montant à 0 ne sont pas traitées. Remplacer par `continue`.

### Nettoyage

4. [x] **`print()` de debug** dans `res_company.maj_objectifs` (`models/res_company.py`).
5. [x] **Imports inutilisés et préfixes `u''`** (`timedelta`, `api`, `_` dans les rapports…).
6. [x] **Description fausse du modèle `is.kmymoney.report`** : « Suivi du temps par activité » (copier-coller).
7. [x] **Manifest et traductions** : description répétée (« Module Odoo 20 Module Odoo kMyMoney ») ; `i18n/fr.po` vide (à supprimer ou regénérer).
8. [x] **`DOCUMENTATION.md` à jour** : parle encore d'Odoo 18, de `ir.model.access.csv` et du script JS des graphiques.

### À savoir

9. [x] **`parent_id=181` en dur** dans `_solde_all_account` (`models/is_kmymoney.py`) : 181 = compte racine « Actif » de la base v18, dont les 31 enfants sont exactement les comptes avec une institution. Remplacé par `institution_id is not null` (même critère que l'action Comptes et les rapports) → plus de dépendance aux ids. Calcul fait une seule fois au lieu d'une fois par compte.
10. [ ] **Soldes calculés en une requête SQL par ligne** (`bal_solde`, `nb`, `solde`) : pas bloquant pour ce volume.
    → **Non traité**. Mesures sur la base v18 (09/2026) : 15 357 opérations, 21 comptes bancaires actifs, 533 opérations par compte en moyenne.
    - Liste des comptes : moins de 1 ms par compte, négligeable.
    - Liste des opérations d'un compte : 2 requêtes par ligne pour `solde`, moins de 0,3 ms sur un compte moyen.
    - ⚠ **AXA Compte Courant** (12 839 opérations, le compte le plus utilisé) : environ 7 ms par ligne, soit **environ 0,6 s par page de 80 lignes**. C'était déjà le cas en v18.
    - Solution si cela devient gênant : calculer le cumul en une seule requête par page (fonction de fenêtre SQL). Risque : on touche au calcul du solde.

### Corrigé en cours de recette

11. [x] **`can't adapt type 'NewId'`** sur `_nb` (et `_bal_solde`, `solde`) : les calculs SQL recevaient l'id temporaire d'un enregistrement pas encore créé (bouton « Nouveau », nouvelle ligne de liste éditable). Les requêtes utilisent `obj._origin.id` (id réel, aussi pendant l'édition d'un enregistrement existant) ; s'il est vide (enregistrement pas encore créé) → valeur 0. `_solde` affecte aussi 0 par défaut (il ne mettait rien hors d'un compte).
12. [x] **Icônes des boutons invisibles** : en v20, `icon="…"` attend un nom Material Symbols (plus de Font Awesome). `fa-list` → `format_list_bulleted`, `fa-bar-chart` → `bar_chart`, `fa-search-plus` → `zoom_in`, `fa-check` → `check`.
