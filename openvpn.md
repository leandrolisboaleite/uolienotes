# OpenVPN

#### O Kali atualizou openvpn da versão 2.5.6-1 para 2.6.0. Após esta atualização o problema ocorreu.

\


**Execute os seguintes comandos no seu Kali:**\
\
\# apt remove --purge openvpn\
\# wget http://sft.if.usp.br/debian/pool/main/o/openvpn/openvpn\_2.5.1-3\_amd64.deb\
\# dpkg -i openvpn\_2.5.1-3\_amd64.deb\
\# apt-mark hold openvpn\
\
O último comando serve para marcar o pacote openvpn e ele não ser atualizado.\
Quando sair uma versão estável podemos liberá-lo novamente para atualização com o comando :\
\
\# apt-mark unhold openvpn\
\
\
Podemos verificar as novas versões "candidatas" para o openvpn com o comando :\
\
\# apt policy openvpn\
\
OBS: Foi utilizado o Kali v2022.2 para os testes.\
\
\
Caso ocorra o erro\
"OpenSSL: error:0A00018E:SSL routines::ca md too weak" ou "Cannot use certificate"\
durante a conexão via openvpn.\
\
Adicione a seguinte linha no final do seu arquivo ovpn:\
tls-cipher "DEFAULT:@SECLEVEL=0"\
