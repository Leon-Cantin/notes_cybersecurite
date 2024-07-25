# TCP

Syn-> Syn-Ack -> Ack
Serveur envoi **Syn-Ack** si port ouvert.
**Rst-Ack** quand le serveur rejète activement une connection 

# UDP
Balayages non-fiables puisque certains pare-feux peuvent bloquer le ICMP Port Unreachable de la réponse ou même donner de faux positifs. Utiliser des protocoles spécifiques pour plus de fiabilité

# Outils
## Netcat
```
nc -nvv -w [Timeout-secondes] -z [IP] [port range] 
```
-u pour UDP
## Test-NetConnection ( Windows Power Shell )
Test si une adresse répond sur un port TCP
```
Test-NetConnection -Port 445 192.168.50.151
1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("192.168.50.151", $_)) "TCP port $_ is open"} 2>$null
```