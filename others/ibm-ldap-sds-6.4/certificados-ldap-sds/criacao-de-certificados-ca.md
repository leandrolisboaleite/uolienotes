---
description: >-
  Esta pagina tem como intuito auxiliar na criação e no deployment de um novo
  certificado assinado por uma CA.
---

# Criação de certificados CA

## Acessar o servidor

{% code overflow="wrap" %}
```bash
ssh -i chave.pem -o ServerAliveInterval=15 xxx.xxx.xxx.xxx
```
{% endcode %}

## Criacao do chaveiro de importacao do CA Atual

```bash
gsk8capicmd_64 -keydb -create -db ca.kdb -pw secret -stash
```

## Importar o CA ASSINADO pela empresa no chaveiro criado

{% code overflow="wrap" %}
```bash
gsk8capicmd_64 -cert -add -db server.kdb -stashed -label "CA 2026" -file cacert.pem -format ascii -trust enable
```
{% endcode %}

## Listar o chaveiro

```bash
gsk8capicmd_64 -cert -list -db server.kdb -stashed
```

## Criar uma requisicao de certificado (CSR)

{% code overflow="wrap" %}
```bash
gsk8capicmd_64 -certreq -create -db server.kdb -stashed -label ”CA 2026" -dn "CN=servidor.dominio.dominio,OU=OUDAEMPRESA,O=O DA EMPRESA" -file ldapcert_request.arm
```
{% endcode %}

## Verificar se a requisicao foi criada

```bash
gsk8capicmd_64 -certreq -list -db server.kdb -stashed
```

{% hint style="danger" %}
O arquivo .csr precisa ser assinado
{% endhint %}

## Receber o certificado assinado pela CA da Empresa

{% code overflow="wrap" %}
```bash
gsk8capicmd_64 -cert -receive -db server.kdb -stashed -file ldapcert_signed.arm
```
{% endcode %}

## Visualizar conteudo do certificado

```bash
gsk8capicmd_64 -cert -details -db server.kdb –stashed -label "CA 2026"as
```
