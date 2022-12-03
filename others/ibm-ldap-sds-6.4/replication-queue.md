---
description: Cheatsheet
---

# Replication Queue

## Verificar filas de replicação e falhas

{% code overflow="wrap" %}
```bash
/opt/ibm/ldap/V6.4/bin/ldapsearch -h xxxxx7 -D cn=root -w xxxxxxx -b cn=lixxx8,cn=lixxx7,ibm-replicagroup=default,c=br objectclass=* ++ibmrepl
```
{% endcode %}

## Tentar novamente as operações de replicação que tiveram falhas

{% code overflow="wrap" %}
```bash
/opt/ibm/ldap/V6.4/bin/idsldapexop -h xxxxxx -p 389 -D cn=root -w xxxxxx -op controlreplerr -ra cn=lixxx8,cn=lixxx7,ibm-replicaGroup=default,c=br -retry all
```
{% endcode %}

## Buscar um DN especifico

{% code overflow="wrap" %}
```bash
/opt/ibm/ldap/V6.4/bin/ldapsearch -h xxxxxx -p 389 -D cn=root -w xxxxxx -b C=BR uid=xxxxxxxxxxx
```
{% endcode %}

## Verificar logs por registro

```bash
/logs/slapd/saved
zgrep -A11 -B1 xxxxxxxxxxx mmdd*.gz | grep -v "bindDN"
```

## Deletar falhas na replicação

{% code overflow="wrap" %}
```bash
/opt/ibm/ldap/V6.4/bin/idsldapexop -h lixxx7 -p 389 -D cn=root -w xxxxxx -op controlreplerr -ra cn=lixxxx8,cn=lixxx7,ibm-replicaGroup=default,c=br -delete all
```
{% endcode %}
