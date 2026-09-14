# Ultimate EPS — Collège Louis Pasteur, Villemomble

Application web pour composer des équipes équitables en ultimate, suivre les compétences
de chaque élève au fil du cycle, différencier les situations d'apprentissage et évaluer.

Elle fonctionne **hors ligne**, **sans compte**, et **sans envoyer aucune donnée sur Internet**
tant que la synchronisation n'est pas activée. Tout tient dans un dossier de fichiers statiques.

---

## 1. Mise en route

### Option A — GitHub Pages (recommandée)

1. Crée un dépôt sur GitHub (par exemple `ultimate-eps`) et dépose-y le contenu de ce dossier.
2. Dans le dépôt : **Settings → Pages → Source : Deploy from a branch → `main` / `/ (root)`**.
3. Au bout d'une minute, l'application est à l'adresse `https://<ton-compte>.github.io/ultimate-eps/`.
4. Sur ta tablette, ouvre cette adresse puis « Ajouter à l'écran d'accueil ». Elle s'installe
   comme une application et fonctionne ensuite **sans réseau**, ce qui est indispensable en gymnase.

### Option B — le fichier unique, sans rien installer

`ultimate-eps-autonome.html` est l'application **entière dans un seul fichier**
(HTML, CSS et JavaScript réunis, aucune dépendance). Double-clique dessus : elle s'ouvre
dans le navigateur et fonctionne immédiatement.

C'est la version à utiliser si tu veux essayer tout de suite, l'envoyer à un collègue,
ou la copier sur une clé USB. Tu peux aussi la déposer telle quelle sur GitHub Pages :
renomme-la `index.html` et c'est en ligne.

Seules différences avec la version en dossier : pas d'installation en application
sur l'écran d'accueil, et pas de cache hors ligne automatique (ces deux fonctions
exigent une adresse `http(s)` et le fichier `sw.js`).

> Les deux versions ne partagent pas leurs données : chaque adresse a son propre stockage.
> Choisis-en une pour ton cycle, et utilise l'export JSON pour passer de l'une à l'autre.

### Option C — serveur local, pour développer

```bash
python3 -m http.server 8000
# puis http://localhost:8000
```

> **Important :** les données sont stockées **par navigateur et par adresse**.
> La version ouverte depuis GitHub Pages et celle ouverte depuis le fichier local
> ne partagent rien. Choisis une adresse et gardes-y tes données, ou fais circuler
> la sauvegarde JSON (Réglages → Exporter / Importer).

---

## 2. Importer une classe

**Classes → Importer un CSV.**

**L'export d'appel du collège est reconnu tel quel** :

```csv
Élèves;encouragement/valorisation;Né(e) le;Sexe
MARTIN Léa;;12/03/2012;Féminin
BERNARD Lucas;à encourager;05/07/2011;Masculin
DE LA TOUR Jean-Luc;;01/01/2012;Masculin
```

| Colonne | Contenu | Ce qu'en fait l'application |
|---|---|---|
| 1 | `Élèves` — NOM et Prénom | séparés automatiquement : les mots en majuscules forment le nom |
| 2 | `encouragement/valorisation` | ignorée |
| 3 | `Né(e) le` | ignorée |
| 4 | `Sexe` — Masculin ou Féminin | sert uniquement à la contrainte de mixité |

L'application détecte seule le séparateur (`;`, `,`, tabulation) et l'encodage (UTF-8 avec repli
sur Windows-1252, le cas des exports Excel français). Un écran de mappage s'affiche quand même,
avec un aperçu en direct : tu vérifies d'un coup d'œil et tu corriges si besoin.

Cas gérés :

- noms composés et particules (`DE LA TOUR Jean-Luc`), apostrophes (`N'GUYEN Maï`) ;
- colonnes `Nom` et `Prénom` séparées, si ton export a cette forme ;
- sexe sous n'importe quelle forme (`Masculin`/`Féminin`, `M`/`F`, `Garçon`/`Fille`, `1`/`2`) ;
- fichier **sans ligne d'en-tête** : la première colonne sert de nom et la colonne du sexe est
  devinée d'après son contenu ;
