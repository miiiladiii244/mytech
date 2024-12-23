# How to Write Verified Git Commits on Windows

Source: [ChatGPT](https://chatgpt.com/)

To sign commits in Git on Windows and have them verified on GitHub, follow the steps below.

## Step 1: Install Git for Windows

If you don't already have Git installed on your Windows system, follow these steps to install it:

1. Go to [Git for Windows](https://git-scm.com/download/win).
2. Download the installer and run it.
3. Follow the default installation options unless you have a specific configuration in mind.

## Step 2: Install GPG (GNU Privacy Guard)

You need to have GPG (GNU Privacy Guard) installed on your Windows machine to generate GPG keys. You can install it using [Gpg4win](https://gpg4win.org/).

1. **Download and Install Gpg4win:**

   - Go to [Gpg4win](https://gpg4win.org/download.html) and download the installer.
   - Run the installer and follow the instructions. Make sure to install the **Kleopatra** tool (for managing keys) and **Gpg4win** components.

2. **Verify GPG Installation:**
   Once installed, open Command Prompt (or Git Bash) and verify that GPG is installed by running:
   ```bash
   gpg --version
   ```
   You should see output confirming the GPG version installed.

## Step 3: Generate a GPG Key

1. **Open Git Bash** (or Command Prompt with Git support).
2. Run the following command to generate your GPG key:
   ```bash
   gpg --full-generate-key
   ```
   Follow the prompts:
   - Select **RSA and RSA** (default option).
   - Choose a key size (2048 or 4096 bits are typical).
   - Set an expiration date (optional).
   - Enter your name and email (use the same email associated with your GitHub account).
   - Set a passphrase to protect your GPG key.

## Step 4: List Your GPG Keys

After generating your key, list it to find the key ID:

```bash
gpg --list-secret-keys --keyid-format LONG
```

This will display a list of your GPG keys. Look for the `sec` line, which contains the key ID. It will look something like this:

```
/Users/yourname/.gnupg/secring.gpg
------------------------------
sec   4096R/<your-key-id> 2019-05-01 [expires: 2020-05-01]
uid                          Your Name <youremail@example.com>
ssb   4096R/<subkey-id> 2019-05-01
```

Copy the key ID from `<your-key-id>` (it will be a long alphanumeric string).

## Step 5: Add Your GPG Key to GitHub

1. **Export Your Public Key:**
   Export your public GPG key to GitHub using the following command:

   ```bash
   gpg --armor --export <your-key-id>
   ```

   Replace `<your-key-id>` with the actual key ID.

2. **Add Your GPG Key to GitHub:**
   - Copy the output from the above command.
   - Go to GitHub, click on your profile picture at the top-right, and select **Settings**.
   - In the **Settings** menu, select **SSH and GPG keys** from the left sidebar.
   - Click on **New GPG key**, paste your key in the field, and click **Add GPG key**.

## Step 6: Configure Git to Use Your GPG Key

1. **Tell Git to Use Your GPG Key:**
   You need to tell Git to use your GPG key for signing commits. Run the following command:

   ```bash
   git config --global user.signingkey <your-key-id>
   ```

   Replace `<your-key-id>` with the key ID you obtained earlier.

2. **Enable Commit Signing by Default:**
   You can enable automatic commit signing with the following command:
   ```bash
   git config --global commit.gpgSign true
   ```

## Step 7: Sign Your Commits

Now that your GPG key is set up, Git will automatically sign your commits. When you make a commit, Git will use your GPG key to sign it.

1. **Make a Commit:**
   When you commit, Git will automatically sign it if you've enabled the `commit.gpgSign` option:

   ```bash
   git commit -m "Your commit message"
   ```

2. **Manual Commit Signing (if not automatic):**
   If you haven't enabled automatic signing, you can sign a commit manually with:
   ```bash
   git commit --gpg-sign -m "Your commit message"
   ```

## Step 8: Verify Your Commit on GitHub

Once you push your signed commits to GitHub, you should see a "Verified" tag next to the commit on GitHub, indicating that the commit has been signed and verified with your GPG key.

To push the commit to GitHub, use the following command:

```bash
git push origin <branch-name>
```

Afterward, navigate to your GitHub repository and check the commit. It should display a "Verified" label next to the commit if everything is set up correctly.
