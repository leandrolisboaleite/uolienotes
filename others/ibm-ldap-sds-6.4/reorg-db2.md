# Reorg DB2

#### Pre\_reorg.sh

```bash
# parar servicos do ldap. mantem db2 no ar
echo -n "Parando SERVICOS LDAP E DIRADM ... "
#/usr/bin/idsslapd -I dsldap_1 -k
/usr/bin/idsslapd -I dsldap_4 -k
#/usr/bin/idsdiradm -I dsldap_1 -k
/usr/bin/idsdiradm -I dsldap_4 -k
echo "[ OK ]"

# extrai backup das bases
echo -n "Criando BACKUP antes do REORG (demora um pouco) ... "
#mkdir -p /opt/backup/reorg/dsldap_1
mkdir -p /opt/backup/reorg/dsldap_4
#time /usr/bin/idsdbback -I dsldap_1 -k /opt/backup/reorg/dsldap_1/ -b /opt/dsldap_1/idsslapd-dsldap_1/logs/dbback_210819.log
time /usr/bin/idsdbback -I dsldap_4 -k /opt/backup/reorg/dsldap_4/ -b /opt/dsldap_4/idsslapd-dsldap_4/logs/dbback_210819.log
du -csh /opt/backup/reorg/*
echo "[ OK ]"

```

#### Reorg.sh

```bash
INSTANCE=$USER


echo "INSTANCIA [ $INSTANCE ]"
echo -n "Rodando REORG em TODAS AS TABELAS (demora um pouco) ... "
db2 connect to $INSTANCE
time db2 reorgchk update statistics on table all > reorgchk_200423.out
db2 -x "select 'reorg table',substr(rtrim(tabschema)||'.'||rtrim(tabname),1,50),';'from syscat.tables where type = 'T' " > reorg_offline_200423.out
time db2 -tvf  reorg_offline_200423.out
echo "[ OK ]"

echo -n "Rodando MAINT para REINDEXAR ATRIBUTOS (demora um pouco) ... "
time /opt/ibm/ldap/V6.4/sbin/idsdbmaint -I $INSTANCE -i -b /opt/$INSTANCE/idsslapd-$INSTANCE/logs/dbmaint_200423.log
echo "[ OK ]"
```

#### Post\_reorg.sh

```bash
##########################
# iniciar servicos do ldap
##########################

echo -n "Iniciando SERVICOS LDAP E DIRADM ... "
#/usr/bin/idsslapd -I dsldap_1
/usr/bin/idsslapd -I dsldap_4
#/usr/bin/idsdiradm -I dsldap_1
/usr/bin/idsdiradm -I dsldap_4
echo "[ OK ]"

##########################
# atualizar catalogos e indices
##########################

echo -n "Rodando RUNSTATS para ATUALIZAR CATALOGS E INDICES (demora um pouco) ... "
#time /usr/bin/idsrunstats -I dsldap_1
time /usr/bin/idsrunstats -I dsldap_4
echo "[ OK ]"

echo "-- PROCEDIMENTO DE REORG FINALIZADO! --"
```
