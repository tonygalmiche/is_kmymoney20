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
8. [ ] **`DOCUMENTATION.md` à jour** : parle encore d'Odoo 18, de `ir.model.access.csv` et du script JS des graphiques.

### À savoir

9. [ ] **`parent_id=181` en dur** dans `_solde_all_account` (`models/is_kmymoney.py`) : id du compte parent de la base v18. Correct si les scripts de migration conservent les ids de `kmn_accounts` ; sinon le « Part sur ce compte » sera faux.
10. [ ] **Soldes calculés en une requête SQL par ligne** (`bal_solde`, `nb`, `solde`) : pas bloquant pour ce volume.

### Corrigé en cours de recette

11. [x] **`can't adapt type 'NewId'`** sur `_nb` (et `_bal_solde`, `solde`) : les calculs SQL recevaient l'id temporaire d'un enregistrement pas encore créé (bouton « Nouveau », nouvelle ligne de liste éditable). Les requêtes utilisent `obj._origin.id` (id réel, aussi pendant l'édition d'un enregistrement existant) ; s'il est vide (enregistrement pas encore créé) → valeur 0. `_solde` affecte aussi 0 par défaut (il ne mettait rien hors d'un compte).
12. [x] **Icônes des boutons invisibles** : en v20, `icon="…"` attend un nom Material Symbols (plus de Font Awesome). `fa-list` → `format_list_bulleted`, `fa-bar-chart` → `bar_chart`, `fa-search-plus` → `zoom_in`, `fa-check` → `check`.
