# Émulateur de Terminal
Permet de communiquer avec le **shell** de l'OS. Indépendant du language.

# Interpréteur de language
Traduit les commandes en language pour l'OS
Entre autres:
* shell scripting languages
* Command and Scripting interpreters
On peut le détecter sur linux avec ```ps``` ou ```env```
```shell
Corsaire@htb[/htb] $ ps
PID TTY          TIME CMD
4232 pts/1    00:00:00 bash
11435 pts/1    00:00:00 ps

Corsaire@htb[/htb] $ env
SHELL=/bin/bash
```