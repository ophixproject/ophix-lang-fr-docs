---
title: Système de documentation
slug: ophix-docs
order: 1
section: Extensions
---

`ophix-docs` est un plugin optionnel qui ajoute une documentation inline à l'administration Django. Chaque application installée peut fournir sa propre documentation Markdown ; une commande de gestion importe tout dans la base de données où elle est affichée dans l'interface d'administration avec coloration syntaxique.

---

## Installation

```bash
pip install ophix-docs
ophix-manage migrate
```

Une fois installée et migrée, une section **Documentation** apparaît dans l'administration.

---

## Import de la documentation

La commande `ophix_docs_update` importe les fichiers Markdown dans la base de données :

```bash
ophix-manage ophix_docs_update
```

Par défaut, elle lit depuis le chemin défini dans `OPHIX_DOCS_PATH` (par défaut un dossier `docs/` à côté de la racine du projet). Pour importer également les docs livrées avec les applications installées :

```bash
# Sur un serveur d'identifiants :
ophix-manage ophix_docs_update \
  --include-app-docs ophix.core,ophix_creds,ophix_docs,ophix_theme_tools
```

`ophix_docs_update` est **additif uniquement** — il crée et met à jour les pages, mais ne les supprime jamais. Pour supprimer des pages, utilisez `ophix_docs_purge`.

---

## Suppression de la documentation

### Supprimer des pages spécifiques par slug

```bash
ophix-manage ophix_docs_purge mon-slug
```

### Supprimer toutes les pages

```bash
ophix-manage ophix_docs_purge --all
```

### Supprimer les pages dont les fichiers source ont été supprimés

```bash
ophix-manage ophix_docs_purge --deleted
```

Tous les modes supportent `--dry-run` :

```bash
ophix-manage ophix_docs_purge --deleted --dry-run
```

---

## Format des fichiers Markdown

Chaque page de documentation est un fichier `.md` avec un bloc de métadonnées YAML :

```text
---
title: Titre de ma page
slug: mon-slug
order: 10
section: Ma section
---

## Ma première section

Le contenu va ici...
```

### Champs des métadonnées

| Champ | Requis | Description |
| --- | --- | --- |
| `title` | Oui | Nom d'affichage dans la navigation de l'administration |
| `slug` | Oui | Identifiant unique — doit être unique pour tous les docs importés |
| `order` | Oui | Position de tri dans la section |
| `section` | Oui | Nom de la section à laquelle appartient cette page |

---

## Sections

Les sections regroupent les pages associées dans la navigation. Chaque application qui contribue des docs devrait inclure un fichier `sections.yaml` :

```yaml
sections:
  - name: Ma Section
    collapsed: false
```

---

## Documentation personnalisée de l'opérateur

Les opérateurs peuvent ajouter leurs propres pages de documentation. Configurez le chemin dans `.env` :

```ini
OPHIX_DOCS_PATH=/home/websites/credserver/docs
```

Créez le répertoire et ajoutez des fichiers Markdown avec le même format de métadonnées. L'import inclut toujours le chemin principal — aucune option supplémentaire n'est nécessaire.

---

## Export de la documentation

```bash
ophix-manage ophix_docs_export mon-slug --output ./docs/
```

---

## Lister les sources de docs

```bash
ophix-manage ophix_docs_list_sources
```

---

## Paramètres

| Paramètre | Défaut | Description |
| --- | --- | --- |
| `OPHIX_DOCS_ENABLED` | `true` | Définir à `false` pour masquer la section Documentation dans l'administration |
| `OPHIX_DOCS_PATH` | `BASE_DIR/docs` | Chemin vers le dossier de docs principal |
| `OPHIX_DOCS_APP_LABEL` | `Documentation` | Étiquette de section de l'administration |
