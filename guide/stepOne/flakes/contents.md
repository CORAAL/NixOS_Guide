# Les Flocons 

[DOCUMENTATION](https://wiki.nixos.org/wiki/Flakes)

**Flake** est une fonctionnalité **expérimentale** de Nix qui débloque un large éventail de possibilités, tout en améliorant la reproductibilité et en simplifiant la gestion des channels. 

Même s'ils sont encore considérés comme expérimentaux, les flocons sont déjà stables, largement utilisé par la communauté et apportent une multitude de fonctionnalités intéressantes pour NixOS. 

Il y énormément de chose à dire à propos des flocons, c'est un véritable couteau suisse.

## Rôle d'un flocon 

Le rôle principal d'un flocon est de fournir une manière cohérente et reproductible de définir et de gérer les environnements de développement et les configurations systèmes. 

<!-- 
    TODO: 
    Voir si une illustration (un éventail avec des icones ?) ne serait pas plus digeste qu'un pavé?
-->

Voici quelques exemples: 

1. Gestion des dépendances: 
  Un flocon peut spécifier les dépendances nécessaire à un projet (bibliothèques, outils de développements, environnement d'exécutions) de manière isolés et reproductible. 

2. Configuration système: 
  Les flocons peuvent être utilisés pour définir la configuration complète d'un système NixOS.
  D'ailleurs, il ne se limite pas à un seul système, il est capable d'en gérer plusieurs.

3. Reproductibilité: 
  Les flocons garantissent que les environnements peuvent être recréés de manière identique sur différentes machines. 

## Squelette d'un flocon 

Un flocon est avant tout un fichier `nix` nommé **obligatoirement** `flake.nix`.
Il est composé de quatre attributs : 

```nix
{

  description = "";
  inputs = {};
  outputs = {};
  nixConfig = {};

}
```

### Les entrées 

Les entrées (**inputs**) sont des ressources externes, on parle de dépôts nixpkgs, git. 

### Les sorties

Les sorties (**outputs**) décrivent des actions réalisés à l'aide des entrées. 

> [!NOTE]
> Dans le cas de la configuration d'un système, on configure le système via des options (`outputs`) fournis par nixpkgs (`inputs`).
> Notez que sans la savoir, `configuration.nix` est en quelques sortes composé d'une entrée et d'une sortie implicite. 
> Le flocon ne fait que rendre les choses explicite.

## Comment les flocons facilite la gestion des channels ? 

Comme nous l'avons vu plus tôt dans un article précédent, les channels permettent d'indiquer à nix où chercher les paquets et les options. 

Pour gérer un channel, nous utilisons `nix-channel`, qui nous permet d'*ajouter*, *supprimer*, *éditer*, *mettre à jour* un ou des channel(s), comme on le ferait avec d'autres distributions. 
L'inconvénient de cette approche des channels est que nous n'avons aucun contrôle sur leur version en dehors de l'update. 

C'est ici que le flocon nous est utile. 

Dans un flocon, on indique dans une chaine de caractères l'url du dépôt (ex: `"github:nixos/nixpkgs/nixos-24.05"`). 
Nix ira chercher la dernière version disponible et écrira un fichier `flake.lock` contenant des informations sur la version du dépôt.

Ce fichier `flake.lock` restera figé jusqu'à ce que l'utilisateur décide de chercher les nouvelles versions de dépôts via `nix flake update`.

Un extrait de mon flake.lock concernant l'input `nixpkgs` : 

```lock
    "nixpkgs": {
      "locked": {
        "lastModified": 1725634671,
        "narHash": "sha256-v3rIhsJBOMLR8e/RNWxr828tB+WywYIoajrZKFM+0Gg=",
        "owner": "NixOS",
        "repo": "nixpkgs",
        "rev": "574d1eac1c200690e27b8eb4e24887f8df7ac27c",
        "type": "github"
      },
      "original": {
        "owner": "NixOS",
        "ref": "nixos-unstable",
        "repo": "nixpkgs",
        "type": "github"
      }
    },
```

Avec ces informations ainsi que notre flocon, nous avons la capacité dans le futur de réinstaller n'importe quelle version de notre système. 

> [!NOTE]
> Vous n'aurez jamais besoin d'éditer manuellement `flake.lock`, tout se fera via `nix flake update`.

> [!WARNING]
> Retenez que vous ne gérez plus les channels via `nix-channel` mais via les flocons.
> Lorsque vous utilisez un flocon, un fichier `flake.lock` est généré. 
> Ce fichier ne change pas tant que vous ne faites pas `nix flake update`, qui permet de chercher les nouvelles 
> versions des dépôts.
> `flake.lock` est important car il décrit quelle version des dépôts vous utilisez. 
> Si vous devez réinstaller votre machine, ce flake.lock vous permettra de restaurer cette exacte version du système. 
> Si cette version fonctionnait, votre système fonctionnera à coup sûr. 




## Travailler avec git et les flocons

Lorsque l'on a un flocon dans un dépôt git, il est important de noter que le flocon ne "suivra" que les fichiers
de travail git. 

En d'autres termes, vous devrez `git add` les fichiers du dépôt pour que le flocon les utilisent, auquel cas,
vous obtiendrez une erreur indiquant que le fichier n'existe pas. 

## Est-ce que je dois obligatoirement utiliser les flocons ? 

Non, loin de là. NixOS peut largement vivre sans flocons.

Cependant, vu les avantages qu'ils apportent en termes de gestion et de maintenance, nous allons les intégrer dans la suite du guide. 

## Peut-on suivre le reste du guide sans flocons ? 

Oui c'est possible. 

Les flocons dans notre cas rendent la gestion de channels bien plus facile à maintenir. 
Il y a quelques différences dans la façon d'appeler un module fournis par une source externe via les channels ou via 
les flocons. 

Quoi qu'il en soit, je me concentrerais uniquement sur les flocons, vous devrez faire vos recherches de votre côté.






<!-- TODO:
    - Déplacer ce qui suis dans l'article suivant
-->

## Les channels

L'une des premières fonctionnalités qui nous intéresse est la possibilité de gérer
déclarativement les channels. 

Qu'est-ce que ça veut dire ? 

Vous vous souveniez de comment passer d'un channel stable à unstable ? 

```bash
sudo nix-channel --add https://nixos.org/channels/nixos-unstable nixos
sudo nix-channel --update -v
sudo nixos-rebuild switch
```

Pour faire le chemin dans le sens inverse : 

```bash
sudo nix-channel --add https://nixos.org/channels/nixos-24.05 nixos
sudo nix-channel --update -v
sudo nixos-rebuild switch
```

Avec un flocon, c'est encore plus simple (il s'agit d'un extrait) :

```nix
{
  # [...]

  inputs = {
    nixpkgs.url = "github:nixos/nixpkgs/nixos-24.05";
  };

  # [...]
}
```

Pour passer à unstable, il suffit de modifier la fin de l'url (`nixos-24.05` -> `nixos-24.11`) puis de reconstruire nixos. 
Le principe est le même dans le sens inverse.

La migration d'une version stable à une autre (ou passage à unstable) est transparente. 

En utilisant les flocons, toutes les commandes `nix-channel` deviennent caduc (dans le sens où on passe par les
flocons pour les gérer, nous n'avons plus d'intérêt à les utiliser). 

## En quoi un flocon améliore la reproductibilité ? 

C'est très simple, après avoir écris un flocon, on rebuild le système. 
A cet instant précis, le flocon génère un nouveau fichier `flake.lock` qui contiendra des informations sur 
- les dépôts ajoutés dans le flocon
- la version de commit d'un dépôt 

Ce fichier `flake.lock` est très important car il contiendra des informations qui nous permettrons de reconstruire 
dans 6 mois, 1 an ou plus un système avec cette même version. 
Tant que vous ne faites pas `nix flake update`, flake.lock ne se mettra pas à jour avec les nouvelles versions de dépôts.

Sans flocon, nous sommes dans l'incapacité d'indiquer une version. 
Nous avons uniquement le choix de stable ou unstable. 
Si un jour vous deviez réinstaller votre système et qu'un paquet ne peut pas être construit, vous ne pourrez pas finir
l'installation. 

Avec les flocons, nous avons en plus de ces deux choix, la possibilité d'épingler n'importe quelle version. 
Lors d'une réinstallation, si vous disposiez d'un flocon, vous pourriez réutiliser une version spécifique où la construction fonctionnait. 

## Comment on met à jour

```bash
nix flake update
```

## L'organe d'un flocon 

Un flocon est composé de quatres attribues : 

```nix
{

  description = "blabla";
  inputs = {};
  outputs = {};
  nixConfig = {};

}
```








