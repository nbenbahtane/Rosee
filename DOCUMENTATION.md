# 🌸 Rosée — Documentation complète

Application personnelle de finances et d'organisation, conçue pour iPhone, 100 % locale et 100 % gratuite.

---

## Phase 0 · Le choix d'architecture (et pourquoi)

Vous vouliez une app sur iPhone, à coût strictement nul, sans compte développeur Apple. Une app native Swift est incompatible avec cette contrainte : sans le compte à 99 $/an, une app installée via Xcode expire au bout de **7 jours** et exige un Mac pour être re-signée. La bonne réponse technique est donc une **PWA (Progressive Web App)** : une application web que Safari installe sur votre écran d'accueil. Une fois installée, elle s'ouvre en plein écran comme une app native, avec sa propre icône, **fonctionne hors-ligne** grâce à un service worker, et stocke toutes vos données **localement sur l'iPhone** (localStorage) — rien ne transite jamais par un serveur, aucune base cloud, aucun compte.

Le seul élément « en ligne » est l'hébergement du fichier de l'app lui-même (le code, pas vos données), via **GitHub Pages**, gratuit et illimité pour ce type d'usage. Votre iCloud+ 2 To sert à une seule chose, mais elle est précieuse : y ranger vos **sauvegardes .json** exportées depuis l'app.

**Fichiers du projet :**

| Fichier | Rôle |
|---|---|
| `index.html` | Toute l'application : structure, design, logique (≈ 860 lignes, aucune dépendance à l'installation) |
| `manifest.json` | Déclare l'app installable (nom, icône, mode plein écran) |
| `sw.js` | Service worker : met tout en cache pour le fonctionnement hors-ligne |
| `icon.png` | Icône fleur pastel affichée sur votre écran d'accueil |

Les bibliothèques d'export (SheetJS pour Excel, jsPDF pour PDF) sont chargées depuis un CDN et mises en cache dès la première visite, donc les exports fonctionnent ensuite hors-ligne.

---

## Phase 1 · Installation (une seule fois, ~10 minutes, depuis l'iPhone ou un ordinateur)

