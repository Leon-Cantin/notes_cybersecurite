* SOAP
* REST
* WSDL

Fuzzer pour trouver des pages d'API et des paramètres

```shell-session
dirb http://<TARGET IP>:3002
ffuf -w "/usr/share/SecLists/Discovery/Web-Content/burp-parameter-names.txt" -u 'http://<TARGET IP>:3002/wsdl?FUZZ' -fs 0 -mc 200
```
[common-api-endpoints-mazen160.txt](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/common-api-endpoints-mazen160.txt)

## SOAP Spoofing
Spécifier une action non permise dans l'en-tête et une action permise dans le XML. Ajouter les params de l'action non permise sous l'action permise