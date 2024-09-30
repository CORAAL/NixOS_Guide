# Activons des features 

Cette section est totalement facultative, il s'agit d'options que vous voudrez peut-être ajouter (ex: trim)

<!-- TODO: 
    - Ajouter une introduction
    - Revoir si le chapitre est "cohérent" ?
-->

## Fstrim 

`modules/nixos/core/trim/default.nix`

```nix
{
    services.fstrim = {
        enable = true;
        interval = "weekly";
    };
}
```

## Diff post-update

<!-- TODO: 
    - Ajouter un chemin
-->

```nix
{ lib, pkgs, ... }:

{
  environment.systemPackages = with pkgs; [ nvd ];
  system.activationScripts.report-changes = ''
    PATH=$PATH:${lib.makeBinPath [ pkgs.nvd pkgs.nix ]}
    nvd diff $(ls -dv /nix/var/nix/profiles/system-*-link | tail -2)
  '';
}
```

## Rebuild-offline

<!-- TODO: 
    - Ajouter un chemin
-->

```nix
{
  nix.settings = {
    keep-outputs = true;
    keep-derivations = true;
  };
}
```

## Garbage Collector

<!-- TODO: 
    - Ajouter un chemin
-->

```nix
{
  nix = {
    gc = {
      automatic = true;
      dates     = "daily";
      options   = "--delete-older-than 3d";
    };
    optimise = {
      automatic = true;
      dates     = ["daily"];
    };
    settings.auto-optimise-store = true;
  };
}
```

## Bootloader theme, fonts  

<!-- TODO: 
    - Ajouter un chemin
    - Expliquer "${pkgs.jetbrains-mono}"
    - Peut-être à déplacer dans un espace "rice" ? 
-->

```nix
{ pkgs, ... }:

{
    boot.initrd.systemd.enable = true;
    boot.kernelParams = [ "quiet" ];
    boot.loader = {
      efi.canTouchEfiVariables = true;
      systemd-boot = {
        enable = true;
        netbootxyz.enable = true;
      };
    };
    boot.plymouth = {
      enable        = true;
      # theme  = "breeze";
      themePackages = [ pkgs.catppuccin-plymouth ];
      theme         = "catppuccin-macchiato";
      font          = "${pkgs.jetbrains-mono}/share/fonts/truetype/JetBrainsMono-Regular.ttf";
    };
}
```


























