
# Step 1: Generate SSH Key Pair

1. **Run the `ssh-keygen` command:**
   - Type the following command and press `Enter`:
     ```bash
     ssh-keygen -t rsa -b 4096
     ```

2. **Choose a file location:**
   - After running the command, you’ll see a prompt like this:
     ```
     Generating public/private rsa key pair.
     Enter file in which to save the key (/Users/your_username/.ssh/id_rsa):
     ```
   - Press `Enter` to accept the default location (`/Users/your_username/.ssh/id_rsa`), or type a different file path and then press `Enter`.

3. **Set a passphrase (optional but recommended):**
   - Next, you’ll be prompted to enter a passphrase:
     ```
     Enter passphrase (empty for no passphrase):
     ```
   - Type a passphrase if you want added security, or just press `Enter` to skip this step without setting a passphrase.
   - If you set a passphrase, you’ll need to confirm it:
     ```
     Enter same passphrase again:
     ```

   Here’s what the whole sequence might look like:

   ```
   ssh-keygen -t rsa -b 4096
   Generating public/private rsa key pair.
   Enter file in which to save the key (/Users/your_username/.ssh/id_rsa): 
   Enter passphrase (empty for no passphrase): 
   Enter same passphrase again: 
   Your identification has been saved in /Users/your_username/.ssh/id_rsa.
   Your public key has been saved in /Users/your_username/.ssh/id_rsa.pub.
   The key fingerprint is:
   SHA256:XXXXXXXXXXXXXX your_username@your_machine
   The key's randomart image is:
   +---[RSA 4096]----+
   |      . .o       |
   |      ..oo.      |
   |     .+o+.       |
   |     .+=E        |
   |    .oo.S        |
   |   . o..         |
   |    + +..        |
   |   o..o+.        |
   |  ...+=+=.       |
   +----[SHA256]-----+
   ```


---

# Step 2: Copy the Public Key to the Ubuntu Machine

1. **Display the public key:**
   - Type the following command to show the public key:
     ```bash
     cat ~/.ssh/id_rsa.pub
     ```
   - Copy the entire output starting with `ssh-rsa`.

2. **Transfer the public key to the Ubuntu machine:**
   - Use `ssh-copy-id` to copy your public key to the Ubuntu machine (replace `username` and `hostname` with your Ubuntu machine's details):
     ```bash
     ssh-copy-id username@hostname
     ```
   - If `ssh-copy-id` is not installed on your Mac, you can manually add the key by logging into the Ubuntu machine and appending the key to `~/.ssh/authorized_keys`.

3. **Alternatively, manually add the key:**
   - SSH into your Ubuntu machine:
     ```bash
     ssh username@hostname
     ```
   - Create a `.ssh` directory if it doesn't exist:
     ```bash
     mkdir -p ~/.ssh
     chmod 700 ~/.ssh
     ```
   - Append the public key to `authorized_keys`:
     ```bash
     echo "your_copied_public_key" >> ~/.ssh/authorized_keys
     chmod 600 ~/.ssh/authorized_keys
     ```
   - Exit the SSH session:
     ```bash
     exit
     ```

---

# Step 3: Test SSH Access

1. **Test the connection:**
   - Attempt to SSH into the Ubuntu machine using the private key:
     ```bash
     ssh username@hostname
     ```
   - If everything is set up correctly, you should log in without being prompted for a password (unless you set a passphrase for your key).

---

# Step 4: (Optional) Configure SSH Config File for Convenience

1. **Create a backup of the SSH config file:**
   - Before making any changes, it's a good idea to back up your existing `~/.ssh/config` file (if it exists). You can do this by running:
     ```bash
     cp ~/.ssh/config ~/.ssh/config.backup
     ```
   - This will create a backup named `config.backup` in the same directory.

2. **Edit the SSH config file using `vim`:**
   - Create or edit the SSH config file to simplify SSH commands:
     ```bash
     vim ~/.ssh/config
     ```
   - Add an entry for your Ubuntu machine:
     1. Press `i` to enter insert mode.
     2. Type the following configuration:
        ```plaintext
        Host your_alias
            HostName hostname
            User username
            IdentityFile ~/.ssh/id_rsa
        ```
     3. Press `Esc` to exit insert mode.
     4. Type `:wq` and press `Enter` to save the changes and exit `vim`.

3. **SSH using the alias:**
   - Now you can connect using the alias:
     ```bash
     ssh your_alias
     ```


---


# Step 5: Managing Your SSH Keys

1. **List your keys:**
   - To view your SSH keys, use:
     ```bash
     ls ~/.ssh
     ```

2. **Add your key to the SSH agent (if using a passphrase):**
   - Start the SSH agent:
     ```bash
     eval "$(ssh-agent -s)"
     ```
   - Add your SSH private key:
     ```bash
     ssh-add ~/.ssh/id_rsa
     ```

