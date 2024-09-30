# Encore de l'organisation...

Nous avons analysé ensemble le fichier de configuration. 
Nous allons maintenant structurer cette dernière (oui encore).

## Organiser nos dotfiles

Vous êtes libre de suivre ou non ce qui suis, sachez que cette structure aura un intérêt pour la suite lorsque nous
ajouterons d'autres outils. 

> [!IMPORTANT]
> Ce que vous devez savoir est que nous allons scinder en plusieurs fichiers la configuration de sorte à organiser nos configurations. 
> 
> Cette structure permettra d'accueillir **Home-Manager**, les **flocons** et d'autres outils et de réutiliser chacune des configurations
> pour plusieurs appareils en "même temps". 

Oui, un répertoire de fichiers nix pour gérer plusieurs appareils, allant du simple bureau au serveur.

### Quelle structure ?

Sans rentrer dans les détails, nous allons scinder dans différents répertoires les options. 
Nous aurons d'un côté les options nixos dite `core` qui seront présente sur **TOUTES** les machines si vous en utilisez plusieurs et 
de l'autre les options `optional` qui seront importés ou non sur vos appareils.

Dans chacun de ces répertoires nous organiserons les options dans des dossiers par "fonction".

### Déplaçons le boot-loader 

Ouvrez de nouveaux `configuration.nix`. 

Vous devriez avoir quelque chose de similaire à : 

```nix
{ config, pkgs, ... }:

{

  imports = [
    ./hardware-configuration.nix
  ];

  boot.loader = {
    systemd-boot.enable = true;
    efi.canTouchEfiVariables = true;
  };

  networking = {
    hostName = "nixos";
    networkmanager.enable = true;
  };

  time.timeZone = "Europe/Paris";

  i18n = {
    defaultLocale = "fr_FR.UTF-8";
    extraLocaleSettings = {
      LC_ADDRESS        = "fr_FR.UTF-8";
      LC_IDENTIFICATION = "fr_FR.UTF-8";
      LC_MEASUREMENT    = "fr_FR.UTF-8";
      LC_MONETARY       = "fr_FR.UTF-8";
      LC_NAME           = "fr_FR.UTF-8";
      LC_NUMERIC        = "fr_FR.UTF-8";
      LC_PAPER          = "fr_FR.UTF-8";
      LC_TELEPHONE      = "fr_FR.UTF-8";
      LC_TIME           = "fr_FR.UTF-8";
    };
  };

  services.xserver = {
    enable = true;
    displayManager.gdm.enable   = true;
    desktopManager.gnome.enable = true;
    xkb = {
      layout  = "fr";
      variant = "mac"; 
    };
  };

  console.keyMap = "fr";

  services.printing.enable = true;

  hardware.pulseaudio.enable = true;

  security.rtkit.enable = true;

  services.pipewire = {
    enable            = true;
    alsa = {
      enable       = true;
      support32Bit = true;
    };
    pulse.enable      = true;
  };

  users.users.user = {
    isNormalUser = true;
    description = "user";
    extraGroups = [ "networkmanager" "wheel" ];
    packages = with pkgs; [];
  };

  programs.firefox.enable = true;

  nixpkgs.config.allowUnfree = true;

  environment.systemPackages = with pkgs; [];

  system.stateVersion = "24.05";

}
```

> [!WARNING]
> Bien, pensez à faire régulièrement des sauvegardes. 
>
> Si vous utilisez **git**, il est possible que les nouveaux fichiers ne soit pas 
> "visible" par nix. 
> Sachez que ce n'est pas un bug mais une feature (oui oui). 
> Il vous suffit de faire `git add ./chemin/vers/le/nouveau/fichier.nix` pour qu'il soit visible. 
> Si vous n'utilisez pas git, ignorez ça. 

A partir de maintenant, pour chaque instruction, sauf mention contraire, je supposerais que nous sommes dans le répertoire
`/etc/nixos`. 

1. Créez un dossier `modules` : 
2. Créer à l'intérieur de celui-ci un répertoire `nixos` puis deux autres répertoires `core` et `optional`. 

En une commande : 

```bash
sudo mkdir -p ./modules/nixos/{core,optional}
```

3. Depuis `modules/nixos/core`, ajoutez un répertoire `bootloader`. 

```bash
sudo mkdir -p ./modules/nixos/core/bootloader
```

4. Créer à l'intérieur un fichier `default.nix`

```bash
sudo touch ./modules/nixos/core/bootloader/default.nix
```

5. Retournez dans `configuration.nix` et récupérez les lignes : 

```nix
boot.loader = {
  systemd-boot.enable = true;
  efi.canTouchEfiVariables = true;
};
```

6. Ajoutez-les dans `modules/nixos/core/bootloader/default.nix`

7. Ajustez le fichier pour qu'il ressemble à : 

