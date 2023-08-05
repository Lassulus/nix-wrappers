nix-wrappers is a nix/nixos based library which uses the module system to generate packages.

this solves similiar problems like nixos, nix-darwin or home-manager. but allows usage on all different systems with just nix installed and easier composability, since the output is just a package which can be used in different ways, like:

inside a systemd-service
just a package in your $PATG
inside a dev shell


some examples:

```nix
...
environment.systemPackages = [
  (wrappers.writeRedis "my-redis123" {
    port = 3001;
    package = pkgs.redis-plus-plus;
    bind = "192.168.0.1";
    databases = 16;
    passFile = "/etc/myredispass";
  })
];
...
```

```nix
...
systemd.services.myNginx = wrappers.nixos.generateSystemdService {
  name = "myNginx";
  description = "some nginx which is serving /srv/http";
  serviceConfig.ExecStart = wrappers.writeNginx {
    locations."/".root = "/srv/http";
    locations."/index.html".alias = pkgs.writeText "index.html" ''
      <doctype ...
    '';
    locations."/favcion.ico".alias = ./favicon.ico;
  };
};
...
```

alternatively people can also use the module which exposes the wrappers in a global scope:

```nix
imports = [ wrappers.module ];
wrapers.weechat-work = {
  type = "weechat";
  configDir = "/home/lass/.weechat-work";
  scripts = [
    pkgs.weechatScripts.wee-slack
  ];
  settings = {
    plugins.var.python.go.short_name = true;
  };
  extraCommands = ''
    /script upgrade
    /script install go.py
    /key bind meta-q /go
  '';
};
```