- absence de colonne sexe : le mode « équipes mixtes » est alors sans effet, et on peut
  renseigner le sexe élève par élève dans sa fiche.

---

## 3. Le parcours de l'application

Le menu suit l'ordre dans lequel on travaille vraiment :

```
1. Classes  →  2. Leçons  →  Présences  →  ┌ Équipes
                                            ├ Situations
                                            └ Matchs

puis, hors séance :   Contraintes  ·  Statistiques  ·  Réglages  ·  Situations (banque)
```

On choisit la classe, on ouvre la leçon, on fait l'appel — et l'appel fait, trois portes
s'ouvrent : composer les équipes, lancer une situation, ou saisir les matchs. Elles sont
proposées directement en bas de l'écran des présences.

**Contraintes** (élèves à séparer ou à garder ensemble), **Statistiques** (niveau des élèves,
évaluation, qualité des données) et **Réglages** vivent en dehors de la séance : on y passe
une fois de temps en temps, pas chaque semaine.

**Situations** est la banque : une bibliothèque de situations toutes prêtes, indépendante des
classes et des leçons (voir plus bas).

La liste des leçons est **entièrement cliquable** : une ligne suffit pour rouvrir une leçon
passée ou une leçon préparée à l'avance. Cliquer sur « 2. Leçons » dans le menu ramène
toujours à cette liste.

### Présences

Tout le monde est **présent par défaut**. On touche simplement le nom de l'élève pour le
faire changer d'état :

| Clic | État | Fond |
|---|---|---|
| — | présent | vert |
| 1 | absent | rouge |
| 2 | blessé / dispensé | orange |
| 3 | retour à présent | vert |

Un élève marqué blessé reçoit un menu « rôle » (arbitre, observateur, secrétaire, coach)
sur une deuxième ligne, pour que la ligne du nom reste entièrement cliquable. C'est le moyen
le plus simple de collecter des statistiques de match : les dispensés tiennent la tablette
pendant que les autres jouent.

Le bouton « Reprendre la leçon précédente » évite de tout ressaisir chaque semaine.

### Équipes

- **Reprendre les équipes de la leçon précédente** en un bouton : les **absents et les blessés
  du jour** sont retirés, les élèves de retour retrouvent leur équipe, les nouveaux venus sont
  placés dans la moins fournie. Un message récapitule ce qui a changé (« 5 absents retirés ·
  7 blessés retirés · 1 élève replacé »). C'est le moyen le plus rapide d'avoir des équipes
  stables d'une semaine à l'autre.
- **Toute équipe tombée sous 4 joueurs est signalée par un fond bleu clair**, avec l'étiquette
  « effectif à compléter » et un rappel sous les équipes. C'est le cas typique après le retrait
  des absents : on voit immédiatement où déplacer quelqu'un.
- **3 à 9 équipes**, **3 à 7 élèves** par équipe. Tu fixes soit le nombre d'équipes,
  soit la taille : l'autre valeur se déduit des présents, avec alerte si la combinaison
  est impossible.
- **Deux modes de composition**, à croiser librement avec la mixité :

| Mode | Ce que ça donne | Quand s'en servir |
|---|---|---|
| **Équilibré** | équipes de force égale entre elles, mélangeant les niveaux en leur sein | matchs équilibrés, entraide entre élèves |
| **Niveau** | équipes homogènes en leur sein, **appariées deux à deux** de force équivalente | travail différencié ; conseillé avec un **nombre pair** d'équipes, pour que chacune ait un adversaire à sa mesure |

  En mode Niveau, les équipes 1 et 2 sont les plus fortes et se valent, les 3 et 4 viennent
  ensuite, et ainsi de suite. Avec un nombre impair, la dernière équipe n'a pas de jumelle :
  l'application le signale.
- **Mixité** : équipes mixtes équilibrées, équipes non mixtes, ou sans contrainte —
  choix libre à chaque leçon, et combinable avec les deux modes ci-dessus.
