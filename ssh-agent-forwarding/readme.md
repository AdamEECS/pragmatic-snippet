# Use ssh agent forwarding to access your github on multiple servers with only one public key.

## Usage

### Generate ssh key

```bash
$ ssh-keygen -t ed25519 -f ~/.ssh/id -C "adam@mbp001"
```

- The "-t" option is used to specify the type of key that you want to generate.

    In general, it is recommended to use ECDSA or Ed25519 keys over RSA keys because they offer better security with shorter key lengths. ECDSA and Ed25519 keys are also faster to generate, transmit, and process compared to RSA keys.

- The "-f" option is used to specify the file name.

- The "-C" option is used to specify the comment.

    I use the format "user name@device name" to indicate who and on which device the key belongs to.


### Add your public key to the server

```bash
$ ssh-copy-id -i ~/.ssh/id.pub user@server
```

- The "-i" option is used to specify the public key file.

First time login to server, you will be asked to confirm the fingerprint of the server, and then you will be asked to enter the password of the user.

### Accessing Github on Local Machine using SSH

Add the SSH key to your github account.

Now you can test that your local key works by `-T` option.


```bash
$ ssh -T git@github.com
> Hi AdamEECS! You've successfully authenticated, but GitHub does not provide shell access.
```

### Set up SSH to allow agent forwarding to your server

Edit `~/.ssh/config`

```vim
Host example.com
    Hostname 192.168.1.1
    User username
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id
    ForwardAgent yes
```

### Add your public key to the ssh-agent

```bash
$ ssh-add ~/.ssh/id
```

On macOS, ssh-agent will "forget" this key, once it gets restarted during reboots. But you can import your SSH keys into Keychain using this command:

```
$ ssh-add --apple-use-keychain ~/.ssh/id
```

### Using SSH agent forwarding

you can SSH into your server and run `ssh -T git@github.com` once more. If all is well, you'll get back the same prompt as you did locally.

```bash
$ ssh -T git@github.com
> Hi AdamEECS! You've successfully authenticated, but GitHub does not provide shell access.
```

Now you can clone your github repository on your server.

```bash
$ git clone git@github.com:AdamEECS/pragmatic-snippet.git
```