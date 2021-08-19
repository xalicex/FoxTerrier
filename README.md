# FoxTerrier

FoxTerrier is a cli version of the "OUTBOUND CONTROL RIGHTS" and "EXECUTION RIGHTS" (RDP only) BloodHound features but with csv reports.

The purpose of FoxTerrier is to identify, from a predefine user or group (hardcoded or in regex), all the interesting objects (GPO, OU, User, Group) that can be modified, and all the machine whom a user or a group can RDP to, and to output all these precious results in a beautiful csv.

FoxTerrier is also more faster than the Bloodhound interface to obtain the results (no graphic interface) and provide a synthesis of the results.

In order to work, FoxTerrier need a json file with all the information it needs (cf template.json).

* "node_start_type"       : Mandatory. Must be **"User"** or **"Group"**. 
* "node_start_name"       : Mandatory. Can be full or a regex (cf "is_node_start_regex")
* "is_node_start_regex"   : If regex is used in "node_start_name", the value must be **true** (be careful to use the value true and not the string "true"). **Default value : false**
* "mode"                  : Relation between start node and object can be direct or indirect. You can set the mode of your choice by choising between the value **"direct"**, **"indirect"** or **"all"** ("all" is "direct"+"indirect"). **Default value : "direct"**
* "objects_type"          : The target objects can be **"GPO"**, **"OU"** ,**"User"**, **"Group"**,**"RDP"** (the values must be within a list). Example : ["GPO", "OU" ,"User"]. **Default value : ["GPO", "OU" ,"User", "Group","RDP"]**
* "exclude_node"          : When using regex the results can be overwhelming. It's possible to exclude specific node from the query (the values must be within a list). Example : ["GENERIC-GROUP-11111111@MONDOMAIN.LOCAL","GENERIC-GROUP-22222222@MONDOMAIN.LOCAL"]

Example of usage (The template file must be in the same folder and same level than FoxTerrier.py) : 

```
python FoxTerrier.py
```

Example of synthesis : 

```
--- Load JSON File C:\Users\XXX\Documents\Tool\FoxTerrier\template.json
--- JSON file C:\Users\XXX\Documents\Tool\FoxTerrier\template.json Loaded ---

--- Executed queries ---

Match p=(m:Group)-[r]->(n:GPO) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' and r.isacl=true return m.name AS start_name, n.name AS end_name

Match p=(m:Group)-[r1:MemberOf*1..]->(g2:Group)-[r2]->(n:GPO) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' and r2.isacl=true return m.name AS start_name, n.name AS end_name

Match p=(m:Group)-[r]->(n:OU) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' and r.isacl=true return m.name AS start_name, n.name AS end_name

Match p=(m:Group)-[r1:MemberOf*1..]->(g2:Group)-[r2]->(n:OU) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' and r2.isacl=true return m.name AS start_name, n.name AS end_name

Match p=(m:Group)-[r]->(n:User) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' and r.isacl=true return m.name AS start_name, n.name AS end_name

Match p=(m:Group)-[r1:MemberOf*1..]->(g2:Group)-[r2]->(n:User) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' and r2.isacl=true return m.name AS start_name, n.name AS end_name

Match p=(m:Group)-[r]->(n:Group) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' and r.isacl=true return m.name AS start_name, n.name AS end_name

Match p=(m:Group)-[r1:MemberOf*1..]->(g2:Group)-[r2]->(n:Group) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' and r2.isacl=true return m.name AS start_name, n.name AS end_name

Match p=(m:Group)-[r:CanRDP]->(n:Computer) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' return m.name AS start_name, n.name AS end_name

Match p=(m:Group)-[r1:MemberOf*1..]->(g2:Group)-[r2:CanRDP]->(n:Computer) WHERE m.name =~ 'MONGROUPE_GENERIQUE-\d{11}.*@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-11111111111@MONDOMAIN.LOCAL' AND NOT m.name = 'MONGROUPE_GENERIQUE-22222222222@MONDOMAIN.LOCAL' return m.name AS start_name, n.name AS end_name

--- Summary of vulnerable object per User or Group ---

MONGROUPE_GENERIQUE-33333333333@MONDOMAIN.LOCAL : 1
MONGROUPE_GENERIQUE-66666666666@MONDOMAIN.LOCAL : 1
MONGROUPE_GENERIQUE-99999999999@MONDOMAIN.LOCAL : 1
MONGROUPE_GENERIQUE-00000000000@MONDOMAIN.LOCAL : 1
MONGROUPE_GENERIQUE-77777777777@MONDOMAIN.LOCAL : 1
MONGROUPE_GENERIQUE-44444444444@MONDOMAIN.LOCAL : 2

--- Summary of CanRDP machines per User or Group ---

MONGROUPE_GENERIQUE-33333333333@MONDOMAIN.LOCAL : 3289
MONGROUPE_GENERIQUE-66666666666@MONDOMAIN.LOCAL : 521
MONGROUPE_GENERIQUE-99999999999@MONDOMAIN.LOCAL : 141
MONGROUPE_GENERIQUE-00000000000@MONDOMAIN.LOCAL : 35
MONGROUPE_GENERIQUE-77777777777@MONDOMAIN.LOCAL : 5
MONGROUPE_GENERIQUE-44444444444@MONDOMAIN.LOCAL : 84
MONGROUPE_GENERIQUE-33333333366@MONDOMAIN.LOCAL : 300
MONGROUPE_GENERIQUE-99333333366-RED@MONDOMAIN.LOCAL : 459
MONGROUPE_GENERIQUE-55444444444@MONDOMAIN.LOCAL : 459
MONGROUPE_GENERIQUE-82333333366@MONDOMAIN.LOCAL : 697

--- Summary of vulnerable object per Type ---

GPO : 4
OU : 1
User : 1
Group : 1
CanRDP : 5990

--- Results available in My_Synthetic_Bloodhound_Report.csv ---
```