- **Stabilité** (curseur 0 → 100) : à 100, on ne touche qu'au strict nécessaire par rapport
  à la leçon précédente (les absents sortent, les revenants réintègrent leur équipe) ;
  à 0, rebrassage complet.
- **Contraintes** de la classe (élèves à séparer, élèves à garder ensemble) toujours prioritaires.
- Déplacement manuel : glisser-déposer à la souris, ou **touche un élève puis touche
  l'équipe d'arrivée** sur tablette. Les indicateurs se recalculent en direct.
- « Affichage à projeter » ouvre une vue plein écran imprimable.

### La banque de situations (onglet « Situations »)

Préparer une situation avec ses paliers, ses seuils, ses messages et ses images prend du temps.
La banque évite de recommencer : une fiche s'y range une fois, et se réutilise autant de fois
qu'on veut.

- **« Vers la banque »** sur n'importe quelle situation de leçon l'y enregistre, sans ses scores.
  Si elle en venait déjà, on choisit entre mettre la fiche à jour ou en créer une nouvelle à côté.
- **« Ajouter à la leçon »** insère une copie dans la leçon ouverte, et propose au passage
  **d'ajuster les critères alimentés** : la même situation peut servir la Technique cette semaine
  et la Défense la suivante, sans toucher à la fiche d'origine.
- La copie repart avec des paliers neufs : les messages reçus lors d'une leçon précédente ne se
  mélangent pas avec ceux du jour.
- La banque s'**exporte et s'importe** en un fichier JSON, pour l'échanger avec un collègue.

### Situations — critères travaillés et paliers

Une situation, c'est une tâche mesurable, rattachée à une leçon, qui alimente
**les critères que tu choisis** avec leur pondération. Exemple :

| Situation | Critères alimentés |
|---|---|
| Passe et va, 10 tentatives | Technique 100 % |
| Montée de disque sur 3 zones, chrono | Jeu en progression 80 % · Technique 20 % |
| 3 contre 2 défensif | Défense 100 % |

Technique et Jeu en progression se travaillent isolément ou ensemble : il suffit de monter
les deux curseurs. Un curseur à 0 veut dire que la situation ne dit rien sur ce critère.

Chaque situation se découpe en **paliers** ordonnés (par exemple 5 m, 8 m, 11 m).
Chaque palier définit deux seuils qui découpent le score en trois bandes :

| Score | Bande | Ce qui s'affiche à l'élève |
|---|---|---|
| < seuil bas | ◆ Critères de réalisation | ton message d'aide technique |
| entre les deux | ▶ Recommandation | placement, intention, consigne de jeu |
| ≥ seuil haut | ▲ Complexification | « recule de 3 mètres », etc. |

Pour **chacune des trois bandes** tu choisis librement ce qui arrive ensuite :
redescendre de deux paliers, redescendre d'un palier, rester, monter d'un palier,
ou **sauter deux paliers** d'un coup. Un élève qui explose l'objectif peut donc gagner
deux crans en une tentative, et un élève en difficulté redescendre là où il réussit.
L'application ne sort jamais des paliers existants.

Chaque message accepte les **retours à la ligne** (pour lister des critères de réalisation)
et peut porter une **image** : schéma de placement, photo d'un geste, croquis de terrain.
L'image est réduite automatiquement avant d'être enregistrée.

La situation elle-même peut porter un **schéma du dispositif** — plots, zones, sens de
déplacement. Il apparaît sur sa fiche, sur la tablette au moment où le groupe se présente,
et sur la feuille imprimable des groupes.

Le message s'affiche dès la saisie du score. **S'il est fermé par mégarde, il n'est pas perdu** :
il est mémorisé par élève, réaffiché au passage suivant en mode kiosque, et consultable
depuis la fiche de la situation. Au passage suivant, l'élève **choisit** son palier
(le palier conseillé est mis en avant) — l'application note s'il a suivi la recommandation,
ce qui donne un indicateur d'autonomie de la classe.

Les **groupes de travail** (2 à 8 élèves) se composent à la main, se reprennent des équipes,
ou se génèrent automatiquement en **maximisant le brassage** : le générateur évite de reformer
les paires déjà vues. C'est ce brassage qui rend possible l'estimation individuelle (voir §4).

