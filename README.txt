SesameSSH: Passphrase-based SSH client
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
SesameSSH is a command-line tool for Unix which lets you connect with a
passphrase to SSH servers which accept public keys only. SesameSSH prompts
you for a passphrase, generates an ED25519 keypair deterministically from
the passphrase, and invokes OpenSSH's ssh tool to connect to the SSH server
using ED25519 public key authentication.

Use cases of SesameSSH:

* On the SSH server you can use ~/.ssh/authorized_keys with `command="..."',
  and different passphrases can run different commands. For that you don't
  need root access on the SSH server.

* You can print out your SesameSSH passphrase or private user key, and type it
  on a client machine which doesn't have your SSH private keys installed. (The
  passphrase can be as short or long as you wish, the length of the private
  user keys is 64 hex nibbles or 44 base64 characters.) You have to type
  much less than typing a full ~/.ssh/id_ed25519 file (>= 399 bytes).

* You can connect to the SSH server even if you client SSH configuration is
  in a bad or unknown state (e.g. bad ssh-agent, bad /etc/ssh/ssh_config,
  bad ~/.ssh/config, bad ~/.ssh/authorized_keys), because SesameSSH doesn't
  use any of those configs.

Dependencies of SesameSSH:

* A Bourne-compatible shell. Tested and works with Bash, Zsh, Dash and
  Busybox sh.
* Python 2.7, 2.6, 2.5 or 2.4. (Python 2.4 needs the hashlib package
  installed as an extra.)
* An SSH server which supports ED25519 public key authentication and
  Curve25519 key excange. OpenSSH >=6.5, TinySSH and
  https://github.com/pts/pts-dropbear all work.
* An OpenSSH client (ssh tool) which supports ED25519 public key
  authentication and Curve25519 key excange. That is, version 6.5 (2014) or
  later.

How to set up SesameSSH:

1. On the client machine, download the sesamessh script and make it
   executable (type commands without the leading $):

     $ curl -LO https://github.com/pts/sesamessh/raw/master/sesamessh
     $ chmod 755 sesamessh

2. On the client machine, try to connect to the server:

     $ ./sesamessh --setup USER@HOST

   You can also specify the port number and other ssh(1) flags:

     $ ./sesamessh --setup -p PORT USER@HOST

   When prompted for the sesamessh user key, invent a passphrase (or use a
   random 64 hex string), and press <Enter>. You will need to remember your
   user key, so take notes (into a file, or print it), and keep your notes a
   secret.

   When prompted for the sesamessh host key, just leave it empty by pressing
   <Enter>.

3. The `sesamessh --setup' output will look like this:

     Enter sesamessh user key for target -p PORT USER@HOST: PASSPHRASE  (echoed, don't show anyone)
     Enter sesamessh host key for target -p PORT USER@HOST:
     Warning: Permanently added 'h' (ED25519) to the list of known hosts.
     USER@HOST: Permission denied (publickey).
     info: sesamessh target: -p PORT USER@HOST
     info: sesamessh user key: .
     info: sesamessh user key b64: Xyz.....................................dYE=
     info: sesamessh user key hex: 123..........................................................581
     info: sesamessh user public key: ssh-ed25519 Aaa..............................................................3cU SeSc
     info: sesamessh host key b64: Ghi.....................................e4U=
     info: sesamessh host key hex: abc..........................................................b85
     info: sesamessh host public key: ssh-ed25519 Bbb..............................................................XuF

   This indicates that the SSH connection wasn't successful (`Permission
   denied.'). It's normal, because the SSH server hasn't been configured
   yet.

   Optionally, add the sesamessh host public key to your notes: add the b64
   or hex version, because the public key version is longer, and they are
   equivalent. If you specify it later, when connecting again to the same
   server with SesameSSH, then this will authenticate the server, and
   protect against man-in-the-middle attacks.

   Optionally, in your notes you can replace the passphrase with either
   the user key b64 or user key hex. This will make SesameSSH connect
   faster, because it doesn't have to do the key derivation iterations.

4. Append the user public key (starting with `ssh-ed25519' above, it's _not_
   the host public key) to a new line in the ~/.ssh/authorized_keys file on
   the SSH server.

5. Connect regularly with sesamessh (now without the --setup):

     $ ./sesamessh USER@HOST

   When prompted, type the user key and host key which you have noted.

   The output should look like this:

     Enter sesamessh user key for target USER@HOST: PASSPHRASE  (echoed, don't show anyone)
     Enter sesamessh host key for target USER@HOST: HOSTKEY
     $ _
     $ exit

   As you see, the SSH connection and authentication succeeds now.

Alternative usage of SesameSSH without downloading it: Do any of:

  $ curl -s https://pts.github.io/sesamessh | sh
  $ curl -Ls  https://github.com/pts/sesamessh/raw/master/sesamessh | sh
  $ wget -qO- https://github.com/pts/sesamessh/raw/master/sesamessh | sh
  $ busybox wget -qO- http://pts.github.io/sesamessh | busybox sh

Shell compatibility:

* Bash 4.1.5 (2009): OK both as sh and bash.
* Bash 4.4.12 (2016): OK both as sh and bash.
* Zsh 4.3.10: OK.
* Zsh 5.4.2: OK.
* Dash 0.5.5.1: OK.
* Dash 0.5.8-2.5: OK.
* Busybox 1.17.3 sh (2010): OK.
* Busybox 1.21.1 sh (2013): OK.

Python compatibility:

* 2.7: OK.
* 2.6: OK.
* 2.5: OK.
* 2.4: Only if hashlib is installed as an extra package (from PyPi).
* 3.x: Not compatible.

__END__
