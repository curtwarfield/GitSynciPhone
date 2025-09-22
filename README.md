### Issue
* How to configure **GitSync** on an iPhone to sync **Obsidian** notes with a self-hosted `Git` server.

### Prerequisite

* Linux server with a public IP address or registered domain name.
* Linux desktop
* Apple iPhone
* GitSync application


### Resolution

## Prepare the Linux Server for Git

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

## Set Up the Desktop Client
To interact with the **Git** server from your desktop, you need to configure `Git` and set up secure SSH access. This ensures you can sync your Obsidian vault without being prompted for a password each time.

### 1. Install Git (if not already installed):
```
# Red Hat Enterprise Linux-based distributions
sudo dnf install git

# Debian-based distributions
sudo apt install git
```
### 2. Configure SSH Access
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
git remote add origin <user>@<git_server>:<full_path_to_the_git_repository>
```
Example Output:
```
git remote add origin git@exampleserver.com:/git/Server_Documentation.git
```
4. Verify the repository is successfully configured:   
```
git remote -v
```

### 4. Initial sync
Before syncing your valut, create a `.gitignore` file to exclude Obsidian’s internal configuration folder. This ensures only your notes are synced, not app-specific metadata.

1. From your Obsidian vault directory, create the `.gitignore` file:
```
echo ".obsidian/" > .gitignore
```
2. Run the `git add` command to stage the files:
```
git add .
```
3. Commit the changes:
```
git commit -m "Initial commit of Obsidian vault"
```
4. Run the `git push` command to sync the repository to the Git server.
```
git push origin master
```
> Depending on your `Git` version, the default branch may be `main` instead of `master`. If so, use `main` in the push command.

The Obsidian vault is now synced to the **Git server** and ready to sync with your **iPhone**.

## Configure iPhone

### 5. Create a local Obsidian vault on iPhone
Before connecting GitSync, you need an Obsidian vault on your iPhone that stores notes locally (not on iCloud), because GitSync can only sync files in a local vault.

1. Open the **Obsidian** app on your iPhone.
2. On the **Vaults** screen, tap **Create New Vault**.
3. Enter a **Vault Name** (e.g., Server_Documentation).
4. For **Storage Location**, choose **On My iPhone** (Do **not** select iCloud).
5. Tap **Create**.

This local vault is what GitSync will use to pull from and push to your self-hosted Git repository

### 6. Configure GitSync on iPhone
With the Git server prepared and your vault pushed from the desktop, the next step is to configure GitSync on your iPhone to connect to the repository and begin syncing your Obsidian vault.

**Install GitSync**
1. Download **GitSync** from the iOS App Store.
2. Open GitSync for the first time.   
3. On the **Welcome Screen**, touch **Let’s Go**.   
![Welcome](/images/gitsync_1.jpg)
4. Tap **OK** to enable notifications.      
![Notification](/images/gitsync_2.jpg)
5. Tap **Allow** to enable iOS notifications.      
![Notification](/images/gitsync_3.jpg)
6. Tap **OK** to continue.     
![Notification](/images/gitsync_4.jpg)
7. Tap **OK** to authenticate.      
![Notification](/images/gitsync_5.jpg)
8. Tap the small triangle on the right side of the `GITHUB` line to open the drop-down menu.   
![Notification](/images/gitsync_9.jpg)
9.  From the drop-down menu, tap **SSH** under the Git Protocols.     
![Notification](/images/gitsync_6.png)
10. Tap **Generate Keys** to create SSH keys.   
![Notification](/images/gitsync_7.jpg)
10.  Tap the **Copy Icon** to the right of the **Pub Key** line to copy the public key to your clipboard.   
![Notification](/images/Untitled.png)
12. Tap **Confirm Pub Key Saved**.   
![Notification](/images/gitsync_10.jpg)

The public SSH key for GitSync is now in your clipboard and will need to be copied to your **Desktop computer**.

### Copy the Public Key contents to Your Desktop   
You need to copy the **public key** to your **Desktop computer** so it can be added to the Git server:

1. Minimize GitSync after confirming the key. **DO NOT CLOSE IT!!!**    
```
DO NOT CLOSE GitSync!!! Minimize it only!
```  
2. Open an email app (e.g., Gmail), compose a new email to yourself, and paste the contents of the public key in the email and send it.
```
Since the contents of the public key are already in your clipboard from the last step, you should just be able 
to tap Paste in the email body, to paste the contents of the public SSH key there.
```
3. From your **Desktop computer**, open the email sent from your iPhone and copy the public key contents so that it's in your clipboard.

### Add the Public Key to the Git Server  
From your **Desktop computer**.

1. SSH to your Git server:
```
ssh user@your-server-domain
```
2. Edit the `authorized_keys` file for the `git` user:
```
vi /home/git/.ssh/authorized_keys
```
3. Paste the public key you copied from GitSync to the end of the file, save and exit.

### Clone the Git Repository in GitSync
From your **iPhone**, continue configuring **GitSync**.

1\. Add your **SSH** connection to your Git Server by removing the existing `https` URL.     

![Notification](/images/gitsycn_11.jpg)

Use the following format:
```
ssh://<user>@<git_server>/<full_path_to_the_git_repository>
```

Example output:
```
ssh://git@example.com/git/Server_Documentation.git
```
