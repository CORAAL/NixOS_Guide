![Header](./images/header-readme.png)

⚠️ Ce guide est en cours de rédaction. ⚠️

# 📘 À propos de ce guide

Ce guide est conçu pour vous accompagner durant vos premières heures avec NixOS. 
De par sa nature très différente de ce à quoi nous sommes habitués, NixOS a une courbe de progression très abrupte. 

En suivant ce guide, vous apprendrez: 

- 🔎 Où cherchez les informations, comment les interpréter.
- ![](./images/nixos-icon.png) Découvrir Nix et NixOS.
- 🗄️ Structurer votre configuration pour s'adapter à un plusieurs cas d'utilisations 

Parce que Nix et par extension NixOS sont en constante évolution, ce guide ne pourra pas tout aborder. 
Mon objectif est que vous soyez capable de vous débrouiller pour répondre à vos besoin.

💡 Sachez que ce guide, se divise en trois étapes. 

## Etape 1 

- 🛠️ Découvrir NixOS, comment le configurer et comment structurer sa configuration.

## Etape 2

- 🧩 Intégration de Flake, Home-Manager, NixOS-Hardware

## Etape 3 

- 🎯 Cas d'utilisations avancés (Homelab, Développement, Gaming)

Je vous recommande de suivre ce guide jusqu'à l'étape 2 au moins. 

Les étapes 1 et 2 sont pensés pour être suivis l'un après l'autre. 

Quant à l'étape 3, elle est pensé pour être standalone et peut donc être suivis à part.

> [!NOTE]
> Bien que l'étape 3 soit standalone, l'arborescence de travail des "dotfiles" est mise en place dès l'étape 1.
> 
> Ne soyez pas surpris si des chemins de fichiers que vous n'avez jamais vu sont utilisés.

# 🖋️ À propos des contributions

Si vous êtes intéressé pour contribuer, vous voudrez probablement lire [[CONTRIBUTING|CONTRIBUTING.md]] pour savoir par où commencer. 

# 📍 Roadmap 

Les éléments ci-dessous sont en préparation, notez que l'ordre des éléments ci-dessus ne réflète aucune priorité : 

- [ ] Installation manuelle 
    - [ ] Partitionnement EXT4, BTRFS, ZFS
    - [ ] Réinstallation à partir d'une configuration existante
- [ ] Comment fonctionne les options ? (analyse du code source des options)
- [ ] Comment écrire ses propres options à l'image de celle fournis dans nixos
- [ ] Intégrer sa configuration dans un flocon
- [ ] Intégrer Home-Manager en temps que module et standalone de NixOS
- [ ] Ecrire un environnement de développement avec un flocon
- [ ] Utiliser direnv
- [ ] Gérer les *secrets* via sops-nix (agenix ne sera pas utilisé).
- [ ] Gérer ses configurations avec git (branche, remote, merge, push, pull)
- [ ] Générer une image iso
- [ ] Déployer nixos sur un SBC (raspberry pi 3B+, zero, zero v2, 4, 5)
- [ ] Ajouter des tests à sa configuration
- [ ] Ajouter des conditions à sa configuration
- [ ] Configurer en détail son environnement utilisateur  (Gnome, XFCE, KDE)
- [ ] Compiler son kernel
- [ ] Compiler un paquet/système avec des options (comme le ferait gentoo avec les flags)
- [ ] Hardening
- [ ] Virtualisation avec MicroVM 
- [ ] Utiliser oci-containers, docker, podman, nixos-containers
- [ ] Rebuild un système nixos distant
- [ ] Rice son système avec Stylix
- [ ] NixOS-Anywhere + Disko
- [ ] Impermanence
- [ ] Specialisation
- [ ] Naviguer dans un flocon
- [ ] Nix repl
- [ ] Configurer un éditeur (neovim, vscode, helix)
- [ ] Mettre en place un homelab sous NixOS
    - [ ] Hardening 
    - [ ] Prévoir des sauvegardes
    - [ ] Chiffrement (luks)
    - [ ] Déployer des services
        - [ ] Reverse Proxy
        - [ ] Services docker/podman
        - [ ] Services Nixos-Containers
        - [ ] Services MicroVM
        - [ ] Services (liste) :
            - [ ] Adguard
            - [ ] Octoprint
            - [ ] Audiobookshelf 
            - [ ] Cloudflare-ddns
            - [ ] Forgejo
            - [ ] FreshRSS
            - [ ] Frigate
            - [ ] Home Assistant
            - [ ] HomeBox
            - [ ] Immich
            - [ ] Invidious
            - [ ] It-tools
            - [ ] Kiwix
            - [ ] Mealie
            - [ ] Pairdrop
            - [ ] Paperless
            - [ ] Radicale
            - [ ] Traefik
            - [ ] Authelia
            - [ ] Stirling-pdf
            - [ ] Vaultwarden

