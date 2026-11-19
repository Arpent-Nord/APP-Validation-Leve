# Cahier de l'application — Validation de levé (APP-Validation-Leve)

> Cahier maintenu avec le code (même commit). Il est intégré automatiquement au PDF
> `00_CAHIER.pdf` à chaque sauvegarde autonome des applications.
> Règle du dépôt public : aucune donnée de client, de projet, de coordonnées de chantier,
> de nom d'employé, de mot de passe ou de clé dans ce fichier ni dans le code.

## 1. À quoi sert l'application

- **Problème réglé :** un levé de terrain arrive souvent avec des codes mal saisis, des liaisons de lignes incomplètes, des numéros hors standard ou des hauteurs d'antenne erronées. L'application permet à l'arpenteur de **lire n'importe quel fichier de terrain, le valider, le corriger lui-même** et de produire un fichier conforme pour Civil 3D, sans passer par Civil 3D / Survey Database.
- **Pour qui :** arpenteurs et techniciens de terrain, chefs arpenteurs, dessinateurs et intégrateurs de données qui reçoivent le levé.
- **Contexte :** application Web (navigateur) sur ordinateur, tablette ou téléphone ; fonctionne **hors ligne** une fois ouverte. Les fichiers restent sur l'appareil : **rien n'est envoyé sur Internet**.
- **Sorties principales :** `<levé>_modif.txt` (P,N,E,Z,D pour Civil 3D), `<levé>.dxf`, `<levé>_rapport.html` (rapport de levé imprimable / PDF), journal des corrections, fichier de séance.

## 2. Utilisation pas à pas

