# Day 7: Linux SSH Authentication

## Challenge Description

The system admins team of xFusionCorp Industries has set up some scripts on the jump host that run at regular intervals and perform operations on all app servers in the Stratos Datacenter. To ensure these scripts work properly, we need to make sure the `thor` user on the `jump_host` has password-less SSH access to all app servers through their respective sudo users (i.e., `tony` for `stapp01`, `steve` for `stapp02`, and `banner` for `stapp03`).

## Key Concepts

*   **SSH (Secure Shell):** A cryptographic network protocol for operating network services securely over an unsecured network.
*   **SSH Key Generation (`ssh-keygen`):** A utility to generate a new pair of authentication keys (public and private).
*   **Public Key Authentication:** A secure method of logging into an SSH server using a cryptographic key pair instead of a password.
*   **`ssh-copy-id` Utility:** A convenient tool for installing an SSH public key on a remote server's `authorized_keys` file.
*   **Password-less SSH:** The ability to log into a remote server via SSH without needing to enter a password, typically achieved using SSH key pairs.

## Solution

To set up password-less SSH authentication from `thor@jump_host` to `tony@stapp01`, `steve@stapp02`, and `banner@stapp03`, we follow these steps:

### 1. Switch to `thor` on the Jump Host

First, we ensure we are logged in as the `thor` user on the `jump_host`. If not, we can switch to this user:

```bash
sudo su - thor
```

### 2. Generate an SSH Key Pair

Next, we generate an SSH key pair for the `thor` user. We will press Enter for all prompts to use the default file locations and an empty passphrase, which is necessary for password-less authentication.

```bash
if [ ! -f ~/.ssh/id_rsa ]; then
    ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa
else
    echo "SSH key already exists."
fi
```

This command creates two files in the `~/.ssh/` directory:
*   `id_rsa`: The private key.
*   `id_rsa.pub`: The public key.

### 3. Copy the Public Key to Each App Server's Sudo User

Now, we copy the generated public key (`~/.ssh/id_rsa.pub`) to the `authorized_keys` file of the respective sudo user on each app server. We will run these commands from the `jump_host` as `thor`. When prompted, we will enter the password for the target user on the app server.

*   **For `tony@stapp01`:**
    ```bash
    ssh-copy-id tony@stapp01
    ```
    (Enter password: `Ir0nM@n`)

*   **For `steve@stapp02`:**
    ```bash
    ssh-copy-id steve@stapp02
    ```
    (Enter password: `Am3ric@`)

*   **For `banner@stapp03`:**
    ```bash
    ssh-copy-id banner@stapp03
    ```
    (Enter password: `BigGr33n`)

### 4. Test the Connections

Finally, we verify that password-less SSH access has been successfully configured by attempting to connect to each app server without providing a password.

```bash
ssh tony@stapp01
ssh steve@stapp02
ssh banner@stapp03
```

If the setup is successful, we should be able to connect to each server without being prompted for a password.

## Verification

To verify the password-less SSH setup, we can execute the following commands from the `jump_host` as the `thor` user. Each command should log us into the respective app server without asking for a password and display the server's hostname.

```bash
ssh tony@stapp01 'hostname'
ssh steve@stapp02 'hostname'
ssh banner@stapp03 'hostname'
```