On déplace un élève d'un groupe à l'autre en le touchant puis en touchant le groupe
d'arrivée, **sans limite de taille** : rien n'empêche de faire un groupe de 2 et un de 8.
Le bouton **« Afficher / imprimer »** produit une feuille propre, lisible de loin, avec pour
chaque groupe sa composition, son palier, sa consigne et des cases vierges pour noter
les scores à la main.

### Matchs

- **Les rencontres sont organisées en tours de deux matchs**, jamais plus : deux terrains, et les
  équipes qui ne jouent pas pendant un tour sont affichées comme **observatrices** — ce sont elles
  qui tiennent les feuilles de relevés et arbitrent. Le tour de chaque match reste modifiable à la
  main ; si tu en places trois dans le même tour, le troisième bascule automatiquement au suivant.
- **Bouton « Modifier les relevés »** : tu choisis ce que les observateurs comptent, tu renommes
  les compteurs, tu les **ranges dans l'ordre** où tu veux les voir sur la tablette (flèches ↑ ↓),
  et tu décides lesquels **rapportent des points au score**. Si tu actives « Buts »
  avec 1 point, chaque but relevé met le résultat de la rencontre à jour immédiatement. La passe
  décisive, elle, est décochée par défaut : tu ne la relèves que si tu en as vraiment besoin.
- **Bonus par combinaison d'actions** : « trois passes puis but sans perte de disque = 5 points ».
  Ces boutons apparaissent sur **fond ambré, avec une étoile**, pour qu'on ne les confonde jamais
  avec un but ordinaire — ce sont eux qui rapportent gros.
- **Réinitialiser une rencontre** efface son score, ses relevés et son fair-play — cette action
  détruit du travail saisi par les élèves, elle demande donc **le code enseignant**, le même que
  pour sortir du kiosque.
- Génération du tournoi complet en un clic, ou match par match.
- **Barème propre à chaque match** : valeur du but ordinaire, plus autant d'actions bonus
  que tu veux (« but après passe dans le dernier tiers = 10 points »). Boutons de saisie rapide.
- **Classement automatique** de la leçon : **victoire 3 points, match nul 2, défaite 1**.
  Le point de participation récompense l'équipe qui joue même battue, ce qui évite de
  décrocher après deux défaites.
- **Trois comptages seulement**, par équipe (un observateur suffit) ou par joueur
  (plus précis) : **passes réussies**, **passes perdues**, **interceptions**. Rien de plus,
  pour que ce soit tenable par un élève pendant un match.
- **Une interception est automatiquement comptée comme une passe ratée pour l'adversaire.**
  L'observateur n'appuie donc que sur « interception » : l'application ajoute la passe ratée
  en face toute seule, sans double comptage. Le compteur « passes perdues » ne sert qu'aux
  pertes non interceptées — disque lâché, sorti du terrain, passe au sol. Un récapitulatif
  affiche en direct le total réel et le pourcentage de réussite de chaque équipe.
  (En relevé joueur par joueur, ce report automatique est désactivé : on ne peut pas savoir
  à quel adversaire précis attribuer la passe manquée.)
- **Fair-play** : un bouton « Rien à signaler » par équipe crédite tout le monde d'un coup ;
  ensuite, « Ajuster individuellement » permet de désigner les exceptions, dans un sens
  (à valoriser) comme dans l'autre (à reprendre). Les élèves non désignés gardent la note
  collective de leur équipe.

### Évaluation (onglet Statistiques)