1. **Ouvrir** https://arpent-nord.github.io/APP-Validation-Leve/ (ou la carte « Validation de levé » de la Boîte à outils https://arpent-nord.github.io/). Sur téléphone, « Ajouter à l'écran d'accueil » pour l'installer.
2. **(Ordinateur, facultatif)** « Dossier de travail… » : choisir le dossier du projet. Les exports et la séance y sont alors enregistrés directement.
3. **Ouvrir un levé…** (ou « Coller »). Le format est détecté automatiquement (voir section 3).
4. **Lire les anomalies** (onglet Anomalies) : erreurs (rouge), avertissements, notes. Toucher une anomalie cadre le point sur le plan ; la plupart proposent un bouton de correction.
5. **Corriger** avec la barre d'outils :
   - Codes hors standard → **CODES** (association aux codes ArpentNord, mémoire par arpenteur).
   - Lignes → **INV PTS, INV LIGNE, FERMER, LIER 2 PTS** (continu, code de liaison au choix), **DÉLIER, UNIR, EFFACER LIAISONS** (tout le levé, un code, ou les lignes touchées sur le plan). LIER 2 PTS suit l'ordre où l'on touche les points : si cet ordre ne suit pas les numéros (point oublié levé plus tard), l'app propose à la fin de la ligne de redistribuer les numéros de ces points dans l'ordre de la ligne (aperçu, Renuméroter / Garder les numéros).
   - Points calculés → **RECT, 2P, 2PD, 2D, 3PP, 4P, 3C**.
   - **HAUT. ANT.** (hauteur d'antenne / prisme), **NUMÉROTER** (standard ArpentNord), effacer / rétablir un point (fiche du point).
   - **Annuler**, **Historique** (annuler une correction précise).
6. **ANALYSE** (facultatif) : analyse d'un fossé entre deux points (eau accumulée, débordement, sens et pente d'écoulement) → « Ajouter au rapport ».
7. **Fond** (facultatif) : orthophoto du projet (image + fichier de référence), **dossier de tuiles** (orthos lourdes découpées par `DECOUPER_ORTHOS.bat` : l'app charge seule les tuiles autour du levé, l'ortho la plus récente à égalité ; ajout des tuiles de la vue ou d'une tuile autour ; dossier retenu sur l'appareil) ou imagerie en ligne.
8. **Rapport** : **Points d'intérêt…** (points choisis par code ou sur le plan, étiquettes et symboles, point de référence avec désignation et symbole, distances cochées entre les points (1–2, 1–3…), autres cotes entre deux points ou d'un point à une ligne de deux points ; symboles des bibliothèques publiques Temaki et Maki (CC0) ; section en tête du rapport avec vue, coordonnées et distances ; exports CSV et DXF de ces points), **sections affichées / cachées** (case devant chaque titre dans l'aperçu ; préréglages « Rapport client » / « Rapport complet »), titre et commentaires modifiables (repris au début du rapport et gardés dans la séance), aperçu, **calibration de site Emlid** (coller le texte des pages Flow 360 « Informations sur le système de coordonnées » et « Points de localisation »), ajout de photos du terrain (légende et taille au rapport pour chacune : petite, moyenne, pleine largeur), « Enregistrer _rapport.html » ou « Imprimer / PDF ».
9. **Exporter _modif.txt** et **Exporter DXF** ; **Envoyer par Outlook…** au besoin.
10. **Sauvegarder la séance** pour reprendre plus tard (ou sur un autre poste) : le fichier `_seance.json` se rouvre avec « Ouvrir un levé… ». Une sauvegarde automatique est aussi gardée dans le navigateur (5 derniers levés).

Un guide illustré est disponible : `guide.html` (bouton Documentation de la Boîte à outils).

## 3. Données en entrée et en sortie

| Élément | Format | Détails |
|---|---|---|
| Levé texte ArpentNord / Leica | `P,N,E,Z,D` ou `P,E,N,Z,D` (virgule, point-virgule, tabulation ou espace) | ordre N/E détecté sur les valeurs ; lignes `#` (PROJET, DATE, OPÉRATEUR) lues |
| Levé Emlid Flow | CSV « tout sélectionner » (Code + Description + qualité GNSS) | origine **Local** = point connu : monument (no du type 312371A ou code monument / repère) → « Points connus contrôlés » ; sinon point de conception saisi au terrain (BOUTON, LIGNE1) → « Implantation et contrôle » ; points importés non utilisés retirables (anomalie, confirmation, Annuler) ; `_stkd` = implantation ; code de type ligne « 526_2 » = Pcode 526, string 2 (.1 au 1er point, `CLS` = fermeture .2) |
| Levé Leica CS20 | rapport « RAPPORT D'ARPENTAGE » (FRT) | strings, fermetures, points connus |
| Levé Trimble Access | rapport HTML (.htm) | stations, résiduels, implantations |
| Fichier de tiers / logiciel | CSV avec noms non numériques | renumérotés, nom d'origine au journal |
| Orthophoto du projet | JPG / PNG + `.jgw` / `.pgw` / `.wld` et/ou `.aux.xml` / `.prj` | MTM, UTM ou degrés (converti en MTM) ; grandes images réduites à 8192 px |
| Calibration de site Emlid | texte copié de Flow 360 (2 pages) | paires et résidus, précision, paramètres ; retenue par nom de système |
| Photos du terrain | JPG / PNG | réduites à 1600 px, légende, ajoutées au rapport |
| **Sortie Civil 3D** | `<levé>_modif.txt` | `P,N,E,Z,D`, virgule, CRLF, **sans en-tête** |
| Dessin | `<levé>.dxf` | points + polylignes par code, couches NouvLR |
| Rapport | `<levé>_rapport.html` | imprimable en PDF |
| Séance | `<levé>_seance.json` | levé d'origine + corrections + analyses + photos |
| Mémoire des codes | `.json` (Options → Exporter / Importer) | associations de codes par arpenteur |

## 4. Installation / mise en ligne

- **Déploiement :** GitHub Pages, dépôt public `Arpent-Nord/APP-Validation-Leve`, branche `main`, dossier racine.
- **Fichiers du dépôt :** `index.html` (application complète en un seul fichier : dictionnaire des codes, lecture, validation, corrections, analyse, rapport), `sw.js` (cache hors ligne), `manifest.webmanifest` + icônes (installation), `guide.html`, `README.md`, `docs/CAHIER.md`.
- **Publier une nouvelle version :**
  1. modifier `index.html` et la mention « Version vX.Y.Z-AAAA-MM-JJ » (onglet Options) ;
  2. changer la constante `VERSION` de `sw.js` (`leve-vX.Y.Z-AAAA-MM-JJ`) pour forcer la mise à jour du cache ;
  3. téléverser les deux fichiers sur `main` ; GitHub Pages republie en 1 à 10 minutes ;
  4. recharger la page (deux fois au besoin) et vérifier la version affichée ;
  5. mettre à jour ce cahier (section 8) dans le même commit ou le suivant.
- **Logiciels requis :** navigateur récent (Chrome, Edge, Safari, Firefox). « Dossier de travail » : Chrome ou Edge sur ordinateur seulement.
- **Sauvegarde :** routine autonome hebdomadaire (bundle Git, `code_actuel.zip`, cahier PDF) dans le SharePoint de développement ; versions validées par l'outil « Validation des versions ».

## 5. Paramètres, comptes et secrets

- **Aucune clé d'API, aucun jeton, aucun compte** utilisé par l'application.
- **Services externes (affichage seulement, facultatifs) :** polices Google Fonts ; imagerie en ligne Esri World Imagery ou service personnalisé (fond de plan). Sans réseau, l'application fonctionne avec les orthos du projet.
- **Données conservées sur l'appareil seulement** (stockage du navigateur) : séances (5 dernières), mémoire des codes par arpenteur, réglages du fond, dossier de travail choisi.

## 6. Règles métier et calculs importants

- **Description de point :** `Pcode String.Contrôle [attributs]` ; liaisons multiples séparées par `_` (ex. `181 1_186 1.1`). Contrôles : `.1` début, `.2` fermeture, `.3/.4` courbe, `.6` cercle.
- **Codes :** liste ArpentNord (numéro, abréviation APH, description, catégorie, couche NouvLR). Codes hors standard associés par l'outil CODES ; texte d'origine gardé en attribut au besoin.
- **Numérotation standard (obligatoire pour exporter) :** Date `AAMMJJNNNN` ou jour julien `AJJJSSNNN`. 9 arpenteurs et moins : arpenteur k = plage (k−1)·1000+1 à +998 ; dépassement / levé d'un tiers : 9001-9999 ; numéros en x000 hors plage. Limite Civil 3D : 1 à 4 294 967 295 (standard Date valide jusqu'au 2042-12-31).
- **Hauteur d'antenne / prisme :** Z corrigé = Z − (bonne hauteur − hauteur au terrain).
- **Points connus et contrôle :** Emlid origine « Local », Leica / Trimble points connus = références (non exportées par défaut ; Z = 0,000 → contrôle horizontal seulement). Mesures sur ces points pendant le levé (no du monument en description ou `_stkd`) = **points connus contrôlés** ; la **calibration de site** elle-même (paires, résidus, paramètres) vient de Flow 360 (texte collé) car l'export CSV ne la contient pas ; `_stkd` = implantation (le point mesuré prend le prochain matricule libre, le no du monument passe en description). Écarts = mesuré − référence ; tolérance de contrôle par défaut 0,030 m (Options).
- **Qualité GNSS (rapport) :** statut FIX, RMS horizontal (tolérance 0,030 m) et vertical (0,050 m), PDOP, époques, distance à la base.
- **Points calculés :** commandes au terrain (`2P`, `2PD`, `2D`, `3PP`, `4P`, `3C`, `RECT`) ou outils ; marqués `CALCUL`, élévation à confirmer quand elle est posée à 0.
- **Analyse de fossé (ANALYSE) :**
  - surface TIN des points et des lignes (densifiées à 0,2 m, servent de lignes de rupture) entre deux coupes ; codes hors sol décochés d'office (clôtures, poteaux, bâtiments, arbres, glissières…) ; frontières choisies (lignes ou points) qui coupent la surface ;
  - eau accumulée : remplissage des dépressions sur une maille de 0,20 m, sortie aux deux limites ; cuvettes de moins de 5 L et 1 cm ignorées ;
  - débordement : cuvette dont le niveau dépasse la frontière / limite à ne pas franchir ; **revanche** = limite − niveau d'eau ;
  - écoulement : sens, pente la plus faible et contre-pentes (> 1 cm) sur la ligne du centre (69 FOC par défaut) ; **pente moyenne du fossé = moyenne des pentes du centre et des bords** (70 FOB ou 65 FOF), avertissement si un bord s'écarte de plus de 0,10 % ;
  - résultats indicatifs, à confirmer dans Civil 3D au besoin.
