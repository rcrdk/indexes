# Git SSH Guide - Multiple Accounts (macOS)

This guide configures two GitHub remotes with separate SSH keys:

  Source            Alias               Host
  ----------------- ------------------- -----------------------
  Personal GitHub   `github-personal`   `github.com`
  Work GitHub       `github-work`       `github.com`

Once configured, each repository automatically uses the correct SSH key.
You can use `git clone`, `git pull`, `git push`, and `git fetch` without
manually switching accounts.

------------------------------------------------------------------------

## 1. Generate SSH Keys

### Personal GitHub

``` bash
ssh-keygen -t ed25519 \
  -C "your-personal-email@example.com" \
  -f ~/.ssh/id_ed25519_personal
```

### Work GitHub

``` bash
ssh-keygen -t ed25519 \
  -C "your-work-email@example.com" \
  -f ~/.ssh/id_ed25519_work
```

You'll be prompted for a passphrase. A passphrase is recommended because
macOS stores it securely in the Keychain. If you don't want one, press
**Enter** twice.

Expected structure:

``` text
~/.ssh
├── config
├── id_ed25519_personal
├── id_ed25519_personal.pub
├── id_ed25519_work
└── id_ed25519_work.pub
```

------------------------------------------------------------------------

## 2. Start the SSH Agent

``` bash
eval "$(ssh-agent -s)"
```

------------------------------------------------------------------------

## 3. Add Keys to the macOS Keychain

``` bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_personal
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_work
```

Verify:

``` bash
ssh-add -l
```

------------------------------------------------------------------------

## 4. Configure SSH

Create the config if necessary:

``` bash
touch ~/.ssh/config
nano ~/.ssh/config
```

Paste:

``` sshconfig
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
    IdentitiesOnly yes
    AddKeysToAgent yes
    UseKeychain yes

Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
    IdentitiesOnly yes
    AddKeysToAgent yes
    UseKeychain yes
```

> **Why `IdentitiesOnly yes`?**
>
> Without it, SSH may offer every key loaded in your SSH agent. GitHub
> accepts the first matching key, which can result in authenticating
> with the wrong account. `IdentitiesOnly yes` forces SSH to use only
> the key specified by `IdentityFile`.

------------------------------------------------------------------------

## 5. Upload the Public Keys

### Personal GitHub

``` bash
cat ~/.ssh/id_ed25519_personal.pub
```

Add it to **GitHub → Settings → SSH and GPG keys**.

### Work GitHub

``` bash
cat ~/.ssh/id_ed25519_work.pub
```

Add it to your work GitHub account under **Settings → SSH and GPG keys**.

------------------------------------------------------------------------

## 6. Test Connections

``` bash
ssh -T git@github-personal
ssh -T git@github-work
```

------------------------------------------------------------------------

## 7. Clone Repositories

### Personal GitHub

``` bash
git clone git@github-personal:your-user/project.git
```

### Work GitHub

``` bash
git clone git@github-work:your-org/project.git
```

------------------------------------------------------------------------

## 8. Existing Repositories

Update remotes instead of recloning.

``` bash
# Personal
git remote set-url origin git@github-personal:your-user/project.git

# Work GitHub
git remote set-url origin git@github-work:your-org/project.git
```

Verify:

``` bash
git remote -v
```

------------------------------------------------------------------------

## 9. Daily Usage

Once cloned (or after updating the remote), use Git normally:

``` bash
git pull
git push
git fetch
git switch
git checkout
git merge
```

Each repository stores its own remote URL, including the SSH alias. SSH
resolves that alias to the correct host and uses the corresponding
private key automatically.