- Constructeur de grille : items librement nommés, chacun rattaché à un critère
  (ou à l'indice global, ou au résultat des matchs), avec coefficient ; niveaux de maîtrise
  et barème paramétrables ; note sur 20 par défaut.
- **« Proposer les niveaux depuis les indices »** remplit les cases vides à partir des données
  récoltées. Les cases déjà saisies à la main ne sont jamais écrasées. C'est un point de départ,
  pas un verdict.
- **Auto-évaluation** en mode kiosque : l'élève se situe sur chaque critère, voit sa note estimée
  et sa courbe de progression depuis le début du cycle.
- Le tableau affiche l'**écart auto-évaluation / évaluation**. Ce n'est pas une erreur de l'élève :
  c'est un indicateur de lucidité et un excellent support d'entretien.
- Export CSV des notes (séparateur `;`, décimales à la française, compatible Excel/Pronote).

---

## 4. Comment les indices sont calculés

C'est le cœur de l'application. Elle ne cache rien : la page **Fiabilité** vérifie
le modèle sur tes propres données.

### Les quatre critères

**Technique · Jeu en progression · Défense · Fair-play.**
Entièrement renommables et repondérables dans les Réglages — donc réutilisables pour
le handball, le basket, etc. Un **indice global** sur 10 en est la moyenne pondérée,
avec **5 = moyenne de la classe**.

Chaque critère est alimenté par des sources précises :

La **fiche d'un élève** (Statistiques → Élèves, puis clic sur son nom) affiche, en plus de son
radar et de sa progression, son **parcours en match** : victoires, nuls, défaites, différence de
points, et la liste des équipes par lesquelles il est passé leçon par leçon, avec ses coéquipiers
et le nombre de camarades différents rencontrés sur le cycle.

| Critère | D'où viennent les données |
|---|---|
| Technique | situations qui le ciblent · pourcentage de passes réussies en match (interceptions adverses comprises) |
| Jeu en progression | situations qui le ciblent · volume de passes réussies en match |
| Défense | situations qui le ciblent · interceptions relevées en match |
| Fair-play | note collective « rien à signaler » et ajustements individuels |

### Quatre sources, dans cet ordre

**1. Situations d'entraînement** (45 % par défaut)

Les scores bruts sont normalisés en z-score : « 14 passes » ne veut rien dire seul,
« 14 passes, soit au-dessus de la moyenne de la classe sur cette situation » veut dire quelque chose.

Quand une tentative est réalisée **en groupe**, on ne peut pas attribuer le score à un seul élève.
L'application résout un système de **moindres carrés régularisés (ridge)** où chaque élève a une
contribution individuelle inconnue, et où chaque tentative affirme « la moyenne des contributions
de ce groupe vaut *z* ». Si les partenaires tournent entre les tentatives, le système devient
identifiable et **sépare l'élève de ses partenaires** — c'est le principe du *plus-minus ajusté*
utilisé dans les sports collectifs professionnels.

Vérification faite sur données simulées (`test/check.js`) :

| Protocole | Corrélation entre niveau réel et niveau estimé |
|---|---|
| Mesure individuelle | r = 1,00 |
| 6 tours, groupes de 3, partenaires brassés | r = 0,91 |
| 6 tours, groupes de 3, partenaires figés | r = 0,70 |

D'où l'indicateur « brassage suffisant / insuffisant » affiché sur chaque situation.
L'objectif est **au moins 3 partenaires distincts** par élève et par situation.

**Paliers** : 15 passes à 5 m et 15 passes à 8 m ne valent pas la même chose. La normalisation
se fait **à l'intérieur de chaque palier** (dès qu'il compte au moins 3 tentatives), puis une
**prime de palier** est ajoutée proportionnellement à l'écart au palier moyen de la classe.
Sans elle, un élève serait pénalisé d'avoir accepté de complexifier sa tâche — exactement
le contraire de ce qu'on veut encourager.

**2. Statistiques de match** (30 %)

Les trois comptages sont convertis en indicateurs comparables, puis normalisés au niveau
de la classe :

| Indicateur | Critère alimenté |
|---|---|
| Pourcentage de passes réussies | Technique |
| Volume de passes réussies | Jeu en progression |
| Interceptions | Défense |

Le total des passes ratées d'une équipe vaut **ce que l'observateur a saisi plus les
interceptions relevées en face**. Une interception n'est donc comptée qu'une seule fois,
mais elle pèse des deux côtés : en défense pour celui qui intercepte, en technique pour
celui qui a perdu le disque.

