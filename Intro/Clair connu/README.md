# Clair connu
Pour cette première WU je vous présente ce challenge, ma foi assez simple mais compliqué sur les bords.<br/>
On nous donne un fichier output.txt et un script Python.<br/>
## Apperçu script
```py
import os
from Crypto.Util.number import long_to_bytes
from Crypto.Util.strxor import strxor

FLAG = open("flag.txt", "rb").read()

key = os.urandom(4) * 20
c = strxor(FLAG, key[:len(FLAG)])
print(c.hex())
```
Le fichier flag.txt a été importé avec comme file IO rb (read binary).<br/>
On sait que la key fait 4 caractères * 20. La fonction urandom(4) return 4 bytes (octets) en format Little-endian.<br/>
En réalité ça veut dire que le blocksize de la key équivaut à 4 caractères vu que la séquence est répétée 20x d'affilé.<br/>
Connaissant le format des flags FCSC. On a déjà notre known plaintext, notamment "FCSC".<br/>
## Apperçu output
```
d91b7023e46b4602f93a1202a7601304a7681103fd611502fa684102ad6d1506ab6a1059fc6a1459a8691051af3b4706fb691b54ad681b53f93a4651a93a1001ad3c4006a825
```
et le msg xor est encodé en hex
donc tu fais d'abord un xor basique
tu fous un fromhex au niveau du msg et tu xor avec la key FCSC encodé en UTF8
puis tu fous un to hex et tu chopes les 4 premiers octets
tu les mets dans la key au lieu du FCSC et tu l'encodes en hex
et là t'obtiens ton flag
GuGu — Aujourd’hui à 09:03
Moi quand j'faisais le chall je pensais que la key faisait 80 caractères
d'où le urandom(4)*20
mais au final non car y a aucun cycle
et ça se devine facilement
le block size de la key est de 4 caractères vu que c'est le FCSC qui se répète 20x
donc chaque block de 4 octets a le même xor
du coup j'en ai déduis que c'était FCSC la key
GuGu — Aujourd’hui à 09:45
mais t'aurais très bien pu bruteforce la key de 4 caractères en soit
c'est juste que ça saute aux yeux
