
### Issue
* How to configure **GitSync** on an iPhone to sync **Obsidian** notes with a self-hosted `Git` server.

### Prerequisite

* Linux server with a public IP address or registered domain name.
* Linux desktop
* Apple iPhone
* GitSync application


### Resolution

### Prepare the Linux Server for Git

Perform the following steps on the Linux server that will host the `Git` repositories:

#### 1. Install Git (if not already installed):
```
# Red Hat Enterprise based distributions
sudo dnf install git

# Debian based distributions
sudo apt install git
```
#### 2. Create the root directory for remote repositories:
Create a dedicated directory to store `Git` repositories:
```
sudo mkdir /git
```

#### 3. Create a Git user account:
For security and separation of responsibilities, create a dedicated system user for managing `Git` repositories:
```
# Red Hat Enterprise based distributions
sudo useradd git

# Debian based distributions
sudo adduser git
```

#### 4. Assign ownership of the Git directory:
Ensure the `git` user owns the repository directory:
```
sudo chown -R git:git /git
```

#### 5. Create a repository directory:
Switch to the `git` user and create a directory for the new repository.

Example Output:
```
mkdir /git/Server_Documentation.git
```
#### 6. Initialize the repository:
From inside the repository directory, initialize it as a bare `Git` repository:

Example Output:
```
cd /git/Server_Documentation.git
```
```
git init --bare
```
The Linux server is now configured as a **Git** server and the new repository is ready for use.

### Set Up the Desktop Client
To interact with the **Git** server from your desktop, you need to configure `Git` and set up secure SSH access. This ensures you can sync your Obsidian vault without being prompted for a password each time.

#### 1. Install Git (if not already installed):
```
# Red Hat Enterprise Linux-based distributions
sudo dnf install git

# Debian-based distributions
sudo apt install git
```
#### 2. Configure SSH Access
The **Git** server requires SSH key authentication. You’ll need to copy your **public SSH key** to the **Git** server.
1. First, generate a key pair on your desktop if you don’t already have one:
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
This creates two files in `~/.ssh/`:

* `id_rsa` (your private key — **keep this safe**)
* `id_rsa.pub` (your public key — safe to share)
2. Copy your public key to the **Git** server:
```
ssh-copy-id git@your-server-address
```
Replace `git@your-server-address` with the correct user and host.

3. If `ssh-copy-id` is not available, you can manually add the key:
* On your desktop, open your public key:
    ```
    cat ~/.ssh/id_rsa.pub
    ```
* Copy it's contents.
* On the server, log in as the `git` user and create the `.ssh` directory if it does not exist:
    ```
    mkdir -p ~/.ssh
    chmod 700 ~/.ssh
     ```
*  Open the file `~/.ssh/authorized_keys` (create it if necessary) and paste the public key contents. Then set permissions:
     ```
     chmod 600 ~/.ssh/authorized_keys
     ```
At this point, your desktop should be able to connect to the **Git** server without a password.

### 3. Initialize Your Obsidian Vault as a Git Repository
With SSH access set up, you can now turn your Obsidian vault into a `Git` repository and sync it to the **Git** server.

1. Open a terminal and change into your Obsidian vault directory:
```
cd /path/to/your/obsidian-vault
```
2. Initialize the vault as a repository:
```
git init
``` 
3. Add the **Git** server repository as a remote:
```
git remote add origin git@your-server-address:/home/git/obsidian-vault
```
Example Output:
```
git remote add origin git@exampleserver.com:/home/git/Server_Documentation.git
```
4. Verify the remote is set:
```
git remote -v
```

### 4. Initial sync
Before syncing your valut, create a `.gitignore` file to exclude Obsidian’s internal configuration folder. This ensures only your notes are synced, not app-specific metadata.

1. Inside your Obsidian vault directory, create the `.gitignore` file:
```
echo ".obsidian/" > .gitignore
```
2. Stage all files (except those ignored):
```
git add .
```
3. Commit your changes:
```
git commit -m "Initial commit of Obsidian vault"
```
4. Push the repository to the **Git** server:
```
git push origin master
```
> Depending on your `Git` version, the default branch may be `main` instead of `master`. If so, use `main` in the push command.

At this point, your desktop vault is fully backed up to the **Git** server and ready to sync with your iPhone.

### 5. Configure GitSync on iPhone
With the Git server prepared and your vault pushed from the desktop, the next step is to configure GitSync on your iPhone to connect to the repository and begin syncing your Obsidian vault.

1. **Install GitSync**
* Download **GitSync** from the iOS App Store.

* Open GitSync for the first time.

* On the **Welcome Screen**, tap **Let’s Go**.

* A notification prompt will appear — tap **OK**.

* iOS will then display: *“GitSync would like to send you notifications.”* Choose **Allow**.

* GitSync will show an *“Almost There”* popup. Tap **OK** to continue.

* Next, an **Auth** screen will appear with the title *“Select your git provider and authenticate.”*
  * In the dropdown menu (default: **GitHub**), choose `>_SSH`.

2. **Generate SSH Keys in GitSync**
* After selecting **SSH**, a new screen appears.

* Tap the **Generate Keys** button.

* GitSync will create a keypair and display:
   * **Priv Key** (your private key — do not share this).
   * **Pub Key** (your public key — this is what you’ll add to the Git server).

* Tap the **copy icon** to the right of the **Pub Key** line.

* When the **Confirm Pub Key** box appears, touch **Confirm** to complete copying the key.

3. **Transfer the Public Key to Your Desktop**
* The easiest way to get the public SSH key off your iPhone:
    1. Minimize GitSync after confirming the key. Do **not** close it.
    2. Open Gmail, compose a new email to yourself, paste the key, and send it.
    3. On your desktop computer, open Gmail and copy the key from the email.

4. **Add the Public Key to the Git Server**
   * Connect to the Git server via SSH:
```
ssh user@your-server-domain
```
* Edit the `authorized_keys` file for the `git` user:
```
vi /home/git/.ssh/authorized_keys
```
* Paste the **public key** at the end of thee file.
* Save and exit.


5. **Add Repository in GitSync**
  
  * Now go back to your iPhone and continue in GitSync.  
