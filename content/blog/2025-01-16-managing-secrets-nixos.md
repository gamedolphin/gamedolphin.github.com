---
title: "Managing Secrets in Nixos with Sops"
taxonomies:
  tags: ["nixos"]
extra:
  comments: true
---
I use a bunch of "secret" keys on my computer. A good example is the Anthropic Claude LLM API key that lets me talk to Claude through emacs's gptel plugin. But if I am setting up a new computer, or moving between workstations, I need to manually copy the key over to its expected location. If I could just commit the secrets to the literate system configuration that I maintain, I can just git pull and have the key on the other computer, ready to use. I can also version control my secrets!
But of course, you do not want to commit secret api keys into a git repository! Thats where sops and its integration with nixos, sops-nix comes in!
Sops allows you to encrypt your secrets and push them up like any other file. Only you can decrypt them and use them.

> **⚠️ Security Note**
> The security of this approach depends entirely on keeping your encryption key safe. Never commit unencrypted keys or share them insecurely.
>
> While this method is convenient, the most secure approach is keeping secrets completely separate from public repositories.

So, how do you integrate sops on nixos? I think the nix-sops github README has a very good tutorial which you can go through. But I'll outline the steps I did for my own setup.

## Adding sops-nix to the configuration
This is the easy step. Add sops-nix as an input, and add the provided module to your list of modules.

``` nix
    {
        inputs.sops-nix.url = "github:Mic92/sops-nix";

        outputs = { self, nixpkgs, sops-nix }: {
            nixosConfigurations.${hostname} = nixpkgs.lib.nixosSystem {
                inherit system;
                modules = [
                    # rest of your modules
                    # ...
                    sops-nix.nixosModules.sops
                ];
            };
        };
    }
```

## Generate encryption key
Next, we need to generate a key that we will use to encrypt our secrets. This can piggy-back on top of your existing ssh key, but I decided to generate my own using age. On nixos, getting the age-keygen command was as simple as `nix-shell -p age`

``` bash
    mkdir -p ~/.config/sops/age
    age-keygen -o ~/.config/sops/age/keys.txt
```
> **💡 Tip**
> Backup this key securely! Without it, you won't be able to decrypt your secrets.

I have saved this key in multiple secure locations for easy future retrieval.

We also need the public key for this file, which can be found using the age-keygen command as well

``` bash
    age-keygen -y ~/.config/sops/age/keys.txt
```

## Sops configuration
We need to create a .sops.yaml file at the root of our configuration directory to tell sops about how we're planning to use/manage our secrets.

``` yaml
keys:
  - &primary age1yq35g6mmlem0rhr47u6ewh8dctlwp9hj0s0ac60e4hrw9hjzlqms6crf7n
creation_rules:
  - path_regex: secrets/secrets.yaml$
    key_groups:
      - age:
        - *primary
```

This tells sops a few things:
1. The public key (which we got from the last bash command) for our private secret key.
2. The path to the actual secrets file when creating secrets and which key (in our case, "primary") to use to encrypt/decrypt the secrets.

## Adding the secrets
Now we can actually add and manage the secrets. To start, you can tell sops to decrypt and provide you a file that you can edit in the $EDITOR of your choice.
``` bash
    sops --run "sops secrets/secrets.yaml"
```

This decrypts (or creates) a `secrets/secrets.yaml` file and opens it in $EDITOR. You can add, remove and modify secrets in this file and sops will encrypt the file on exit.

``` yaml
   claude_key: super-secret-unecrypted-api-key-that-i-want-to-keep-secret
```

Once I exit the $EDITOR, the file looks like this

``` yaml
    claude_key: ENC[AES256_GCM,data:PMPcEfsKZCALRdgQf5J12k8IsxlNT7R+zfSgmy3LbVhvQQzpCEVr5Xjh6ABhDRGjsTssXc1Vh7FA03oud40i5YxoYwJ2i2EqrRmpp/QNVAfbPrOzfCcwUxlbgJOUEMVv/1RJFYeqddi+bf1F,iv:lD1bRtBtnBf0ub5mGznVuoxLcFcJhxdp+mGv5TMBcKQ=,tag:TP6YLujPHFmFnK5gWwTREg==,type:str]
    ... other things
```

This file can now be safely committed to the git repository.

We must tell sops-nix about these files as well. That is done through a few lines of nix configuration.

``` nix
{
    sops.defaultSopsFile = ../secrets/secrets.yaml;  # where are the secrets
    sops.age.keyFile = "/home/<username>/.config/sops/age/keys.txt"; # the key to decrypt the secrets
}
```

## Accessing secrets

The final step is telling sops to allow users to access the secrets. I want my logged in user to see the claude_key secret so that emacs can read it.

This is done through a few additional lines of nix configuration.

``` nix
    sops.secrets.claude_key = {
      owner = "${user.username}";
    };
```

Here, I specify that the owner of the secret is the user. Otherwise, by default, root is the owner, and normal user cannot read the secret.

Once configured, your secrets are available at `/run/secrets/secret_name`. For example, to use the Claude API key in Emacs:

```elisp
(setq gptel-api-key
      (with-temp-buffer
        (insert-file-contents "/run/secrets/claude_key")
        (string-trim (buffer-string))))
```

## Troubleshooting

Common issues and solutions:

- **Permission denied**: Check the `owner` setting in your sops configuration
- **Decryption failed**: Ensure your age key is correctly configured
- **Secret not found**: Verify the path in `defaultSopsFile`

## Useful Resources

- [sops-nix GitHub Repository](https://github.com/Mic92/sops-nix)
- [Age encryption tool](https://age-encryption.org)
- [Mozilla sops documentation](https://github.com/mozilla/sops)

## Conclusion

Sops has allowed me to not worry about managing secrets separately from my nixos configuration. When I switch workstations, a simple git pull lets me access all my latest secrets and keys!
