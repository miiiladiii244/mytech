# Setup SSH and GPG for github on Ubuntu

Source: [ChatGPT](https://chatgpt.com)
Source: [GitHub Copilot](https://github.com/features/copilot)

This guide covers how to set up both SSH keys and GPG keys for secure Git operations on Ubuntu. 

---

## Table of Contents
1. [Why Use SSH and GPG?](#why-use-ssh-and-gpg)
2. [Setup SSH](#setup-ssh)
    1. [Generate an SSH Keypair](#generate-an-ssh-keypair)
    2. [Add SSH Key to GitHub](#add-ssh-key-to-github)
    3. [Clone a Repository via SSH](#clone-a-repository-via-ssh)
3. [Setup GPG](#setup-gpg)
    1. [Generate a GPG Key](#generate-a-gpg-key)
    2. [Export Your Public Key](#export-your-public-key)
    3. [Add GPG Key to GitHub](#add-gpg-key-to-github)
    4. [Configure Git to Use Your GPG Key](#configure-git-to-use-your-gpg-key)
    5. [Verify a Signed Commit](#verify-a-signed-commit)
4. [Troubleshooting](#troubleshooting)
5. [Summary](#summary)

---

## Why Use SSH and GPG?

- **SSH** keys replace the need to enter your password when interacting with Git repositories over SSH. They’re more secure than using basic credentials and easier to automate.
- **GPG** (GNU Privacy Guard) is used to sign commits, so others can verify that the commit truly came from you and hasn’t been tampered with.

By combining these two, you get a robust, secure workflow for interacting with Git repositories and verifying code changes.

**Note:** Always keep your private keys secure and never share them.

---

## Setup SSH

### Generate an SSH Keypair

1. **Open a terminal** on Ubuntu.
2. Run:
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
3. Press Enter to accept the default location (~/.ssh/id_ed25519).
4. Set a passphrase if you want additional security.

Once complete, you’ll have two files:

`~/.ssh/id_ed25519` (private key, do not share)
`~/.ssh/id_ed25519.pub` (public key, safe to share)

### Add SSH Key to GitHub

1. View your public key:
    ```bash
    cat ~/.ssh/id_ed25519.pub
    ```
2. Copy the entire output.
3. In GitHub, go to:
    Settings → SSH and GPG keys → New SSH key.
    Paste your public key into the Key field, give it a title (optional), and click Add SSH key.

### Clone a Repository via SSH

To clone a GitHub repository using SSH:

    ```bash
    git clone git@github.com:Username/repo-name.git
    ```
You’ll no longer need to provide a username/password every time you push or pull.

## Setup GPG

### Generate a GPG Key

1. Install GPG if you haven’t:

    ```bash
    sudo apt-get update
    sudo apt-get install gnupg
    ```
2. Generate a key:
    ```bash
    gpg --full-generate-key
    ```
**Key type**: You can choose RSA (default) or ECC (like ed25519).
**Key size (if RSA)**: 4096 bits is common.
**Expiry**: Decide if it should expire or not.
**Name/Email**: Use the same email you use for GitHub commits.
**Passphrase**: Protects your private key. Keep it secure and do not share it.

### Export Your Public Key

1. List your keys to find the key ID:
    ```bash
    gpg --list-secret-keys --keyid-format=long
    ```
2. Export your public key in ASCII format:
    ```bash
    gpg --armor --export <KEY_ID>
    ```
This will print out a block that starts with `-----BEGIN PGP PUBLIC KEY BLOCK-----`.

### Add GPG Key to GitHub

1. Copy the entire ASCII-armored public key block.
2. In GitHub, navigate to:
    Settings → SSH and GPG keys → New GPG key.
3. Paste the block into the Key field, then Add GPG key.

### Configure Git to Use Your GPG Key

To automatically sign your commits:

    ```bash
    git config --global user.signingkey <KEY_ID>
    git config --global commit.gpgsign true
    git config --global gpg.program gpg
    ```
Where <KEY_ID> is the same key ID or fingerprint identified in the previous steps.

### Verify a Signed Commit

Locally, you can check a commit’s signature:
    ```bash
    git log --show-signature -1
    ```

On GitHub, commits signed by this GPG key will have a “Verified” badge.

## Troubleshooting

“No public key”: You need to import the public key into your local GPG keyring. If you’re verifying someone else’s commit, import their public key.
“Bad signature”: The GPG signature didn’t match the commit. Verify the email, key ID, and that the commit is truly from the signer.
Password prompts for SSH: Make sure you’re using the SSH clone URL (beginning with git@github.com:) and that your private key permissions are `chmod 600 ~/.ssh/id_ed25519`.

## Summary

SSH Setup: Generate a key (ssh-keygen), add it to GitHub, and clone via SSH.
GPG Setup: Generate a GPG key (gpg --full-generate-key), add the public key to GitHub, configure Git to sign commits.
Enjoy a Secure Workflow: You’ll no longer enter passwords for Git operations, and your signed commits will be recognized as verified on GitHub.
Use this workflow to maintain best practices for secure code collaboration on Ubuntu.
