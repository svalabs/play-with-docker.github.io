---
layout: post
title: "SVA Ansible Intro"
date:   2020-12-17
author: "@ebartz"
tags: [linux,operations,ansible,community]
categories: beginner
img: "compose_swarm.png"
terms: 1
---
# Basics Installieren

Damit wir Ansible nutzen können, benötigen wir als erstes Ansible auf unserem node1:

```.term1
apk add ansible
```


Damit wir gleich unsere Playbooks vorhanden haben, muss noch das GIT-Repository gecloned werden
```.term1
git clone https://github.com/svalabs/ansible-demo && cd ansible-demo
```



# Beispiel 1
Damit es losgehen kann, bitte in den Branch step1 wechseln:

```.term1
git checkout step1
```

Als erstes soll ein Webserver auf unserem node1 installiert werden. Hierfür gibt es ein Playbook, welches dies für uns tut:

```yaml
---
- hosts: localhost
  roles:
    - httpd
```

Dies besagt also dass auf dem Server `localhost` die Ansible Rolle httpd ausgeführt werden soll.

Unsere httpd Rolle tut nicht viel mehr als das apache2 Paket zu installieren und danach den Prozess httpd zu starten.

Um das Playbook auszuführen führt einfach folgendes Kommando aus:

```.term1
ansible-playbook playbook.yaml
```

```
$ ansible-playbook playbook.yaml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'

PLAY [localhost] ***************************************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [httpd : Install apache2] *************************************************
changed: [localhost]

TASK [httpd : Check if Namenode is running] ************************************
ok: [localhost]

TASK [httpd : start apache] ****************************************************
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=4    changed=2    unreachable=0failed=0    skipped=0    rescued=0    ignored=0
```

Jetzt sollten wir prüfen, dass unser Webserver auch wirklich läuft. [**Hier gehts zum Webserver**](/){:data-term=".term1"}{:data-port="80"}

# Beispiel 2


Damit weitergehen kann, bitte in den Branch step2 wechseln:

```.term1
git checkout step2
```

Wir haben vorher unseren Webserver installiert und können diesen auch erreichen. Aber eigentlich wollen wir dort ja auch unsere Mega-Geile Website anzeigen.

Aus diesem grund kommt in Step2 nun eine weitere Rolle mit ins Spiel:

```yaml
---
- hosts: localhost
  roles:
    - httpd
    - website
```

Die Rolle Website kopiert die relevaten Daten an den Richtigen Ort, damit unsere Website nun auch ordentlich angezeigt wird.

Damit diese Änderung nun auch auf unseren Server angewendet wird, müssen wir ansible neu laufen lassen:

```.term1
ansible-playbook playbook.yaml
```

```
$ ansible-playbook playbook.yaml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'

PLAY [localhost] ***************************************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [httpd : Install apache2] *************************************************
ok: [localhost]

TASK [httpd : start apache] ****************************************************
changed: [localhost]

TASK [copy over website data] **************************************************
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=4    changed=2    unreachable=0failed=0    skipped=0    rescued=0    ignored=0
```


Wenn wir alles richtig gemacht haben, sollte nun unsere Mega-Geile Website angezeigt werden:

[**Hier gehts zum Webserver**](/){:data-term=".term1"}{:data-port="80"}

# Beispiel 3
Verdammt! Die Marketing Abteilung will auf einmal, dass die Hintergrundfarbe geändert wird. Offensichtlich gibt es alle 2 Tage eine neue Farbe die im Trend ist, also sollte das nach möglichkeit in einer Varriable hinterlegt werden.

Bevor es losgeht muss noch auf den Branch step3 gewechselt werden:

```.term1
git checkout step3
```

In diesem Beispiel gibt es nun eina Varriable die wir in unserem Playbook setzen können:

```yaml
---
- hosts: localhost
  vars:
    background_color: red
  roles:
    - httpd
    - website
```
Das erklärte Ziel unseres Marketings ist, dass der neue Hintergrund auf jeden Fall mit **ROT** anfangen sollten. Damit dies nun auch auf unserer Website wirkung zeigt, muss das Playbook erneut ausgeführt werden:

```.term1
ansible-playbook playbook.yaml
```

```
$ ansible-playbook playbook.yaml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the
implicit localhost does not match 'all'

PLAY [localhost] **********************************************************************

TASK [Gathering Facts] ****************************************************************
ok: [localhost]

TASK [httpd : Install apache2] ********************************************************
ok: [localhost]

TASK [httpd : Check if Namenode is running] *******************************************
ok: [localhost]

TASK [httpd : start apache] ***********************************************************
skipping: [localhost]

TASK [copy over website data] *********************************************************
changed: [localhost]

TASK [website : add color to css] *****************************************************
changed: [localhost]

PLAY RECAP ****************************************************************************
localhost                  : ok=5    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

Wenn wir alles richtig gemacht haben, sollte nun auch der Hintergrund oben rot statt grün sein:

[**Hier gehts zum Webserver**](/){:data-term=".term1"}{:data-port="80"}



