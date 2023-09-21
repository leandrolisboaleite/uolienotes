---
description: É necessario saber a senha do usuario
---

# /usr/bin/pkexec



```
find / -perm -4000 2>/dev/null | grep pkexec

Abrir dois terminais

1° terminal
echo $$
>999999 - Usar este numero no 2° Terminal antes de rodar o proximo comando

pkexec /bin/bash

2° terminal
pkttyagent -p numero

```

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption><p>Exemplo</p></figcaption></figure>

