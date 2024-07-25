Questions:
* Quel compte exécute cette tâche
* Qu'est-ce qui déclenche
* Quelles actions sont exécutés

N'a pas de binaire mais des programs et des scripts spécifié par les actions

# Lister
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
get-scheduledtask
schtasks /query /fo LIST /v
schtasks /query /fo LIST /v | findstr /b /c "Task To Run"
tasklist /v
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Simplement remplacer le fichier exécutable par un que l'on contrôle.

# PowerUp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Get-ModifiableScheduledTaskFile
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 (pas encore fontionné)