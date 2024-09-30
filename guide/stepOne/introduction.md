# Introduction à NixOS

NixOS est une distribution Linux qui se distingue par sa manière unique de gérer la configuration du système. 
Là où d'autres distributions reposent sur un système de gestion des paquets traditionnels, NixOS utilise Nix.

Nix révolutionne la gestion du système en proposant une approche différente pour la configuration et la gestion des paquets.

1. La configuration déclarative du système.

Alors que les autres distributions linux nous ont habitués à modifier des fichiers de configurations pour configurer le système,
Nix regroupe **tout** dans un fichier unique. Un fichier pour les contrôler tous. 

De la configuration des paquets à l'installation de drivers ou la configuration du réseau, tout ce fait depuis ce fichier.

Cela contraste avec l’approche traditionnelle, dite "impérative", qui peut être résumée ainsi :
"J'installe et je configure."

Avec Nix :
"Je décris à Nix ce que je veux, comment cela doit être configuré, et il s’occupe du reste."

2. La reproductibilité 

L’un des avantages clés de Nix est sa capacité à reproduire à l’identique un système à partir de la même configuration. 
Cela signifie que vous pouvez recréer à l'identique le même environnement sur une autre machine ou après une réinstallation, sans erreurs ni incohérences.

3. Isolation et réversibilité

Nix ne suit pas la convention **FHS** (**F**ilesystem **H**ierarchy **S**tandard) couramment utilisée dans les systèmes Linux, 
où l’on trouve des répertoires comme /bin et /usr. 
À la place, Nix utilise une arborescence propre dans `/nix/store`, où chaque programme est isolé dans son propre espace.

Grâce à cette isolation, il n’y a plus de conflits entre bibliothèques ou versions de logiciels, 
et vous pouvez conserver plusieurs versions de n'importe quel paquet sans souci.

De plus, chaque fois que vous appliquez une configuration Nix, une nouvelle version du système est générée. 
Vous avez alors la capacité de basculer entre différentes versions du système à tout moment, ce qui permet de revenir en arrière en cas de bétise. 

4. Les environnements de développement

Un autre avantage de l’approche Nix est la possibilité de créer des environnements temporaires pour le développement.
À partir d'un simple fichier Nix, vous pouvez lancer un environnement de travail contenant des paquets spécifiques, 
sans que ceux-ci n'affectent le système global.

Par exemple, si vous êtes développeur Rust, vous pouvez configurer un environnement Rust dédié. 
Une fois votre travail terminé, l’environnement disparaît et les outils Rust ne sont plus présents sur la machine, sans laisser de traces.

5. NixOS et ces modules

Comme mentionné plus haut, NixOS se gère via un fichier de configuration. 
Un des points forts de NixOS est son système de modules, 
qui propose des composants prêts à l’emploi pour configurer facilement des services ou applications comme Docker, Nginx, ou encore des environnements de bureau. 
Ces modules simplifient l'intégration et la gestion des services sur votre système.