- **Sortie `_modif` :** toujours `P,N,E,Z,D`, virgule, CRLF, sans ligne d'en-tête, quel que soit le format lu ; points effacés et références exclus ; points calculés par l'app : code **300** PCB s'ils ne font partie d'aucune liaison (`300 *CALCUL 526`, 526 = code des points utilisés ; 299 PCT = calculé au terrain), sinon code et string de la ligne (`526 2 *CALCUL`) ; description : `Pcode String.Contrôle *attributs` (ex. `526 2.1 *Conteneur`) — le « * » précède tout ce qui suit le code et la liaison, le code seul définit le type de point / ligne du gabarit Civil 3D.

## 7. Limites connues et pistes d'amélioration

- La calibration de site n'est pas dans l'export CSV Emlid : elle doit être collée depuis Flow 360 (retenue ensuite sur l'appareil pour le même système).
- Analyse de fossé : triangulation sans contraintes strictes (lignes densifiées), résultats indicatifs.
- Imagerie en ligne : selon le serveur, le fond peut être absent de l'image du rapport (le rapport l'indique).
- Dossier de travail : Chrome / Edge sur ordinateur seulement ; la sauvegarde automatique du navigateur reste propre à un poste.
- Pistes : liste maîtresse unique des codes partagée avec APP-PCodes ; numérotation des levés de tiers en jour julien ; référentiel des arpenteurs tiré de Monday ; autres analyses sous le bouton ANALYSE.

