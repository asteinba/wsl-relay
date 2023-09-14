# openssh-agent-wsl-relay

This tool forwards the named pipe \\.\pipe\openssh-ssh-agent (as used by OpenSSH or gpg4win) to your WSL system. I
personally use it for SSH keys that I store on my yubikey.

openssh-agent-wsl-relay is a fork of
[Lexicality/wsl-relay](https://github.com/Lexicality/wsl-relay). I made this fork because for me this tool should just
do one thing: Forward the openssh-agent from windows to WSL. Without any commandline flags or options, super simple.
I reduced the tool ~50 lines of code.

# Installation

1. Download (or compile) openssh-agent-wsl-relay.exe on your Windows system (in this example I
   use `C:\openssh-agent-wsl-relay.exe`).
2. Install `socat`.
3. Set SSH_AUTH_SOCK to a file of your choice (in this example I use `$HOME/.openssh-agent-wsl-relay`).
4. Run socat:

```bash
socat UNIX-LISTEN:$HOME/.openssh-agent-wsl-relay,fork, EXEC:'/mnt/c/openssh-agent-wsl-relay.exe',nofork
```

# Optional

For convenience, I recommend adding the following lines to your .zshrc /.bashrc / ...:

```bash
if ! pgrep --full "socat UNIX-LISTEN:$HOME/.openssh-agent-wsl-relay" > /dev/null; then
    rm -f $HOME/.openssh-agent-wsl-relay
    socat UNIX-LISTEN:$HOME/.openssh-agent-wsl-relay,fork, EXEC:'openssh-agent-wsl-relay.exe',nofork &
    disown
fi
```

This will ensure that the relay process is still running everytime you open a shell.
