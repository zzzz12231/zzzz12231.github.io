# Workflow Git + Amplify
Source : https://docs.aws.amazon.com/amplify/latest/userguide/team-workflows-with-amplify-cli-backend-environments.html

## Suppositions
- Plusieurs environnements amplify(prod et dev par ex)
- Un workflow git de type : 
	- master
	- develop (seulement  modifié via des PR)
	- feature/* : pour les différentes features en cours de développement
	- potentiellement d'autres branches comme release/\*, bugfix/\* 

## Commandes utiles
- `amplify init` : initialisation d'un projet avec amplify
- `amplify env add` : ajout d'environnement amplify
- `amplify push` : mise à jour du backend distant avec les derniers changements locaux
- `amplify publish` : déploie les ressources statiques vers AWS S3
- `amplify env checkout env_name` : changer d'environnement amplify


## Workflow dev pré-requis
- Dans la console Amplify, avoir connecté son repository git au projet Amplify (tab *Frontend environments*)
- Associer les différents environnements backend avec des branches git : par exemple l'environnement *prod* avec la branche *master*
**NB** : dans ce cas, toute modification du code de la branche *master* entrainera une mise à jour de l'application à l'URL `https://master.appid.amplifyapp.com`.

## Workflow
- Dans le cas d'une nouvelle feature : créer la branche puis se connecter à l'environnement de dev
> git checkout -b \<branchname>
> amplify env checkout dev
> amplify add api
> ...
> amplify push

- Une fois le développement fini : créer une PR
> git commit -am 'Decentralized internet v0.1'
> git push origin <branchname>

- Pour visualiser les changements apportés par la feature : créer une branche "amplify" qui va permettre de déployer le résultat de la feature en ligne 
> aws amplify create-branch --app-id <appid> --branch-name <branchname>
> aws amplify start-job --app-id <appid> --branch-name <branchname> --job-type RELEASE

**NB1** : Nécessite d'avoir installer AWS CLI (différent de Amplify CLI).
**NB2** : <appid> est disponible à AppSettings > General > AppARN: *arn:aws:amplify:<region>:<region>:apps/<appid>*
**NB3** : la feature sera accessible à l'URL : *https://<branchname>.appid.amplifyapp.com*

- Si la PR est validée : merger la branche feature dans develop
> git checkout develop
> git merge <branchname>
> git push

Cela va entrainer un build qui va mettre à jour le frontend et le backend de l'application, dont le résultat sera disponible à l'URL suivante : _https://dev.appid.amplifyapp.com_ (en supposant qu'on a lié plus tôt l'environnement amplify dev à la branche develop).

- Supprimer les différentes branches de Git, Amplify et l'environnement de dev ? :
> git push origin --delete <branchname>
> aws amplify delete-branch --app-id <appid> --branch-name <branchname>
> amplify env remove dev


## Questions 
- Devons-nous chacun avoir un env local propre ?
- Peut-être plutôt un env par feature ? quid de la différence entre local et distant ?
- Quels utilisateurs AWS utiliser ?
