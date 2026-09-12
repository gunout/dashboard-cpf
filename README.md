# 🗺️ Dashboard DROM‑COM

![HTML](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![Leaflet](https://img.shields.io/badge/Leaflet-199900?style=for-the-badge&logo=leaflet&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

> **Carte interactive du cadastre des Départements et Régions d'Outre-Mer (DROM)**

Application web monopage (SPA) permettant de visualiser les **communes** et leurs **parcelles cadastrales** pour les 5 DROM : Guadeloupe, Martinique, Guyane, La Réunion et Mayotte. Les parcelles sont colorées selon leur **typologie de propriété** (privé, commune, État, terre coutumière…).

---

## 📋 Table des matières

- [Aperçu](#-aperçu)
- [Fonctionnalités](#-fonctionnalités)
- [Structure des données](#-structure-des-données)
- [Installation](#-installation)
- [Utilisation](#-utilisation)
- [Technologies](#-technologies)
- [Captures d'écran](#-captures-décran)
- [Contribuer](#-contribuer)
- [Licence](#-licence)

---

## 🎯 Aperçu

Ce dashboard cartographique offre une vision complète du cadastre des territoires ultramarins français. Il combine :

- 🗺️ **Deux fonds de carte** : OpenStreetMap + fond cadastral WMS (couche superposable)
- 📍 **Visualisation des communes** (contours GeoJSON) par département
- 🎨 **Coloration des parcelles** selon leur typologie de propriété
- 🎛️ **Double filtre** : département puis commune
- 🔍 **Filtre par typologie** : Privé, Commune, État, Terre coutumière, Mixte, Non renseigné
- 📊 **Compteur dynamique** de parcelles affichées
- 🌐 **Légende interactive** mise à jour automatiquement

---

## ✨ Fonctionnalités

### 🗺️ Carte interactive
- Choix entre **OpenStreetMap** et **fond cadastral WMS** (superposable)
- Affichage des **contours communaux** en pointillés
- Zoom automatique sur le département sélectionné
- Clustering des parcelles (Leaflet.markercluster)

### 🎨 Coloration par typologie
Chaque parcelle est colorée selon sa nature de propriété :

| Typologie | Couleur | Libellé |
|-----------|---------|---------|
| 🏠 PRIVE | 🟢 `#2e7d32` | Privé |
| 🏛️ COMMUNE | 🔵 `#1565c0` | Commune |
| 🏢 ETAT / ETAT FRANCAIS | 🟠 `#f57f17` | État |
| 🌿 TERRE COUTUMIERE | 🟣 `#6a1b9a` | Terre coutumière |
| 🏛️ PUBLIQUE | 🔵 `#1565c0` | Publique |
| 🔀 MIXTE | 🟠 `#f57f17` | Mixte |
| ❓ NON RENSEIGNE | ⚪ `#9e9e9e` | Non renseigné |

> 💡 **Détection automatique** : le script reconnaît plusieurs noms de champs (`nature`, `proprietaire_nature`, `type`). Si aucun n'est présent, le filtre est automatiquement désactivé.

### 🔍 Popups détaillés
Chaque parcelle affiche :
| Champ | Description |
|-------|-------------|
| 🏛️ Commune | Nom de la commune |
| 📍 NIC | Identifiant NIC |
| 📋 Lot | Numéro de lot |
| 📐 Section | Section cadastrale |
| 📏 Surface | Surface (convertie en hectares) |
| 🏷️ Typologie | Badge coloré selon la nature |

### 🏘️ Couverture géographique
Les 5 DROM sont supportés :

| Code | Département | Zoom initial |
|------|-------------|--------------|
| 971 | 🇬🇵 Guadeloupe | 9 |
| 972 | 🇲🇶 Martinique (par défaut) | 9 |
| 973 | 🇬🇫 Guyane | 9 |
| 974 | 🇷🇪 La Réunion | 9 |
| 976 | 🇾🇹 Mayotte | 9 |

---

## 📂 Structure des données

L'application charge dynamiquement des fichiers GeoJSON :

```
📁 repository/
├── index.html
├── README.md
├── LICENSE
├── serve.sh
└── 📁 data/
    ├── communes-971.geojson           # Communes de Guadeloupe
    ├── communes-972.geojson           # Communes de Martinique
    ├── communes-973.geojson           # Communes de Guyane
    ├── communes-974.geojson           # Communes de La Réunion
    ├── communes-976.geojson           # Communes de Mayotte
    ├── 📁 communes_971/
    │   ├── parcelles-97101.json
    │   ├── parcelles-97102.json
    │   └── ...
    ├── 📁 communes_972/
    │   ├── parcelles-97201.json
    │   ├── parcelles-97202.json
    │   └── ...
    ├── 📁 communes_973/
    │   └── ...
    ├── 📁 communes_974/
    │   └── ...
    └── 📁 communes_976/
        └── ...
```

### Convention de nommage

```
data/communes-{dep}.geojson              # Contours communaux du département
data/communes_{dep}/parcelles-{insee}.json  # Parcelles d'une commune
```

Exemple pour la Martinique :
- Communes : `data/communes-972.geojson`
- Parcelles de Fort-de-France (97209) : `data/communes_972/parcelles-97209.json`

### Format attendu des GeoJSON

**Communes** (`communes-{dep}.geojson`) :
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": { "type": "Polygon", "coordinates": [[[-61.0, 14.6], "..."]] },
      "properties": {
        "code_com": "97209",
        "nom_commune": "Fort-de-France"
      }
    }
  ]
}
```

**Parcelles** (`parcelles-{insee}.json`) :
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": { "type": "Polygon", "coordinates": [[[-61.0, 14.6], "..."]] },
      "properties": {
        "commune": "Fort-de-France",
        "nic": "972090000A0123",
        "num_lot": "123",
        "section_cadastrale": "AB",
        "surface_cadastrale": 4500,
        "nature": "PRIVE"
      }
    }
  ]
}
```

### Propriétés reconnues (variantes acceptées)

| Champ affiché | Propriétés reconnues |
|---------------|----------------------|
| Code commune | `code_com`, `code_insee`, `code`, `commune_code` |
| Nom commune | `nom_commune`, `nom`, `NAME`, `commune`, `ville` |
| NIC | `nic`, `id` |
| Lot | `num_lot`, `numero`, `lot` |
| Section | `section_cadastrale`, `section` |
| Surface | `surface_cadastrale`, `contenance` |
| Typologie | `nature`, `proprietaire_nature`, `type` |

---

## 🚀 Installation

### Prérequis
- Un navigateur moderne (Chrome, Firefox, Edge, Safari)
- Un serveur HTTP local pour éviter les restrictions CORS sur `fetch()`

### Installation rapide (script Bash)

```bash
# 1. Cloner le dépôt
git clone https://github.com/votre-utilisateur/dashboard-drom-com.git
cd dashboard-drom-com

# 2. Lancer le serveur local
./serve.sh
```

### Installation manuelle

```bash
# Python 3
python3 -m http.server 8000

# Node.js (avec npx)
npx serve . -l 8000

# PHP
php -S localhost:8000
```

Puis ouvrir : **http://localhost:8000**

---

## 🛠️ Script Bash : `serve.sh`

Script utilitaire pour lancer automatiquement le dashboard avec détection du serveur HTTP disponible.

```bash
#!/usr/bin/env bash
# ================================================================
# 🗺️  Dashboard DROM-COM
# Script de lancement rapide du serveur local
# ================================================================

set -euo pipefail

# ---------- Configuration ----------
PORT="${PORT:-8000}"
HOST="${HOST:-localhost}"

# ---------- Couleurs ----------
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m'

log_info()  { echo -e "${BLUE}ℹ️  $*${NC}"; }
log_ok()    { echo -e "${GREEN}✅ $*${NC}"; }
log_warn()  { echo -e "${YELLOW}⚠️  $*${NC}"; }
log_error() { echo -e "${RED}❌ $*${NC}" >&2; }

# ---------- Vérification des dépendances ----------
check_command() {
  command -v "$1" &>/dev/null
}

# ---------- Détection du serveur HTTP ----------
detect_server() {
  if check_command python3; then
    echo "python3"
  elif check_command python; then
    echo "python"
  elif check_command npx; then
    echo "npx"
  elif check_command php; then
    echo "php"
  else
    echo ""
  fi
}

# ---------- Vérification des données ----------
check_data() {
  if [[ ! -d "data" ]]; then
    log_warn "Dossier 'data/' introuvable."
    log_warn "Ajoutez vos fichiers 'communes-{dep}.geojson' et 'communes_{dep}/parcelles-{insee}.json'."
  else
    local nb_communes nb_parcelles
    nb_communes=$(find data -maxdepth 1 -name "communes-*.geojson" 2>/dev/null | wc -l)
    nb_parcelles=$(find data -name "parcelles-*.json" 2>/dev/null | wc -l)
    log_ok "$nb_communes fichier(s) de communes et $nb_parcelles fichier(s) de parcelles détecté(s)."
  fi
}

# ---------- Lancement du serveur ----------
start_server() {
  local server="$1"
  log_info "Démarrage du serveur sur http://${HOST}:${PORT}"
  log_info "Appuyez sur Ctrl+C pour arrêter."
  echo

  case "$server" in
    python3|python)
      "$server" -m http.server "$PORT" --bind "$HOST"
      ;;
    npx)
      npx --yes serve . -l "$PORT"
      ;;
    php)
      php -S "${HOST}:${PORT}"
      ;;
    *)
      log_error "Aucun serveur HTTP disponible."
      log_info "Ouvrez 'index.html' directement dans votre navigateur."
      exit 1
      ;;
  esac
}

# ---------- Main ----------
main() {
  echo -e "${BLUE}"
  echo "╔══════════════════════════════════════════════════════════╗"
  echo "║  🗺️  Dashboard DROM-COM                                  ║"
  echo "╚══════════════════════════════════════════════════════════╝"
  echo -e "${NC}"

  if [[ ! -f "index.html" ]]; then
    log_error "Fichier 'index.html' introuvable. Exécutez ce script depuis la racine du projet."
    exit 1
  fi

  check_data

  local server
  server="$(detect_server)"
  if [[ -z "$server" ]]; then
    log_error "Aucun serveur HTTP trouvé (Python, Node, PHP)."
    exit 1
  fi
  log_ok "Serveur détecté : $server"

  start_server "$server"
}

main "$@"
```

### Utilisation

```bash
# Rendre exécutable
chmod +x serve.sh

# Lancer sur le port par défaut (8000)
./serve.sh

# Lancer sur un autre port
PORT=3000 ./serve.sh
```

---

## 🎮 Utilisation

1. **Sélectionner un département** dans le premier menu déroulant (Guadeloupe, Martinique, Guyane, La Réunion, Mayotte)
2. Les **contours communaux** apparaissent et la carte zoome automatiquement
3. **Sélectionner une commune** dans le second menu déroulant
4. Les **parcelles** se chargent et s'affichent en clusters colorés
5. **Filtrer par typologie** via le troisième menu (Privé, Commune, État, Terre coutumière…)
6. **Cliquer sur un marqueur** pour voir les détails (NIC, lot, section, surface, typologie)
7. **Basculer entre les fonds de carte** (OSM / Cadastre WMS) via le sélecteur en haut à gauche

---

## 🛠️ Technologies

| Technologie | Usage |
|-------------|-------|
| [Leaflet 1.9.4](https://leafletjs.com/) | Carte interactive |
| [Leaflet.markercluster 1.5.3](https://github.com/Leaflet/Leaflet.markercluster) | Clustering des marqueurs |
| [WMS Cadastre](https://carto.gouv.nc/) | Fond cadastral superposable |
| [OpenStreetMap](https://www.openstreetmap.org/) | Fond de carte principal |

---

## 📸 Captures d'écran

> *Ajoutez ici vos captures d'écran du dashboard*

```
📷 [Capture 1 : Vue de la Martinique avec contours communaux]
📷 [Capture 2 : Zoom sur une commune avec parcelles colorées par typologie]
📷 [Capture 3 : Popup détaillé d'une parcelle avec badge de typologie]
```

---

## 🤝 Contribuer

Les contributions sont les bienvenues !

1. Fork le projet
2. Créer une branche (`git checkout -b feature/amelioration`)
3. Commit les changements (`git commit -m 'Ajout fonctionnalité X'`)
4. Push (`git push origin feature/amelioration`)
5. Ouvrir une Pull Request

### Idées d'amélioration
- [ ] Support des COM (Collectivités d'Outre-Mer) : Nouvelle-Calédonie, Polynésie, Wallis-et-Futuna…
- [ ] Statistiques par commune (nombre de parcelles, surface totale, répartition par typologie)
- [ ] Export CSV / GeoJSON des parcelles filtrées
- [ ] Recherche par NIC ou numéro de lot
- [ ] Mode sombre
- [ ] Affichage des polygones de parcelles (et non plus seulement les centroïdes)
- [ ] Ajout d'un graphique de répartition par typologie

---

## 📄 Licence

Ce projet est distribué sous licence **MIT**. Voir le fichier [LICENSE](LICENSE) pour plus d'informations.

### Fichier `LICENSE` (MIT)

```
MIT License

Copyright (c) 2025 Dashboard DROM-COM

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## 🙏 Remerciements

- [IGN](https://www.ign.fr/) – Données cadastrales (BD Parcellaire)
- [API Cadastre](https://cadastre.data.gouv.fr/) – Données ouvertes
- [Gouv.nc](https://carto.gouv.nc/) – Fond cadastral WMS
- [OpenStreetMap](https://www.openstreetmap.org/) – Fond de carte
- Communauté Leaflet

---

<p align="center">
  <strong>🗺️ Fait avec ❤️ pour les Outre-mer</strong><br>
  <em>Dashboard DROM-COM – 2025</em>
</p>

---


