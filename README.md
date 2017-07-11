# `rsync-auto` is broken!

It looks as though mitchellh/vagrant#5160 has made a functional change to the behaviour of `vagrant rsync-auto`. It is no longer possible to watch and synchronise the contents of directories other than the Vagrantfile.

---

## Testing

Bring up the machine, preventing the provisioner from removing the README files:

```
$ cd _vagrant/
$ vagrant up --no-provision
```

See that the three READMEs are present:

```
$ vagrant ssh -c "ls -1 /{vagrant,insider,outsider}/README.md"
/insider/README.md
/outsider/README.md
/vagrant/README.md
Connection to 127.0.0.1 closed.
```

Let the provisioner remove the READMEs and verify:

```
$ vagrant provision
$ vagrant ssh -c "ls -1 /{vagrant,insider,outsider}/README.md"
ls: cannot access '/vagrant/README.md': No such file or directory
ls: cannot access '/insider/README.md': No such file or directory
ls: cannot access '/outsider/README.md': No such file or directory
Connection to 127.0.0.1 closed
```

Run `rsync-auto` and note the output indicating that the directories are being excluded:

```
$ vagrant rsync-auto
==> default: Not syncing /home/lcarrier@HLD.local/vagrantbug/_vagrant/insider as it is not part of the current working directory.
==> default: Not syncing /home/lcarrier@HLD.local/vagrantbug/outsider as it is not part of the current working directory.
==> default: Doing an initial rsync...
==> default: Rsyncing folder: /home/lcarrier@HLD.local/vagrantbug/_vagrant/ => /vagrant
==> default: Watching: /home/lcarrier@HLD.local/vagrantbug/_vagrant
^C
```

Finally, note that the files are no longer present on the server:

```
$ vagrant ssh -c "ls -1 /{vagrant,insider,outsider}/README.md"
ls: cannot access '/insider/README.md': No such file or directory
ls: cannot access '/outsider/README.md': No such file or directory
/vagrant/README.md
Connection to 127.0.0.1 closed.
```