Une statistique relevée **par équipe** est diluée à 60 % avant d'être attribuée aux joueurs :
elle ne les distingue pas entre eux. Une statistique **par joueur** compte pleinement.

**3. Ce que tu observes** (15 %) — le fair-play relevé sur chaque match, et tes `+` / `−`
sur un critère pendant la leçon.

**4. Résultat des matchs** (10 %) — calculé **en dernier**, volontairement.

L'écart de points est d'abord rendu indépendant du barème : on utilise la marge relative
`(A − B) / (A + B)`, si bien que 30–10 avec des buts à 1 point et 300–100 avec des buts à
10 points donnent exactement la même valeur. Cette marge est ensuite comparée à la
**marge attendue** compte tenu des indices des deux équipes : battre l'équipe la plus forte
de 2 points vaut plus que battre la plus faible de 15. Seule cette « surprise » alimente
l'indice **global** — jamais les critères techniques.

> **Pourquoi en dernier ?** Si l'écart de points alimentait le critère technique, et que le
> critère technique servait à prédire l'écart de points, le modèle se mordrait la queue et
> amplifierait ses propres erreurs. En calculant le résultat après coup, on garde une mesure
> indépendante — et la page Fiabilité peut alors vérifier honnêtement si les deux concordent.

### Agrégation dans le temps

- **Moyenne glissante pondérée (EWMA)** sur les leçons : les séances récentes comptent plus
  (35 % par défaut), sans effacer l'historique. C'est ce qui fait évoluer l'indice au fil du cycle.
- **Régression vers la moyenne** proportionnelle au nombre d'observations : un élève vu deux fois
  reste proche de 5. Le niveau de fiabilité (faible / moyenne / bonne) est affiché partout.
  Un élève peu observé est réparti comme un élève moyen — c'est volontaire, c'est plus juste
  que de lui inventer un niveau.

### L'onglet Qualité des données

- **Corrélation** entre l'écart d'indice des deux équipes et la marge réelle des matchs,
  avec nuage de points et droite d'ajustement. C'est la vérification de ton hypothèse
  « plus la technique est maîtrisée, plus l'écart est important », sur *ta* classe.
- **Brassage des partenaires** situation par situation.
- **Couverture des données** critère par critère, et liste des élèves à moins de 4 observations.

---

## 5. Mode kiosque (la tablette qui circule)

Plein écran, gros boutons, aucun accès aux réglages ni aux notes des autres. Le bouton qui
l'ouvre est violet, pour ne pas le confondre avec les commandes de l'enseignant.

**La sortie est protégée par un code** (1999 par défaut, modifiable dans Réglages → Mode kiosque).
Un pavé numérique s'affiche : sans le code, la tablette reste en mode élève et personne ne va se
promener dans les notes de la classe. Le même code protège la réinitialisation d'une rencontre.

Trois modes :

- **Situations** : l'élève touche son groupe → relit le message du passage précédent →
  choisit son palier → saisit son score → reçoit son retour automatique (texte et image) →
  passe la tablette.
- **Statistiques de match** : les deux équipes **côte à côte sur un seul écran**, sans aucun
  défilement. Score en gros en haut de chaque colonne, gros bouton **« + But »** qui met
  le résultat à jour immédiatement, et trois compteurs `+` / `−` en dessous. Une légende
  rappelle qu'on ne compte pas une passe ratée en plus d'une interception.
- **Auto-évaluation** : l'élève se situe sur chaque critère, voit sa note estimée et sa progression.

Les **bonus par combinaison d'actions** définis dans « Modifier les relevés » apparaissent bien
dans le kiosque, sur la ligne des boutons de score de chaque équipe, en **ambré avec une étoile**
à côté du bouton « + But ».

Depuis l'écran des matchs, un bouton **Statistiques** ouvre une page faite pour les élèves :
ils choisissent leur équipe, voient ses totaux (points au classement, bilan, différence, pourcentage
de passes réussies, chaque compteur relevé), **comparent les équipes entre elles** dans un tableau
où la leur est surlignée, et parcourent le **détail de chaque match** tour par tour.

---

