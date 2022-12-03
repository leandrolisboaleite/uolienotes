---
description: Comandos para filtro de logs dentro do SDS LDAP
---

# Filtro de logs

## Audit Log

```
$ tail -f /opt/dsldap/idsslapd-dsldap/logs/audit.log
```

## Audit Log - Filtro Add Modify Delete

Filtro de audit log separando apenas alteracoes com Add Modify Delete

{% code overflow="wrap" %}
```bash
tail -f /opt/dsldap/idsslapd-dsldap/logs/audit.log | egrep "Add|Modify|Delete" -A5 -B3
```
{% endcode %}

## Ibmslapd.log

Filtro para o arquivo de log diradm.log

{% code overflow="wrap" %}
```bash
tail -f /opt/dsldap/idsslapd-dsldap/logs/ibmdiradm.log 
```
{% endcode %}

## diradm.log

Filtro para o arquivo de log diradm.log

```
tail -f /opt/dsldap/idsslapd-dsldap/logs/ibmdiradm.log 
```

## Leitura de logs comprimidos

Os logs da aplicação são comprimidos e armazenados com o padrao da data DDMMYYYY

{% code overflow="wrap" %}
```bash
zgrep CADASTRO /logs/slapd/saved/000000.gz
zgrep 99999999999 /logs/slapd/saved/0622*.gz
```
{% endcode %}
