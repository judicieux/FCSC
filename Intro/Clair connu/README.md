# Clair connu
Pour cette première WU je vous présente ce challenge introduction, ma foi assez simple.<br/>
On nous donne un fichier output.txt et un script Python, j'en déduis que ce dernier sert à chiffrer le message contenu dans l'output.txt.<br/>
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
Le fichier flag.txt a été importé [File IO: rb (read binary)].<br/>
On sait que la key fait 4 caractères * 20. La fonction urandom(4) retourne 4 bytes (octets) randoms en format Little-endian.<br/>
En réalité, la key ne fait pas 80 caractères. Le blocksize de la key équivaut à 4 caractères vu que la séquence est répétée 20x d'affilé.<br/>
Connaissant le format des flags FCSC. On a déjà notre known plaintext, notamment "FCSC".<br/>
La fonction strxor() permet de xor deux strings (bytes-like) de la même taille.
## Apperçu output
```
d91b7023e46b4602f93a1202a7601304a7681103fd611502fa684102ad6d1506ab6a1059fc6a1459a8691051af3b4706fb691b54ad681b53f93a4651a93a1001ad3c4006a825
```
Je présume que c'est le flag xoré + encodé en hexadécimal.<br/>
## Solutions
J'ai résolu ce challenge grâce à Cyberchef mais je trouvais la manière de faire bien trop simple, donc j'ai décidé de compléter le challenge en faisant un script en Python afin de m'entraîner.
## Solution Script
```py
# g3zb0yy
import os
from Crypto.Util.strxor import strxor
"""
FLAG = open("flag.txt", "rb").read()

key = os.urandom(4) * 20
c = strxor(FLAG, key[:len(FLAG)])
print(c.hex())
"""
known_plaintext = b"FCSC"*20
file = open("output.txt", "r", encoding="utf-8").read()
flag = bytes.fromhex(file)
__first = strxor(flag, known_plaintext[:len(flag)])
key = __first.hex()[:8]
key = bytes.fromhex(key)*20
__next = strxor(flag, key[:len(flag)])
print(__next)
```
### Solution Cyberchef
Je commence par un xor basique en me munissant des éléments que j'ai.<br/>
Tout d'abord je fous un Fromhex au niveau du flag encodé.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/836528652434800700/H1x75DD9A3kAAAAAElFTkSuQmCC.png?width=1308&height=613"/><br/><br/>
Je le xor avec la key FCSC encodé en UTF8.<br/>
Ensuite je pose un To hex et je chope les 4 premiers octets étant donné que la known plaintext fait 4 caractères de long.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/836529336290246656/unknown.png?width=1297&height=613"/><br/><br/>
Je remplace la key "FCSC" par les 4 octets et je l'encode en hexadécimal.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/836529573519818782/unknown.png?width=1344&height=613"/></br><br/>
On voit qu'on a flag ce challenge.
## Avis
Quand je faisais le chall je pensais que la key faisait 80 caractères. D'où le urandom(4)\*20.<br/>
En effet, la key fait 80 caractères, mais ce sont juste 4 octets répétés 20x.<br/>
Le block size de la key est de 4 caractères vu que la key se répète 20x.<br/>
Donc chaque block de 4 octets a la même propriété xor.<br/>