## 6. Données, sauvegarde et RGPD

- Tout est enregistré dans le **stockage local du navigateur** de l'appareil.
- **Exporte régulièrement** ta sauvegarde JSON (Réglages). Vider le cache du navigateur
  efface les données ; il n'y a pas de récupération possible.
- L'import propose de **fusionner** ou de **remplacer**.
- Option **anonymisation** : les élèves s'affichent en « Prénom N. » partout, y compris
  en vidéoprojection.

### Synchronisation Firebase (optionnelle)

Pour retrouver ses données sur plusieurs appareils :

1. Crée un projet sur [console.firebase.google.com](https://console.firebase.google.com).
2. Active **Firestore** et **l'authentification anonyme**.
3. Colle la configuration JSON du projet dans Réglages → Synchronisation.

Règles Firestore minimales :

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /ultimate-eps/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

> **Vigilance RGPD.** Héberger des noms d'élèves sur un service cloud non conventionné
> relève du traitement de données personnelles de mineurs. Avant d'activer la synchronisation :
> active l'anonymisation, ou n'importe que les prénoms, et rapproche-toi du chef d'établissement
> et du DPD académique. En mode local (par défaut), la question ne se pose pas : rien ne sort
> de l'appareil.

---

## 7. Structure du code

```
index.html               page unique, aucune dépendance externe
css/app.css              feuille de style (clair par défaut, mode sombre disponible)
js/core.js               stockage local, utilitaires, algèbre linéaire, import CSV
js/progress.js           paliers, seuils, messages conditionnels
js/ratings.js            moteur d'indices (ridge, EWMA, régression vers la moyenne)
js/teams.js              composition des équipes (coût + recherche locale avec recuit)
js/ui.js                 briques d'interface (radar, courbes, nuage de points, modales)
js/cloud.js              synchronisation Firebase, optionnelle
js/views.js              classes, élèves, contraintes, fiabilité, réglages
js/training.js           situations d'entraînement et paliers
js/eval.js               grilles, notes, auto-évaluation
js/lesson.js             présences, équipes, matchs
js/kiosk.js              mode tablette
js/main.js               routeur, actions, jeu de démonstration
sw.js                    cache hors ligne
test/check.js            vérification des calculs (Node, sans navigateur)
test/csv.js              vérification de l'import CSV sur l'export du collège
test/browser.js          test de bout en bout (Playwright)
```

Aucune bibliothèque tierce, aucun outil de compilation : les fichiers déposés sont les
fichiers exécutés. Les scripts sont des scripts classiques (pas des modules ES), ce qui
permet d'ouvrir `index.html` directement depuis le disque.

### Tests

```bash
node test/check.js      # ridge, paliers, équipes, barème, interceptions, mode Niveau
node test/csv.js        # import de l'export d'appel du collège
npm i playwright && node test/browser.js   # parcours complet dans Chromium
```

---

## 8. Conseils d'usage

**Les trois premières leçons servent à alimenter le modèle.** Avant cela, les indices valent
tous à peu près 5 et les équipes sont composées presque au hasard — ce qui reste préférable
à un choix par affinités. Commence par des situations mesurables à partir de la leçon 1.

**Brasse les partenaires.** C'est la condition qui fait la différence entre « je sais que ce
groupe réussit » et « je sais qui, dans ce groupe, fait la différence ». Le bouton
« Générer automatiquement » est fait pour ça.

**Garde le résultat des matchs à faible poids.** Un élève peut très bien jouer dans une équipe
qui perd. Le réglage par défaut (10 %) est déjà un maximum raisonnable. Si tu veux valoriser
l'investissement en match, la **progression individuelle** entre la première et la dernière
leçon est un indicateur plus juste que le résultat brut.

**Regarde la qualité des données avant de noter.** Si la corrélation affichée est faible,
c'est que le modèle n'explique pas grand-chose de ta classe : sers-t'en pour composer les
équipes, mais pas pour justifier une note.

---

Développé pour un cycle d'ultimate en collège. Réutilisable pour toute APSA en renommant
les cinq critères dans les Réglages.