## 8. Journal des versions

| Version | Date | Changement principal |
|---|---|---|
| v9 | 2026-10-21 | Codes hors standard (CODES) avec mémoire par arpenteur ; numérotation 9 arpenteurs et moins ; fichiers de tiers |
| v9.2 | 2026-11-03 | Code 28 (balises) ; LIER en continu ; EFFACER LIAISONS |
| v9.3 | 2026-11-03 | Liaison continue appliquée d'office ; effacer / rétablir un point |
| v9.4 | 2026-11-03 | LIER avec un autre code (double liaison) ; sauvegarde automatique de la séance |
| v9.5 | 2026-11-03 | Bouton ANALYSE (fossé) ; vue du levé au rapport |
| v9.5.1-9.5.3 | 2026-11-03 | Orthos du projet (UTM, .aux.xml, grandes images, fichier de référence séparé) ; codes hors sol |
| v9.6 | 2026-11-03 | Fichier de séance ; dossier de travail |
| v9.6.1-9.6.6 | 2026-11-03 | Analyse : limite à ne pas franchir, frontières, ligne du profil, pente moyenne centre + bords, repères 1/2 |
| v9.7 | 2026-11-03 | Rapport : vue épurée, profil orienté comme le plan, tableau d'écoulement, photos du terrain |
| v9.7.1 | 2026-11-17 | Points connus Emlid (Local) non exportés ni comptés ; dépôt renommé APP-Validation-Leve |
| v9.8 | 2026-11-17 | Calibration de site Emlid jointe au rapport (paires, résidus, paramètres) ; section « Points connus contrôlés » |
| v9.9 | 2026-11-18 | Lignes tracées dans Emlid (code « 526_2 », Control code CLS) reprises d'office en liaisons ArpentNord |
| v9.10 | 2026-11-18 | _modif.txt : « * » devant tout ce qui suit le code et la liaison (gabarit Civil 3D) ; point calculé hors ligne = code 300 PCB (« 300 CALCUL 526 »), dans une ligne = code de la ligne + CALCUL |
| v9.11 | 2026-11-18 | EFFACER LIAISONS : « Lignes choisies sur le plan… » (toucher les lignes à effacer, en rouge) |
| v9.12 | 2026-11-18 | LIER 2 PTS : points du même code liés en une seule ligne (même string), reprise du string existant d'un point (ex. 385 5), fermeture en retouchant le 1er point |
| v9.13 | 2026-11-18 | LIER 2 PTS en liaison libre : la ligne suit l'ordre touché ; à la fin, renumérotation proposée (mêmes numéros redistribués dans l'ordre de la ligne, rows replacées), bloquée si une autre ligne ou commande changerait d'ordre ; journal, rapport, Annuler |
| v9.14 | 2026-11-18 | Implantation sans point numéroté (BOUTON, LIGNE1) : points de conception distingués des monuments, _stkd au standard date ; retrait (confirmé, annulable) des points importés non utilisés ; titre et commentaires du rapport |
| v9.15 | 2026-11-18 | Photos du rapport : taille choisie pour chaque photo (petite, moyenne, pleine largeur) |
| v9.16 | 2026-11-19 | Points d'intérêt au rapport (vue centrée numérotée, distances choisies entre les points, cotes point–ligne, point de référence avec désignation et symbole, symboles libres CC0 Temaki/Maki, coordonnées, exports CSV/DXF) ; sections du rapport affichées/cachées, préréglage « Rapport client » |
| v9.17 | 2026-11-19 | Fond d'ortho / imagerie sous la vue des points d'intérêt ; secteur marqué (cercle rouge, étoile) sur la vue générale ; case devant chaque titre de l'aperçu pour afficher / cacher la section |
| v9.18 | 2026-11-19 | Fond → « Dossier de tuiles » : index des tuiles (INDEX_TUILES.csv ou .jgw), choix automatique de l'ortho qui couvre le levé, chargement des seules tuiles utiles ; « Charger les tuiles de la vue », « Une tuile de plus autour », choix d'une autre ortho, aperçu complet ; contour des tuiles non chargées (plan seulement) ; dossier retenu (Chrome / Edge) |

## 9. Responsables

- Développeur principal et personne-ressource : voir le registre interne des applications (dépôt privé `OUTILS-ARPENT-NORD-INTERNE`) — aucun nom dans ce dépôt public.
- Relève : la Boîte à outils et ce cahier décrivent l'usage ; le code est un seul fichier `index.html` commenté en français.