```nix
{
    boot.loader = {
      systemd-boot.enable = true;
      efi.canTouchEfiVariables = true;
    };
}
```

8. Retournez dans `configuration.nix` et ajustez : 

```nix
{ config, pkgs, ... }:

{

  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader # <-- On a ajouté le chemin vers le nouveau fichier
  ];

  # Les lignes bootloader étaient ici
  # Vous pouvez les supprimer ou les "commenter" avec "#".

  networking = {
    hostName = "nixos";
    networkmanager.enable = true;
  };
  # ....
```

> [!IMPORTANT]
> Si vous êtes attentif, vous vous demandez pourquoi nous n'avons pas écris le chemin complet vers `default.nix`.
> 
> Par défaut, lorsque l'on donne un chemin à importer, nix cherche toujours un fichier `default.nix`
> De ce fait, le simple fait d'indiquer le répertoire parent est suffisant. 
> Nous aurons l'occasion de profiter de `default.nix` à l'avenir pour une autre utilisation.
> Ecrire `./modules/nixos/core/bootloader/default.nix` aurait également fonctionné.

> [!WARNING]
> Pour être clair, 
> 
> Si l'on avait donné un chemin complet avec un nom de fichier différent de `default.nix`, il n'aurait pas cherché à importer `default.nix`, 
> uniquement ce que l'on a indiqué. 

Vous voyez ? Rien de bien compliqué. 
Nous avons déplacé des lignes vers un autre fichier. 
On parle ici de "modules". 

Essayez de reconstruire : 
```nix
sudo nixos-rebuild switch
```

Si vous n'avez pas fait d'erreur, tout devrait fonctionner. 
Si vous utilisez git, encore une fois référez-vous à l'avertissement fournis ci-dessus. 

### Déplaçons les options réseaux

Copiez les lignes suivantes : 

```nix
  networking = {
    hostName = "nixos";
    networkmanager.enable = true;
  };
```

Déplacez-les dans un nouveau fichier (et répertoire) `./modules/nixos/core/network/default.nix`

```nix
{

  networking = {
    hostName = "nixos";
    networkmanager.enable = true;
  };

  # Vous souhaitez peut-être rajouter la ligne suivante, je vous invite à regarder pourquoi cette option pourrait vous plaire : 
  boot.initrd.systemd.network.wait-online.enable = false;

}
```

Depuis `configuration.nix` : 

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
  ];
```

Reconstruisez. 

> [!IMPORTANT]
> Je ne l'ai pas mentionné jusqu'à présent, je suppose que vous l'avez compris par vous-même, 
> pour chaque fichier nix, vous avez obligatoirement une accolade autour de votre "code/configuration". 

### timeZone

On répète encore le processus en déplaçant vers `modules/nixos/core/timezone/default.nix` :

```nix
  time.timeZone = "Europe/Paris";
```

```nix
{
  time.timeZone = "Europe/Paris";
}
```

Enfin on importe dans `configuration.nix` :

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
  ];
```

### Locales

Vous comprenez le principe, accélérons :

`configuration.nix` -> `modules/nixos/core/locales/default.nix`

```nix
{
  i18n = {
    defaultLocale = "fr_FR.UTF-8";
    extraLocaleSettings = {
      LC_ADDRESS        = "fr_FR.UTF-8";
      LC_IDENTIFICATION = "fr_FR.UTF-8";
      LC_MEASUREMENT    = "fr_FR.UTF-8";
      LC_MONETARY       = "fr_FR.UTF-8";
      LC_NAME           = "fr_FR.UTF-8";
      LC_NUMERIC        = "fr_FR.UTF-8";
      LC_PAPER          = "fr_FR.UTF-8";
      LC_TELEPHONE      = "fr_FR.UTF-8";
      LC_TIME           = "fr_FR.UTF-8";
    };
  };
}
```

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
  ];
```

### Keyboard

`configuration.nix` -> `modules/nixos/core/keyboard/default.nix`

```nix
  services.xserver.xkb = {
      layout  = "fr";
      variant = "mac";
  };

  console = {
    useXkbConfig = true;
  };
```

Profitez-en pour remplacer `console.keyMap = "fr"` par `console.useXkbConfig = true;`, ça permettra d'utiliser le même clavier que l'on soit en "graphique" ou en "tty".

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
    ./modules/nixos/core/keyboard
  ];
```

### Premier module optionnel : desktop

`configuration.nix` -> `modules/nixos/optional/desktop/default.nix`

```nix
  services.xserver = {
    enable = true;
    displayManager.gdm.enable   = true;
    desktopManager.gnome.enable = true;
  };
```

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
    ./modules/nixos/core/keyboard
    ./modules/nixos/optional/desktop # <-- Faites attention à adapter le chemin.
    ./modules/nixos/optional/printing
  ];
