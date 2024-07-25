Essentiellement une base de données contenant divers objets comme ordinateurs, utilisateurs, politiques, groupes d'objets, ...
Organisé de façon hierarchique.

```shell-session
INLANEFREIGHT.LOCAL/
├── ADMIN.INLANEFREIGHT.LOCAL
│   ├── GPOs
│   └── OU
│       └── EMPLOYEES
│           ├── COMPUTERS
│           │   └── FILE01
│           ├── GROUPS
│           │   └── HQ Staff
│           └── USERS
│               └── barbara.jones
├── CORP.INLANEFREIGHT.LOCAL
└── DEV.INLANEFREIGHT.LOCAL
```
dépendant du DNS, donc contient souvent un DNS
Organizational Units (OU) comme un dossier qui possède des machines et des usagers
Domain Controller (DC) s'occupe de l'authentification.
domain admin  est admin sur un domaine
enterprise admin est admin sur tous les domaines
utilise LDAP

**TODO: explorer les classes .net, powerview et requêtes bloodhound**

### Forêt
Conteneur le plus élevé. contient domaines et divers objets
### Arbres
Regroupement d'AD. Un domaine à la racine. Relation de confiance parent-enfant établie quand un domaine est ajouté sous un autre.
### security principal
N'importe quoi que le système d'exploitation peut authentifier. Usager, Ordinateur, compte, processus, ...
Séparé du contrôle locale SAM
### distinguished name (DN)
`cn=bjones, ou=IT, ou=Employees, dc=inlanefreight, dc=local`
bjones travail au département de IT de inlanefreight.
### FSMO Roles
Permet un service ininterrompu en partageant des droits. Le DC à la racine de la forêt les a tous.
* `Schema Master`
* `Domain Naming Master` (one of each per forest)
* `Relative ID (RID) Master` (one per domain)
* `Primary Domain Controller (PDC) Emulator` (one per domain)
* `Infrastructure Master` (one per domain)

|**Roles**|**Description**|
|---|---|
|`Schema Master`|This role manages the read/write copy of the AD schema, which defines all attributes that can apply to an object in AD.|
|`Domain Naming Master`|Manages domain names and ensures that two domains of the same name are not created in the same forest.|
|`Relative ID (RID) Master`|The RID Master assigns blocks of RIDs to other DCs within the domain that can be used for new objects. The RID Master helps ensure that multiple objects are not assigned the same SID. Domain object SIDs are the domain SID combined with the RID number assigned to the object to make the unique SID.|
|`PDC Emulator`|The host with this role would be the authoritative DC in the domain and respond to authentication requests, password changes, and manage Group Policy Objects (GPOs). The PDC Emulator also maintains time within the domain.|
|`Infrastructure Master`|This role translates GUIDs, SIDs, and DNs between domains. This role is used in organizations with multiple domains in a single forest. The Infrastructure Master helps them to communicate. If this role is not functioning properly, Access Control Lists (ACLs) will show SIDs instead of fully resolved names.|
### Global Catalog
Un contrôleur de domaine qui contient une copie de tous les objets de son domaine et copie partielle des objets des autres domaines de la forêt. Un contrôleur de domaine standard n'a que son domaine.
### Service Principal Name (SPN)
Identifie une instance de service.
### SYSVOL
Dossier ou partage contenant des fichiers publiques du domaine comme les politiques, settings de politiques de groupes, logon/logoff scriptes et autres.
### AdminSDHolder
S'exécute régulièrement pour s'assurer que les membres des groupes par défaut protégés sont toujours les mêmes et non modifiés.
### dsHeurisitic
Liste permetant de retirer le status protégé d'un groupe par défaut pour AdminSDHolder
### adminCount
Attribut déterminant si un usager est protété par SDProp. 1 = protégé.
### NTDS.dit
coeur de l'AD `C:\Windows\NTDS\` base de donné sur le DC.
### Trust
* Transitif: la confiance est étendu aux objets que le domaine enfant à confiance en
* non-transitif: La confiance est accordé au domaine enfant seulement.

|**Trust Type**|**Description**|
|---|---|
|`Parent-child`|Domains within the same forest. The child domain has a two-way transitive trust with the parent domain.|
|`Cross-link`|a trust between child domains to speed up authentication.|
|`External`|A non-transitive trust between two separate domains in separate forests which are not already joined by a forest trust. This type of trust utilizes SID filtering.|
|`Tree-root`|a two-way transitive trust between a forest root domain and a new tree root domain. They are created by design when you set up a new tree root domain within a forest.|
|`Forest`|a transitive trust between two forest root domains.|

![[trusts-diagram.png]]

### LDAP
Authentification:
* Simple: Requête BIND au server avec mot de passe et nom d'utilisateur.
* SASL: framework qui permet la connexion avec d'autres services comme Kerberos.
Communication en texte claire. Devrait utiliser TLS.

### Group Policy Object (GPO)
regroupement de politiques
Ordre de précédence:
![[gpo_levels.png]]
* Peut appliquer "enforced" sur une politique ce qui prévient tout override