1. Créez un compte gratuit sur **github.com** si vous n'en avez pas.
2. Créez un nouveau dépôt (bouton **New repository**), nommez-le par exemple `rosee`, cochez **Public** (le code est public, vos données ne s'y trouvent jamais — elles restent sur votre téléphone). Si vous préférez, un dépôt **privé** fonctionne aussi avec GitHub Pages sur les comptes gratuits depuis peu ; sinon Public suffit, il n'y a rien de sensible dedans.
3. Dans le dépôt, cliquez **Add file → Upload files** et déposez les 4 fichiers : `index.html`, `manifest.json`, `sw.js`, `icon.png`. Validez avec **Commit changes**.
4. Allez dans **Settings → Pages**, section *Build and deployment* : Source = **Deploy from a branch**, Branch = **main** / **(root)**, puis **Save**.
5. Attendez 1 à 2 minutes. Votre app est en ligne à l'adresse `https://votrepseudo.github.io/rosee/`.
6. Sur l'iPhone, ouvrez cette adresse dans **Safari** (pas Chrome), touchez le bouton **Partager** (carré avec flèche) puis **Sur l'écran d'accueil** → **Ajouter**.
7. L'icône 🌸 Rosée apparaît. Ouvrez-la une première fois **avec internet** : le service worker met tout en cache. À partir de là, l'app fonctionne en mode avion.

**Mise à jour de l'app** : remplacez simplement `index.html` sur GitHub ; à la prochaine ouverture connectée, la nouvelle version se charge. Vos données ne sont pas touchées (elles vivent dans le téléphone, pas dans le fichier).

**Alternative sans GitHub** : si vous avez ponctuellement accès à un Mac/PC sur le même Wi-Fi, `python3 -m http.server` dans le dossier suffit pour installer la PWA une fois — mais GitHub Pages est plus simple et permet les mises à jour.

---

## Phase 2 · Premier paramétrage (5 minutes dans l'app)

À la première ouverture, faites trois choses. D'abord, sur l'Accueil, touchez **gérer** à côté de « Mes 4 banques » : renommez les banques avec vos vrais noms (ex. Boursorama, Revolut…) et saisissez le **solde réel actuel** de chaque compte — c'est le point de départ de tous les calculs. Ensuite, onglet **Budgets → catégories** : fixez vos budgets mensuels par catégorie variable et choisissez avec le bouton 🎀 votre catégorie « plaisir » (celle qui recevra le bonus +5 %). Enfin, onglet **Plus**, tout en bas : renseignez votre **taux horaire net** (salaire net mensuel ÷ heures travaillées), utilisé par la wishlist, et créez vos **charges fixes** dans l'onglet Opérations (loyer, crédit, assurances, abonnements) avec leur jour de prélèvement.

---

## Phase 3 · Guide d'utilisation

### Les 4 banques et les flux ⤳
Chaque banque est une carte pastel avec son solde en temps réel. Toucher une carte affiche l'historique de cette banque. Pour un **virement inter-banques**, touchez ＋ puis l'onglet **Flux ⤳** : choisissez banque de départ, banque d'arrivée, montant. Un flux déplace l'argent (le solde des deux banques change) mais **n'est jamais compté comme une dépense** — vos jauges de budget et graphiques restent justes. Dans la liste des opérations, un flux s'affiche avec un ruban lilas « Banque A → Banque B ».

### Charges fixes automatiques 🔁
Chaque charge fixe (nom, montant, banque, jour du mois) est appliquée **automatiquement** : à la première ouverture de l'app après son jour d'échéance, la dépense est créée toute seule, marquée « 🤖 auto ». Une charge peut être mise en pause (⏸) sans la supprimer.

### Budgets, prévisions et alertes 🎯
Chaque jauge montre le dépensé sur le budget, avec un petit **trait vertical** indiquant où vous devriez en être à ce stade du mois. Le moteur de prévision projette votre rythme : vert 🌿 si tout va bien, orange 🧡 si le rythme est élevé, **rouge** si la projection annonce un dépassement avant la fin du mois — avec le montant projeté affiché. L'Accueil affiche le **reste à vivre** global et son équivalent par jour restant.

### Crédits & échéances 🏛️
Chaque crédit affiche un compte à rebours visuel : une petite case par mensualité, qui se remplit au fil des paiements. Le bouton **Payer la mensualité** crée la dépense sur la bonne banque et décrémente le compteur. Dernière mensualité = confettis 🎉.

### Enveloppes, CTO & patrimoine 🌱
Les enveloppes sont des cagnottes virtuelles à objectif (voyage, achat) avec pourcentage de progression. Le **CTO** se suit par pointages : à chaque fois, saisissez le total investi et la valeur actuelle ; l'app calcule la performance et trace la courbe. Le **patrimoine physique** (or, montres, bijoux) est un registre valorisé, réutilisé par le calculateur de Zakat.

### Zakat 🌙
Le module additionne les actifs que vous cochez (soldes bancaires, enveloppes, CTO, or), compare au **nisab** (85 g d'or × prix du gramme que vous renseignez) et calcule les **2,5 %** dus si le seuil est franchi. Ne cochez que les actifs détenus depuis une année lunaire complète (hawl). Le calcul est indicatif — en cas de doute, vérifiez auprès d'une personne de science.

### Wishlist anti-impulsion 💝
Un coup de cœur ? Ajoutez-le : l'app affiche immédiatement son **vrai coût en heures de travail** et le **verrouille 48 h ou 7 jours**. Pendant la réflexion, impossible de décider. Après le délai, deux boutons : « Je l'achète » (qui pré-remplit la dépense) ou « J'abandonne » — et là, l'app célèbre l'argent économisé.

### Gamification & objectifs de vie 🎀
En fin de mois, touchez **Clôturer le mois** : si tous les budgets variables sont tenus, le mois suivant démarre avec **+5 % sur votre budget plaisir** (badge « bonus 🎀 » visible sur la jauge). Côté hygiène de vie : créez des objectifs (palier de poids, séances de sport…) avec un montant de récompense. Valider un objectif **débloque ce montant** dans votre enveloppe spéciale 🎁 Récompense — vous ne pouvez y piocher que ce que vos objectifs ont débloqué. Provisionnez cette enveloppe comme les autres.

### Exports & sauvegardes 💾
Onglet Plus : **Excel (.xlsx)** avec 7 feuilles (transactions, budgets, banques, enveloppes, crédits, patrimoine, wishlist), **PDF** de synthèse, et **sauvegarde .json** complète. Rangez régulièrement le .json dans **iCloud Drive** (vos 2 To) : c'est votre assurance-vie de données. La restauration se fait en un clic depuis le même écran.

---

## Phase 4 · Architecture technique (pour comprendre ou modifier)

**Modèle de données** — un objet unique `S`, sérialisé dans `localStorage` sous la clé `rosee_v1` :

| Clé | Contenu |
|---|---|
| `banks` / `initBal` | Les 4 banques (nom, icône, dégradé) et leurs soldes initiaux |
| `cats` | Catégories typées `rev` (revenu), `fix` (charge fixe), `var` (variable, avec budget) |
| `tx` | Transactions : `{date, type: dep\|rev\|vir, bankId, toBankId?, catId?, amount, note, auto?}` |
| `fixed` | Modèles de charges récurrentes avec la liste des mois déjà appliqués |
| `credits`, `envs`, `cto`, `assets`, `wishlist`, `goals` | Les modules respectifs |
| `months` | Historique des clôtures (`kept: true/false`) pilotant le bonus +5 % |
| `rewardUnlocked` / `rewardSpent` | Comptabilité du droit de piocher dans l'enveloppe Récompense |

**Principes clés** — Les soldes ne sont jamais stockés : ils sont **recalculés** à chaque affichage à partir du solde initial et de toutes les transactions (`balance()`), ce qui rend l'app incorruptible aux incohérences. Les flux (`type: 'vir'`) débitent une banque et créditent l'autre mais sont exclus de tous les agrégats de dépenses. La prévision (`paceInfo()`) compare le dépensé à la fraction du mois écoulée et projette linéairement en fin de mois. Le moteur de charges fixes (`applyFixedCharges()`) tourne à chaque ouverture : idempotent grâce au marquage des mois appliqués.

**Confidentialité** — Aucune donnée ne quitte l'appareil. Pas d'analytique, pas de compte, pas de requête réseau en dehors du chargement initial du code et des polices/bibliothèques (mises en cache).

---

## Phase 5 · Limites connues & pistes d'évolution

Les données vivent dans le stockage de Safari : si vous **supprimez l'app de l'écran d'accueil** ou effacez les données de site Safari, elles disparaissent — d'où l'importance des sauvegardes .json dans iCloud. iOS peut théoriquement purger le stockage d'un site jamais ouvert pendant des mois ; ouvrir l'app régulièrement (ce qui est le but !) l'en protège. Pistes futures si l'envie vous prend : graphiques d'évolution mensuelle, Face ID via WebAuthn, rappels de clôture, saisie par photo de ticket.

Belle route avec Rosée 🌸
