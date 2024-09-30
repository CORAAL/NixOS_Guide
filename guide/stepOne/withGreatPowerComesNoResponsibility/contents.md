# Un grand pouvoir n'implique aucune grande responsabilités

Nous venons de modulariser la configuration et maintenant nous allons respirer et admirer les pouvoir de nix. 

L'un des attraits de Nix et sa capacité à installer/désinstaller des programmes sans laisser la moindre trace.

> [!NOTE]
> Pour être plus précis, nix ne touche **pas** à `~/`, ce qui inclus `~/.config` également. 
>
> Donc tout ce qui n'est pas `~/` est supprimé. 


Prenons un exemple, un **environnement de bureau**. 

Nous allons passer de **Gnome** à **KDE** en éditant `/etc/nixos/modules/nixos/optional/desktop/default.nix`.

Après avoir procédé à notre modification (ci-dessous), *Gnome* disparaîtra de la machine sans laisser de trace.

- [DOCUMENTATION GNOME](https://wiki.nixos.org/wiki/GNOME)
- [DOCUMENTATION KDE](https://wiki.nixos.org/wiki/KDE#Plasma_6)

Editez : 

```nix
 services.xserver = {
    enable = true;
    displayManager.gdm.enable   = true;
    desktopManager.gnome.enable = true;
 };
```

Pour : 

```nix
services = {
  xserver.enable = true;
  displayManager = {
      sddm = {
        enable = true;
        wayland.enable = true;
      };
  };
  desktopManager.plasma6.enable = true;
};
```

> [!NOTE]
> Notez que dans le premier cas, les options `displayManager` et `desktopManager` sont dans `services.xserver` alors que dans le second cas :
> `services`
> 
> La raison est simple: ils ont été déplacés (pas tous, c'est en cours).

```bash
sudo nixos-rebuild switch
```

Redémarrez. 

Vous êtes maintenant sur KDE et GNOME a disparu. Cool non ? :D  

Vous n'êtes pas satisfait ? 

Retournez en arrière avec `sudo nixos-rebuild switch --rollback` puis redémarrez. 

Vous préférez tester le résultat dans une machine virtuelle (oui nous en avions déjà parlé, c'est l'occasion rêvé de voir ce que ça donne) ?

<!-- TODO: Remplacer le code ci-dessous par un lien vers la page du guide dédié -->

```bash
sudo nixos-rebuild build-vm && ./result/bin/run-nixos-vm
```

Quelque soit votre choix final, vous voudrez probablement nettoyer le store de tout les orphelins. 

<!-- TODO: Remplacer le code ci-dessous par un lien vers la page du guide dédié -->

```bash
sudo nix-collect-garbage -d
```