```

### Printing

`configuration.nix` -> `modules/nixos/optional/printing/default.nix`

```nix
{
    services.printing.enable = true;
}
```

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
    ./modules/nixos/core/keyboard
    ./modules/nixos/optional/desktop
    ./modules/nixos/optional/printing 
  ];
```

### Son 

`configuration.nix` -> `modules/nixos/optional/sound/default.nix`

```nix
{
  hardware.pulseaudio.enable = true;

  services.pipewire = {
    enable         = true;
    alsa = {
      enable       = true;
      support32Bit = true;
    };
    pulse.enable   = true;
  };
}
```

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
    ./modules/nixos/core/keyboard
    ./modules/nixos/optional/desktop
    ./modules/nixos/optional/printing 
    ./modules/nixos/optional/sound
  ];
```

### Security

`configuration.nix` -> `modules/nixos/core/security/default.nix`

```nix
{
  security.rtkit.enable = true;
}
```

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
    ./modules/nixos/core/keyboard
    ./modules/nixos/core/security
    ./modules/nixos/optional/desktop
    ./modules/nixos/optional/printing 
    ./modules/nixos/optional/sound
  ];
```

### Nix 

`configuration.nix` -> `modules/nixos/core/nix/default.nix`

```nix
{
  nixpkgs.config.allowUnfree = true;
}
```

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
    ./modules/nixos/core/keyboard
    ./modules/nixos/core/security
    ./modules/nixos/core/nix
    ./modules/nixos/optional/desktop
    ./modules/nixos/optional/printing 
    ./modules/nixos/optional/sound
  ];
```

### stateVersion

`configuration.nix` -> `modules/nixos/core/stateVersion/default.nix`

```nix
{
  system.stateVersion = "24.05";
}
```

```nix
  imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
    ./modules/nixos/core/keyboard
    ./modules/nixos/core/security
    ./modules/nixos/core/nix
    ./modules/nixos/core/stateVersion
    ./modules/nixos/optional/desktop
    ./modules/nixos/optional/printing 
    ./modules/nixos/optional/sound
  ];
```

Et nous nous arrêterons là. 

Votre fichier `configuration.nix` devrait ressembler à ça : 

```nix
{ config, pkgs, ... }:

{
    imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
    ./modules/nixos/core/keyboard
    ./modules/nixos/core/security
    ./modules/nixos/core/nix
    ./modules/nixos/core/stateVersion
    ./modules/nixos/optional/desktop
    ./modules/nixos/optional/printing 
    ./modules/nixos/optional/sound
  ];

  users.users.user = {
    isNormalUser = true;
    description = "user";
    extraGroups = [ "networkmanager" "wheel" ];
    packages = with pkgs; [];
  };

  programs.firefox.enable = true;

  environment.systemPackages = with pkgs; [];

}
```

Je me répète de nouveau, avec ces modifications, nous avons organisé en deux espaces, 
les options `core` qui l'on souhaite avoir **partout** des options **optional** qui apparaîtront uniquement là où nous en avons besoin. 

Ces modifications nous éviterons de nombreux mots de têtes plus tard, c'est promis. 

Nous allons conclure ce chapitre dans une dernière tâche. 

### Dernière optimisation pour la route

Si les modules `core` sont présents sur toutes les machines, nous n'avons **aucune** raisons de les voir apparaître sur dix lignes d'importation. 

Nous allons les rassembler en une seule "instruction". 

Toujours dans `configuration.nix`, modifiez : 

```nix
    imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core/bootloader
    ./modules/nixos/core/network
    ./modules/nixos/core/timezone
    ./modules/nixos/core/locales
    ./modules/nixos/core/keyboard
    ./modules/nixos/core/security
    ./modules/nixos/core/nix
    ./modules/nixos/core/stateVersion
    ./modules/nixos/optional/desktop
    ./modules/nixos/optional/printing 
    ./modules/nixos/optional/sound
  ];
```

pour : 

```nix
    imports = [
    ./hardware-configuration.nix
    ./modules/nixos/core
    ./modules/nixos/optional/desktop
    ./modules/nixos/optional/printing 
    ./modules/nixos/optional/sound
  ];
```

Créez un fichier dans `modules/nixos/core/default.nix` et ajoutez : 

```nix
    imports = [
    ./bootloader
    ./network
    ./timezone
    ./locales
    ./keyboard
    ./security
    ./nix
    ./stateVersion
  ];
```

Rebuild, vérifiez que vous n'ayez aucune erreur. 
Félicitations, vous venez de **modulariser** votre configuration.

> [!IMPORTANT]
> Si vous n'aviez pas compris, nous avons réunis dans `modules/nixos/core/default.nix` l'ensemble des
> importations `core`. 
> Puisque de toute manière, on les veux sur toutes les machines, autant les réunir en un seul point. 
> Nous ne faisons pas la même chose avec les modules `optional` puisque nous piocherons ces derniers au besoin.


