---
description: script
---

# Subfinder



```bash
/#!/bin/bash

# Atualize o sistema
sudo apt-get update -y
sudo apt-get upgrade -y

# Instale as dependências
sudo apt-get install -y git golang

# Configure o GOPATH
export GOPATH=$HOME/go
export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin

# Adicione as variáveis de ambiente ao .bashrc para persistência
echo "export GOPATH=$HOME/go" >> ~/.bashrc
echo "export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin" >> ~/.bashrc

# Clone o repositório do Subfinder
git clone https://github.com/projectdiscovery/subfinder.git $GOPATH/src/github.com/projectdiscovery/subfinder

# Entre no diretório do Subfinder
cd $GOPATH/src/github.com/projectdiscovery/subfinder/v2/cmd/subfinder

# Instale o Subfinder
go install

# Execute o Subfinder para verificar se a instalação foi bem-sucedida
$GOPATH/bin/subfinder --version

chmod +x install_subfinder.sh


./install_subfinder.sh


##Usage

##~/go/bin/subfinder -d site.com -o output.txt
```
