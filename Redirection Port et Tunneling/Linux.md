# Énumération
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Interfaces réseau: 
ip addr
#Routes
ip route
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# socat
Port forward 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
socat -ddd TCP-LISTEN:2345,fork TCP:10.4.50.215:5432
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# rinetd
deamon, meilleur pour un lien long terme, plus chiant pour le court terme

# nc + fifo named pipe
https://gist.github.com/holly/6d52dd9addd3e58b2fd5

# iptables
Requière root
Doit activer forwarding sur l'interface en écrivant “1” à ```/proc/sys/net/ipv4/conf/[interface]/forwarding```

# linpeas.sh
apparement qu'il peut?