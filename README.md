# Git branch

Découvrons un peu les branches et les Merge Requests ( MR ).

## Exercice 1

En travaillant à plusieurs sur un projet, il est fréquent de travailler sur des branches. Ou plutôt, c'est une mauvaise pratique de travailler directement sur la branche main. 

Une première pratique, souvent vue sur des projets étudiants mais déconseillé en règle générale, est de créer une branche par développeur. L'illusion étant que nous n'aurons plus de conflits de merge. 


```bash
git clone git@gitlab.com:docusland-courses/versionning/git-branch.git
cd git-branch
git switch --create branche-de-marcel
echo $null > marcel.md
git add marcel.md
git commit -m "add marcel.md"
git push --set-upstream origin branche-de-marcel
```

Nous avons alors le dépôt suivant : 
```mermaid
gitGraph
    commit id:"Initial commit"
    branch branche-de-marcel
    checkout branche-de-marcel
    commit id:"add marcel.md"
```

Faites celà sur votre ordinateur. 
> Vous pouvez voir les branches locales sur votre ordinateur en faisant git branch


Lorsque vous basculez d'une branche à l'autre, par le biais de la commande `git switch <nom-de-la-branche>`, vous pourrez voir apparaître et disparaitre ce fichier.

Ajoutez du contenu dans le fichier marcel.md
```bash
echo "Marcel est trop fort" > marcel.md
git add marcel.md
git commit -m "Ajout d'une vérité"
git push
```

```mermaid
gitGraph
    commit id:"Initial commit"
    branch branche-de-marcel
    checkout branche-de-marcel
    commit id:"add marcel.md"
    commit id:"Ajout d'une vérité"
```

Maintenant que marcel a bien travaillé, il est temps de fusionner son travail dans la branche principale. 
```bash
git switch main
git merge branche-de-marcel
git push
```

```mermaid
gitGraph
    commit id:"Initial commit"
    branch branche-de-marcel
    checkout branche-de-marcel
    commit id:"add marcel.md"
    commit id:"Ajout d'une vérité"
    checkout main
    merge branche-de-marcel
```

Ici, la branche `branche-de-marcel` a été versionnée chez gitlab. Nous aurions pu ne jamais l'envoyer en ligne. ( jamais faire de push ) Cette branche aurait simplement été stockée sur votre ordinateur. 

Si une branche locale ne vous sert plus, vous pouvez la supprimer: 

```bash
git branch --delete branche-de-marcel
```

> A vous de trouver comment supprimer une branche distante :)

## Exercice 2
Après la sortie de git, un modèle de branches nommé "git flow" a été imaginé par M. Vincent Driessen. Il s'agit d'une première standardisation des branches qui a été mis en place dans de nombreuses entreprises. Vous trouverez une documentation [plus détaillée ici en anglais](https://nvie.com/posts/a-successful-git-branching-model/)

Le voici : 

```mermaid
gitGraph TB:
    commit id:"Initial commit"
    branch develop
    commit
    branch feature
    commit
    commit
    checkout develop
    merge feature
    commit
    branch feature-2
    commit
    checkout develop
    merge feature-2
```
Détail des branches : 
 - `main` : Branche des versions validées et tagguées
 - `develop`: Branch a partir de laquelle tout dev travaille, en constante évolution
 - `feature` : Branche partant de develop permettant d'implémenter une fonctionnalité. Ex : feature-user-can-log-in

Une fois que la branche develop semble satisfaisante, on la livre sur une branche release, validée par le client. Si ce dernier valide, le tout est migré sur la branche main avec un tag.

```mermaid
gitGraph:
    commit
    branch release
    branch develop
    checkout develop
    commit
    commit
    checkout release
    merge develop
    commit id:"Bump version 0.1"
    checkout main
    merge release tag:"v0.1"
```

Reproduisez ce modèle de branch manuellement sur un projet solo.
Vous aurez besoin des commandes suivantes : 
- `git add` : Ajout d'un fichier dans le prochain commit
- `git commit` : Sauvegarde d'un état
- `git branch` : Lister les branches / Créer une branche
- `git checkout` : Bascule sur une branche ou un commit
- `git checkout -b` : Créé une branche et bascule dans cette dernière
- `git switch` : Bascule vers une branche
- `git merge` : Fusionner deux branches
- `git tag` : Ajoute un tag sur un commit

> Si celà vous intéresse, un outil a été implementé pour réaliser automatiquement ces branches. Il s'agit de [git-flow](https://danielkummer.github.io/git-flow-cheatsheet/)

## Exercice 3

Cette complexité des branches est très pratique en entreprise mais lorsque vous réalisez des projets en école vous n'avez pas besoin d'utiliser un modèle aussi complexe pour le processus de livraison. Toujours est-il que prenez l'habitude de ne pas travailler sur la branche `main` mais de travailler sur la branche `develop`. Et utilisez les branches features pour les projets en groupe pour ne pas impacter vos collègues. **N'oubliez pas, le jour de la livraison au formateur de tout merger sur la branche main !**

Les merge de branches sont monnaie courante losqu'on utilise git. Et contrairement à ce qui a été évoqué lors de l'exercice 1, l'utilisation de branches ne prémunit en aucun cas des conflits de merge.
Les outils tels que gitlab ou github permettent de gérer la fusion des branches par une interface graphique. Celà s'appelle des "Merge Requests".

Le concept est simple, en équipe, vous n'avez pas toujours les droits pour fusionner sur la branch `develop` ou `release`, mais vous pouvez faire une merge request et assigner cette merge request à une personne habilitée. Cette dernière peut alors regarder votre code et appliquer ( ou refuser ) le merge.

Pour celà, au sein du dépôt, allez dans `Code > Merge Request`, spécifiez la branche que vous souhaitez merge ainsi que la branche destination. 
Vous aurez alors la possibilité de rajouter de plus amples détails tels que : 
 - qui peut réaliser le merge.
 - une description sur les tâches réalisées
 - squash des commits
 - ...

Réalisez un petit projet simple avec un binôme afin de voir comment s'utilise les merge requests. Lors de vos tests faites deux cas de figure : 
 - une merge requests où il y a eu plusieurs commits de réalisés. 
 - une merge request qui mène à un conflit de merge.

Lors de vos tests, amusez-vous à refuser des MR, à en accepter d'autres, afin de bien éprouver le système.