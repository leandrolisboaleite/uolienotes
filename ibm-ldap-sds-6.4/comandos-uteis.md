---
description: Comandos úteis no uso diario do LDAP
---

# Comandos Úteis

## Localização do arquivo ibmslapd.conf

Comando para editar o arquivo ibmslapd.conf

```bash
vi /opt/dsldap/idsslapd-dsldap/etc/ibmslapd.conf
```

## Monitorar conexoes ativas e outros detalhes

{% code overflow="wrap" %}
```bash
watch -n1 "/opt/ibm/ldap/V6.4/bin/idsldapsearch -h li50179 -p 389 -D cn=root -w h8xy73as -s base -b cn=monitor objectclass=* | egrep 'totalconnections|currentconnections|maxconnections|livethreads|bind|operations_waiting|maximum_operations_waiting|available_workerscurrent_workqueue_size|largest_workqueue_size|entry_cache|filter_cache|ops'"
```
{% endcode %}

## Realizar uma consulta na base&#x20;

Ldapsearch em conjunto do cadastro

{% code overflow="wrap" %}
```bash
/opt/ibm/ldap/V6.4/bin/ldapsearch -h li3927 -p 389 -D cn=root -w vb9x27q5 -b uid=CADASTRO,ou=people,o=portoseguro,dc=com,c=br objectclass=*
```
{% endcode %}

## Listar versão e Istance do LDAP

```
idsilist -a
```

## Parar o serviço do LDAP e DIRADM&#x20;

```bash
ibmslapd -I dsldap -k 
ibmdiradm -I dsldap -k 
```

## Subir o serviço do LDAP e DIRADM&#x20;

```bash
ibmslapd -I dsldap 
ibmdiradm -I dsldap
```
