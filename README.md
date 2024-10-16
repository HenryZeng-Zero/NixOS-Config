# NixOS-Config
LANG: EN -> [CN](README.cn.md)

Rikki's NixOS Config



# NixLang Learning
## How to share set / variable between modules
You can seen `_module.args` or `specialArgs` as your best solution. 

The differents between above is here:  

- `specialArgs` just used in `flake.nix`

    ```nix
    {
      description = "description";

      inputs = {
        
      };

      outputs = {
        self,
        nixpkgs,
        ...
      } @ inputs: {
        nixosConfigurations = {
          "<sub-config-name>" = nixpkgs.lib.nixosSystem {
            system = "x86_64-linux";
            specialArgs = {inherit inputs;}; # <- here
            modules = [

            ];
          };
        };
      };
    }
    ```

    In this example, you inherit inputs set as your extra arguments, then
    all the modules can get variables which in your inputs set.

    You can use below to help your module to get variables:

    ```nix
    {
      self,
      nixpkgs,
      ...
    }: {

    }
    ```

    Just list variables in the header of file, then your module got it.

- `_module.args` must be used in an module

    ```nix
    {...} @ upstream: let
      trackerList = import ./aria2-tracker;
    in {
      _module.args = {inherit trackerList;};
      imports = [
      ];
    }
    ```

    In this example, we created a variable named `trackerList`.

    We use `_module.args = {inherit trackerList;};` which equal to 
    `_module.args.trackerList = trackerList;` to expose the variable
    `trackerList` to another module.

    If we want to import `trackerList`, we can simply use the same code in paragraph one.

    extra: If you want to replace `specialArgs` to `_module.args`

    You can just use below to achieve your goal.

    ```nix
    {
      description = "description";

      inputs = {
        
      };

      outputs = {
        self,
        nixpkgs,
        ...
      } @ inputs: {
        nixosConfigurations = {
          "<sub-config-name>" = nixpkgs.lib.nixosSystem {
            system = "x86_64-linux";
            modules = [
              {
                _module.args = {inherit inputs;}; # <- here
              }
            ];
          };
        };
      };
    }
    ```

    You can easily find that we created an inline module, and it not against the rules.


- [nixos-module-imports-with-arguments](https://stackoverflow.com/questions/47650857/nixos-module-imports-with-arguments)
- [nixos-flake-and-module-system](https://nixos-and-flakes.thiscute.world/zh/nixos-with-flakes/nixos-flake-and-module-system)


# Nix Code Formatter

Use [alejandra](https://github.com/kamadorueda/alejandra) as the official formatter for this repo.

You can try it in your browser. [here](https://kamadorueda.com/alejandra/)
