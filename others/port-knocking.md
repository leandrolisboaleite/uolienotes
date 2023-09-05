# Port Knocking



```sh
#!/bin/bash

# Defina o endereço IP do host alvo
TARGET_IP="37.59.174.235"

# Defina a sequência de portas a serem "batidas"
PORT_SEQUENCE="13 37 30000 3000 1337 1972"

# Cores
GREEN='\033[0;32m'
NC='\033[0m'

# Executa o comando knock para realizar o port knocking
knock -v $TARGET_IP $PORT_SEQUENCE

# Verifica se o comando knock foi bem-sucedido

if [ $? -eq 0 ]; then
 echo -e "${GREEN}Port knocking successful!${NC}"
else
 echo "Port knocking failed."
fi
```
