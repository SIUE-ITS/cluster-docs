# Setup SSH keys

Secure Shell (better known as SSH) is a cryptographic network protocol which allows users to securely perform a number of network services over an unsecured network. SSH keys provide a more secure way of logging into a server with SSH than using a password alone. While a password can eventually be cracked with a brute force attack, SSH keys are nearly impossible to decipher by brute force alone.

Generating a key pair provides you with two long string of characters: a public and a private key. You can place the public key on any server, and then unlock it by connecting to it with a client that already has the private key. When the two match up, the system unlocks without the need for a password. You can increase security even more by protecting the private key with a passphrase.

?> On **Windows** do the following steps in a PowerShell window. **Linux** or **MacOS** use the  built-in terminal.

### Step 1 – Create the RSA Key Pair
The first step is to create the key pair on your personal or workstation computer:

```
$ ssh-keygen -t rsa -b 4096
```

Once you have entered the `ssh-keygen` command, you will get a few more questions:

```
Enter file in which to save the key (/home/<username>/.ssh/id_rsa):
```

You can press enter here, saving the file to the default directory (e.g., `/home/<username>/.ssh/id_rsa` on macOS or Linux or `C:\Users\<username>\.ssh\id_rsa` on Windows).

```
Enter passphrase (empty for no passphrase):
```

It’s up to you whether you want to use a passphrase. Entering a passphrase does have its benefits: the security of a key, no matter how encrypted, still depends on the fact that it is not visible to anyone else. Should a passphrase-protected private key fall into an unauthorized users possession, they will be unable to log in to its associated accounts until they figure out the passphrase, buying the hacked user some extra time. The only downside, of course, to having a passphrase, is then having to type it in each time you use the key pair.

The entire key generation process looks something like this:

```
$ ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (/home/<username>/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/<username>/.ssh/id_rsa.
Your public key has been saved in /home/<username>/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 <username>@local
The key's randomart image is:
+--[ RSA 4096]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
```

The public key is now located in `/home/<username>/.ssh/id_rsa.pub` . The private key (identification) is now located in `/home/<username>/.ssh/id_rsa` .

### Step 2 – Copy the Public Key
Once the key pair is generated, the next step is to copy the public key to your account into your `/home` directory.

You can copy the public key with the ssh-copy-id command, if it is installed on your local computer:

```
$ ssh-copy-id <username>@login.hpc.siue.edu
```

?> Make sure to replace the username.

Alternatively, you can copy the keys using SSH.

On macOS or Linux:

```
$ cat ~/.ssh/id_rsa.pub | ssh <username>@login.hpc.siue.edu "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >>  ~/.ssh/authorized_keys"
```

On Windows:

```
> cat C:\Users\<username>\.ssh\id_rsa.pub | ssh <username>@login.hpc.siue.edu "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >>  ~/.ssh/authorized_keys"
```

?> Make sure to replace the `<username>`.

No matter which command you choose, you may see something like the following:

```
The authenticity of host 'login.hpc.siue.edu' can't be established.
RSA key fingerprint is b1:2d:33:67:ce:35:4d:5f:f3:a8:cd:c0:c4:48:86:12.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'login.hpc.siue.edu' (RSA) to the list of known hosts.
<username>@login.hpc.siue.edu's password:
```

This message helps us to make sure that we haven’t added extra keys that you weren’t expecting.

Now you can go ahead and log into your user profile and you will not be prompted for a password. However, if you set a passphrase when creating your SSH key, you will be asked to enter the passphrase at that time (and whenever else you log in in the future).

### Step 3 (optional) – Configure SSH agent to add key for no passphrase login.

#### Linux and MacOS

Start ssh-agent if not started:

?> Note: the `eval` command below is not required for MacOS since ssh-agent should be started by default on newer versions. If not enabled run `sudo touch /var/db/useLS`, then reboot.

```
$ eval `ssh-agent -s`
```

Add your private key using ssh-add

```
$ ssh-add ~/.ssh/id_rsa  
Enter passphrase for /home/<username>/.ssh/id_rsa:  
Identity added: /home/<username>/.ssh/id_rsa   
(/home/<username>/.ssh/id_rsa)
```

Check if the key is added:

```
$ ssh-add -l  
2048 55:96:1a:b1:31:f6:f0:6f:d8:a7:49:1a:e5:4c:94:6f  
/home/<username>/.ssh/id_rsa (RSA)
```

Now you can use SSH without extra passphrase prompts.

#### Windows

Startup a PowerShell prompt and run the following commands:

```
Get-Service ssh-agent
```

Check if service has Status  `Stopped` s.

```
Status   Name               DisplayName
------   ----               -----------
Stopped  ssh-agent          OpenSSH Authentication Agent
```

?> If service above has Status `Running` skip to the `ssh-add <key>` step below.

```
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
```

Now anytime you open a PowerShell window to SSH first type:

```
ssh-agent
```

Now that the ssh-agent service is running type:

```
ssh-add C:\Users\<username>\.ssh\id_rsa
```

?> Make sure to change `<username>`.

List your current keys by typing:

```
ssh-add -l
```
