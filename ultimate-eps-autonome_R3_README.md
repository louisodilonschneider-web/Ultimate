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
                                            ├ Échauffement
                                            ├ Situations
                                            └ Matchs

puis, hors séance :   Contraintes  ·  Statistiques  ·  Réglages  ·  Situations (banque)
```

On choisit la classe, on ouvre la leçon, on fait l'appel — et l'appel fait, quatre portes
s'ouvrent : composer les équipes, lancer l'échauffement, lancer une situation, ou saisir
les matchs. Elles sont proposées directement en bas de l'écran des présences.

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
- **3 à 9 équipes**, **2 à 7 élèves** par équipe. Tu fixes soit le nombre d'équipes,
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
- **Couleur de chasuble** : la pastille ronde à droite du nom d'équipe ouvre une palette de
  douze couleurs. On choisit celle du jeu de chasubles réellement distribué ; le nom de
  l'équipe suit la couleur (« Bleue », « Rouge »…), et un point signale les couleurs déjà
  portées par une autre équipe. Le champ « nom affiché » reste là si tu préfères un autre
  intitulé. La couleur se retrouve partout : cartes d'équipe, tableau des matchs, classement,
  écran kiosque, feuille imprimable.
- « Affichage à projeter » ouvre une vue plein écran imprimable.

### La banque de situations (onglet « Situations »)

Préparer une situation avec ses paliers, ses seuils, ses messages et ses images prend du temps.
La banque évite de recommencer : une fiche s'y range une fois, et se réutilise autant de fois
qu'on veut.

- **« Copier dans la banque »** sur n'importe quelle fiche de leçon — échauffement comme situation,
  paliers comme confrontation — l'y enregistre sans ses scores. La fiche de la leçon continue sa vie
  de son côté. Si elle venait déjà de la banque, on choisit entre mettre la fiche d'origine à jour
  ou en créer une variante à côté.
- **Des dossiers** regroupent les variantes d'un même exercice : « Passe à 10 » à 5 m, à 8 m, en
  mouvement. Le bouton « Nouveau dossier » en crée un ; le menu **Dossier** de chaque ligne y range
  une fiche (avec l'entrée « ＋ Nouveau dossier… » pour en créer un sans quitter la page) ; et la
  fenêtre « Copier dans la banque » permet de choisir le dossier, ou d'en créer un au passage.
  **Dupliquer** crée une variante dans le même dossier, prête à être ajustée. Supprimer un dossier
  ne supprime jamais ses fiches : elles retournent dans « Sans dossier ».
- **« Ajouter à la leçon »** insère une copie dans la leçon ouverte, et propose au passage
  **d'ajuster les critères alimentés** : la même situation peut servir la Technique cette semaine
  et la Défense la suivante, sans toucher à la fiche d'origine.
- La copie repart avec des paliers neufs : les messages reçus lors d'une leçon précédente ne se
  mélangent pas avec ceux du jour. Les oppositions et les équipes d'une confrontation ne sont jamais
  mises en banque : elles dépendent des présents du jour.
- La banque s'**exporte et s'importe** en un fichier JSON, dossiers compris, pour l'échanger avec un
  collègue. À l'import, un dossier du même nom est réutilisé plutôt que dupliqué.

### Échauffement et situations : deux onglets, deux formats

La leçon sépare **Échauffement** et **Situations**. Le fonctionnement est exactement le même — les
deux familles vivent simplement dans deux onglets différents, et la banque les range séparément
(filtre « Échauffements » / « Situations d'apprentissage »).

**Dans les deux cas, la fiche se mène de deux façons, au choix.** Le menu « Comment se mène cette
situation ? » est la première question posée à la création.

| Format | Ce que font les élèves | Exemples |
|---|---|---|
| **Par paliers** | groupes de travail, paliers, seuils, score saisi, retour automatique différencié | maximum de passes en 1 minute, montée de disque chronométrée, 3 contre 2 défensif |
| **En confrontation** | deux équipes s'opposent, chacune note son score, le vainqueur s'affiche tout seul | passe à 10, déménageur, gagne-terrain, épervier |

Un échauffement peut donc être une simple opposition, et une situation d'apprentissage peut l'être
aussi : un gagne-terrain est un vrai contenu d'apprentissage, pas une mise en route.

**Les fiches se réordonnent** dans la leçon : les flèches ↑ ↓ de chaque carte la déplacent dans sa
liste, sans toucher à l'autre onglet. L'ordre affiché est celui du déroulement prévu — pratique quand
on prépare la séance la veille et qu'on intercale une situation le matin même.

En format **confrontation**, le bouton « Opposer les équipes » ouvre une fenêtre en deux temps.

**1. Quelles équipes s'affrontent ?** Soit les équipes de la leçon, soit **des équipes composées pour
ce seul échauffement**. Un échauffement n'a pas les mêmes contraintes qu'un match d'ultimate : rien
n'oblige à jouer un déménageur à quatre équipes de sept parce que le tournoi se joue comme ça. Tu
retrouves donc ici les mêmes réglages que dans l'onglet Équipes — nombre d'équipes ou taille des
équipes, composition équilibrée ou par niveau, mixité — et le bouton « Composer les équipes » les
tire à partir des élèves **présents du jour**, avec les contraintes de la classe. La composition
s'affiche aussitôt, nom par nom ; un second clic rebrasse. **Les équipes de la leçon ne sont jamais
modifiées** : les matchs d'ultimate gardent les leurs. Les équipes propres à l'échauffement restent
attachées à la fiche, sont rappelées sur la carte et servent aussi bien à ta saisie qu'à celle des
élèves sur tablette.

**2. Qui affronte qui ?** *Un tour simple* (chaque équipe joue une fois), *toutes contre toutes*, ou
*je choisis les affiches équipe par équipe* — on part du tournoi complet et on décoche ce qu'on ne
veut pas. Un aperçu montre, avant de valider, le détail **tour par tour** des oppositions.

**Les confrontations acceptent des bonus**, comme les matchs d'ultimate : « marquer après trois
passes sans perte = 3 points ». Ils se définissent dans la fiche, s'affichent en ambré avec une
étoile à côté du bouton `+ 1` sur la tablette, et rapportent leurs points au score de l'équipe.

Le champ **Terrains disponibles** décide du nombre d'oppositions simultanées : deux par défaut, mais
un déménageur peut tourner sur quatre petites aires pendant qu'un passe à 10 n'en occupe qu'une.
L'application ne promet jamais plus de terrains que le nombre d'équipes ne permet d'en remplir, et
le dit (« 5 équipes ne remplissent que 2 terrains »). **Les équipes en trop attendent leur tour —
jamais les mêmes** : le planificateur fait passer en premier celles qui ont le moins joué.

Il essaie plusieurs ordres de passage et garde le meilleur : de trois à neuf équipes, en « toutes
contre toutes », il atteint le nombre de tours minimal et répartit l'attente à une fois près. Cinq
équipes sur deux terrains donnent dix oppositions en cinq tours ; six équipes sur trois terrains,
quinze oppositions en cinq tours de trois. Les équipes au repos sont nommées sur ta fiche comme sur
la tablette des élèves.

C'est le même planificateur qui organise le tournoi d'ultimate — avec le nombre de terrains de la
leçon.

Chaque opposition reçoit deux scores, saisis par toi dans le tableau ou par les élèves en mode
kiosque (deux gros boutons `+1` par équipe). Ni palier, ni unité, ni groupe de travail :
l'éditeur masque tout ce qui ne sert pas.

**Ce que le résultat alimente** : l'écart relatif entre les deux scores — `(nous − eux) / total`,
donc indépendant du barème du jeu — nourrit les critères que tu as cochés, à l'échelle de l'équipe,
avec la même dilution que les relevés collectifs de match (il ne distingue pas les joueurs entre eux).
**Ce résultat n'entre pas dans le classement de la leçon** : un échauffement n'est pas une rencontre
officielle. Si tu préfères un échauffement purement qualitatif, ne coche aucun critère : la fiche
sert alors de simple rappel de consigne à projeter.

Créer une fiche dans l'onglet Échauffement la marque comme échauffement ; une fiche prise
dans la banque revient dans l'onglet dont elle vient, avec son format. Les oppositions, elles,
ne sont jamais mises en banque : elles dépendent des équipes du jour.

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

**Les paliers sont colorés du orange au vert foncé** : le palier 1 tire vers l'orange, et plus on
monte, plus la couleur vire au vert profond. Le dégradé s'étale toujours sur le nombre de paliers
réellement définis — avec trois paliers comme avec sept, le premier est orange et le dernier vert
foncé. On retrouve cette couleur partout où un palier est nommé : la répartition de la classe, le
tableau des tentatives, les cartes de groupe, l'écran de choix du kiosque, le bandeau « nouveau
palier » après un score, et la feuille imprimable. De loin, dans un gymnase, la couleur suffit à
savoir où en est un groupe sans rien lire.

Chaque message accepte les **retours à la ligne** (pour lister des critères de réalisation)
et peut porter une **image** : schéma de placement, photo d'un geste, croquis de terrain.
L'image est réduite automatiquement avant d'être enregistrée.

La situation elle-même peut porter un **schéma du dispositif** — plots, zones, sens de
déplacement. Il apparaît sur sa fiche, sur la tablette au moment où le groupe se présente,
et sur la feuille imprimable des groupes.

**L'écran de rappel des messages est rangé par palier.** Quand un groupe se présente, la tablette
n'aligne plus les élèves les uns après les autres : elle forme un bloc par palier — orange pour le
premier, vert de plus en plus foncé à mesure qu'on monte, exactement les couleurs de l'écran de choix
du palier. Chaque bloc annonce en gros le palier et sa consigne, affiche les **prénoms en grosses
pastilles** (celles des messages non encore lus sont pleines), puis le message. Un message identique
pour tout le bloc n'est écrit qu'une fois ; s'ils diffèrent, chaque message porte le prénom des
élèves concernés. Les élèves qui vont travailler au même endroit lisent au même endroit.

**Effacer une tentative efface tout ce qu'elle avait produit.** C'est le cas typique de la situation
qu'on essaie soi-même avant le cours, avec un score inventé : la croix rouge en bout de ligne, dans
« Dernières tentatives », retire la tentative **et** le palier atteint, la recommandation et le
message qui en découlaient. L'application rejoue les tentatives restantes dans l'ordre et reconstruit
l'état exact d'avant l'essai — les élèves retrouvent la recommandation qu'ils auraient eue. Un message
déjà lu par un élève reste marqué comme lu, et rien ne se rouvre sans raison.

Le bouton **« Effacer les résultats »**, en tête de fiche, fait le ménage d'un coup : toutes les
tentatives, tous les paliers atteints, toutes les recommandations. Il n'apparaît que s'il y a
quelque chose à effacer. Sur une fiche de confrontation, il efface les scores des oppositions en
gardant les appariements.

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

- **Les rencontres sont organisées en tours, à raison d'un match par terrain disponible.** Deux
  terrains par défaut, donc deux matchs simultanés ; le champ **Terrains** de la barre d'outils
  change cela en un geste, et le découpage en tours se refait aussitôt. Les équipes qui ne jouent
  pas pendant un tour sont affichées comme **observatrices** — ce sont elles qui tiennent les
  feuilles de relevés et arbitrent. Le tour de chaque match reste modifiable à la main ; si tu
  places dans un tour plus de matchs qu'il n'y a de terrains, le surnuméraire bascule au suivant.

  Le nombre de terrains se règle à trois endroits, du plus général au plus précis : **Réglages → Le
  gymnase** donne la valeur par défaut de toute l'application ; le champ **Terrains** de l'onglet
  Matchs (ou de « Modifier les relevés ») la redéfinit pour **cette leçon** ; la fenêtre d'une
  confrontation d'échauffement la redéfinit pour **cette fiche**. Le jour où le gymnase est coupé en
  deux, une seule case à changer.
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
- **Relevés de tous les matchs d'un tour en une seule fenêtre** : le bouton « Relevés des N matchs »,
  dans l'en-tête du tour, ouvre les rencontres du tour l'une sous l'autre — scores et compteurs. Les
  équipes observatrices rendent leur feuille en même temps, tu saisis tout d'un bloc et tu valides
  une fois. Un récapitulatif en bas montre en direct ce qui sera calculé (passes réussies, total
  des passes ratées interceptions comprises, pourcentage de réussite), pour repérer une erreur de
  relevé avant d'enregistrer.
- **Génération du tournoi, avec des choix** : le bouton « Générer le tournoi » ouvre une fenêtre
  d'options plutôt que de tout décider à ta place. Trois questions indépendantes.

  **Qui rencontre qui ?**

  | Formule | Effet |
  |---|---|
  | **Toutes contre toutes** | chaque équipe rencontre toutes les autres — le tournoi complet |
  | **Un tour simple** | chaque équipe joue une fois au plus : de quoi occuper une fin de séance |
  | **Je choisis les affiches équipe par équipe** | tu désignes toi-même les rencontres |

  Dans le troisième cas, tu sélectionnes une équipe et coches celles qu'elle doit rencontrer —
  avec « tout cocher » / « tout décocher » pour aller vite. Une affiche cochée d'un côté l'est
  forcément de l'autre : la Bleue contre la Verte, c'est la même rencontre que la Verte contre la
  Bleue. La liste des affiches retenues s'affiche en dessous, aux couleurs des équipes. On part du
  tournoi complet : on retire ce qu'on ne veut pas plutôt que de tout construire depuis une page
  blanche.

  **Combien de fois chaque affiche se joue-t-elle ?** — une fois (match aller seulement), deux fois
  (aller et retour), trois ou quatre. Au match retour, les deux équipes changent de côté sur la
  feuille, et tous les matchs aller se jouent avant d'entamer les retours.

  **Et deux cases à cocher** : *n'apparier que des équipes de même composition* — deux équipes non
  mixtes de sexe opposé ne se rencontrent pas, une équipe mixte rencontre tout le monde ; et
  *remplacer les matchs déjà créés*, à décocher pour ajouter des rencontres aux précédentes.

  L'option de composition n'apparaît que si la classe compte effectivement des équipes non mixtes
  des deux sexes — sinon elle n'aurait aucun effet, et l'application le dit ; elle disparaît aussi
  quand tu désignes les affiches toi-même, puisqu'elle agirait alors dans ton dos. Un **aperçu
  vivant** annonce, avant de valider, combien de matchs et combien de tours seront créés ; si les
  options choisies ne laissent aucune rencontre possible, il le signale au lieu de générer un
  tournoi vide.
- Ou match par match, à la main, en choisissant les deux équipes et le tour.
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

**La sortie est protégée par un code** (**2706** par défaut, modifiable dans Réglages → Mode kiosque).
Un espace créé avant ce changement et resté sur l'ancien code bascule automatiquement sur 2706 ;
un code que tu aurais personnalisé toi-même n'est jamais touché.
Un pavé numérique s'affiche : sans le code, la tablette reste en mode élève et personne ne va se
promener dans les notes de la classe. Le même code protège la réinitialisation d'une rencontre.

Trois modes :

- **Situations ou échauffement** : le bouton kiosque de chaque onglet n'ouvre que les fiches de
  cet onglet — pas question qu'un élève tombe sur la situation d'apprentissage alors qu'il en est
  encore à l'échauffement. L'élève touche son groupe → relit le message du passage précédent →
  choisit son palier (les boutons sont teintés de l'orange au vert foncé) → saisit son score →
  reçoit son retour automatique (texte et image) → passe la tablette.
  Un échauffement **en confrontation** saute tout cela : l'écran affiche directement les oppositions
  tour par tour, deux gros boutons `+1` par équipe, le vainqueur annoncé en tête de bloc et l'équipe
  au repos rappelée dans le titre du tour.
- **Statistiques de match** : les deux équipes **côte à côte sur un seul écran**, sans aucun
  défilement. Score en gros en haut de chaque colonne, gros bouton **« + But »** qui met
  le résultat à jour immédiatement, et trois compteurs `+` / `−` en dessous. Une légende
  rappelle qu'on ne compte pas une passe ratée en plus d'une interception.
  Depuis l'écran de choix, le bouton violet **« Relever les N matchs ensemble »** ouvre les
  rencontres du tour l'une sous l'autre, repérées « Terrain 1 », « Terrain 2 »… : les équipes
  observatrices se partagent une seule tablette et ne reviennent jamais en arrière. Chaque bouton
  agit sur son propre match — rien ne se mélange entre les terrains.
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

### Synchronisation ordinateur ↔ tablette (Realtime Database)

La configuration du projet Firebase **`eps-pasteur`** est intégrée à l'application : il n'y a
aucun code à coller. La base est une **Realtime Database hébergée en Europe** (`europe-west1`),
ce qui est le bon choix pour des données scolaires.

**Réglages → Synchronisation ordinateur ↔ tablette.** Sur chaque appareil, tu te connectes avec
**le même compte e-mail + mot de passe**. La première fois, « Créer le compte » ; ensuite,
« Se connecter ». Le mot de passe n'est jamais stocké par l'application — c'est Firebase qui
garde la session ouverte.

> **Pourquoi un compte et pas une connexion anonyme ?** La connexion anonyme attribue un
> identifiant différent à chaque appareil : ton ordinateur et ta tablette se retrouveraient dans
> deux espaces séparés, sans rien partager. Un compte, c'est un identifiant unique — donc un seul
> espace — et c'est aussi ce qui permet d'écrire des règles de sécurité qui tiennent.

#### À faire une seule fois dans la console Firebase

Sans ces deux réglages, la synchronisation ne fonctionnera pas — et surtout, **la base créée en
« mode test » est ouverte à tout le monde pendant 30 jours**.

1. **Authentication → Sign-in method** : activer **E-mail/Mot de passe**.
2. **Realtime Database → Règles** : remplacer tout par ceci, puis **Publier**.

```json
{
  "rules": {
    "espaces": {
      "$uid": {
        ".read": "auth != null && auth.uid === $uid",
        ".write": "auth != null && auth.uid === $uid"
      }
    }
  }
}
```

Ce que ces règles disent : chaque compte ne peut lire et écrire que son propre espace, et un
visiteur non connecté n'a accès à rien. Le bouton « Voir les règles à coller » dans les réglages
te les affiche avec un bouton de copie, pour ne pas avoir à revenir ici.

> Une clé d'API web Firebase n'est pas un mot de passe : elle identifie le projet et se retrouve
> forcément dans le code de n'importe quelle page web. **Ce sont les règles ci-dessus, et elles
> seules, qui protègent les données.** Publie-les avant de saisir la moindre donnée réelle.

#### Comment ça se comporte au quotidien

- **Le local reste la référence.** Tout est d'abord enregistré dans le navigateur ; la
  synchronisation vient par-dessus. Sans réseau — un gymnase, typiquement — l'application
  fonctionne normalement et rattrape son retard dès que la connexion revient.
- Un **voyant** dans la barre du haut indique l'état : synchronisé, envoi en cours, hors ligne,
  non connecté, erreur. Un clic dessus ouvre les réglages.
- L'envoi est **différé de deux secondes** après la dernière modification, pour ne pas
  bombarder le serveur pendant une saisie.
- **En cas de modification des deux côtés**, l'application ne tranche jamais toute seule : si des
  changements locaux existent, elle signale qu'un autre appareil a envoyé quelque chose et te
  laisse choisir entre « Envoyer mes données » et « Récupérer celles du serveur ». Si rien n'a
  bougé localement, la mise à jour distante s'applique d'elle-même avec un message discret.
- Les **images** des situations voyagent avec les données. Au-delà de 6 Mo de sauvegarde,
  l'envoi est refusé avec un message : allège les schémas.
- « Désactiver sur cet appareil » arrête la synchronisation sans rien effacer, ni en local ni
  sur le serveur.

Le projet peut être remplacé par un autre (section « Utiliser un autre projet Firebase ») si tu
changes d'établissement ou si tu veux un projet par classe.

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
js/cloud.js              synchronisation Realtime Database (compte enseignant)
js/views.js              classes, élèves, contraintes, fiabilité, réglages
js/bank.js               banque de situations, dossiers de variantes
js/training.js           échauffements, situations et paliers
js/eval.js               grilles, notes, auto-évaluation
js/lesson.js             présences, équipes, matchs
js/kiosk.js              mode tablette
js/main.js               routeur, actions, jeu de démonstration
build.js                 fabrique le fichier autonome (node build.js)
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
node test/check.js      # ridge, paliers, équipes, barème, interceptions, mode Niveau, effacement
node test/csv.js        # import de l'export d'appel du collège
npm i playwright && node test/browser.js   # parcours complet dans Chromium (58 vérifications)
node build.js           # régénère ultimate-eps-autonome.html après une modification
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
