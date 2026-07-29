# Connecting via SSH

To connect to the server, use the following command:

```bash
ssh -p 22 talktosu@36.253.137.4
```

## Add Your SSH Key to the Server

1. On the server terminal, open the `authorized_keys` file:
   - `vim ~/.ssh/authorized_keys`

2. On your local computer, navigate to your `.ssh` directory and display your public key:
   - `cd /Users/suman/.ssh`
   - `cat id_ed25519.pub`

3. Copy the content of `id_ed25519.pub` from your local computer and paste it into the `authorized_keys` file on the server.

## Using FileZilla

- Set the Login Type to "Key file" and select `/Users/suman/.ssh/id_ed25519` as your private key file.