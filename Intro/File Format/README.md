# File Format
<img src="https://media.discordapp.net/attachments/768928242467340328/840127831895834644/unknown.png"/><br/>
## Résolution
Ce challenge n'était pas très compliqué. Il suffisait de comprendre l'énoncé et d'écrire un script adéquat.<br/>
En premier temps je lis attentivement l'énoncé.<br/>
Ce que je retiens:<br/>
- Une succession d'échantillons, avec chaque composante I et Q de chaque échantillon représentée par un nombre flottant compris entre 0 et 1, sur 32 bits.<br/>
- En mode petit-boutiste.<br/>
- Représentation du format:<br/>
```
+-------+-------+-------+-------+-------+-------+     +-------+-------+
|  i_0  |  q_0  |  i_1  |  q_1  |  i_2  |  q_2  | ... |  i_n  |  q_n  |
| (f32) | (f32) | (f32) | (f32) | (f32) | (f32) | ... | (f32) | (f32) |
+-------+-------+-------+-------+-------+-------+     +-------+-------+
```
- Le fichier ```challenge.iq``` contient un signal représenté sous la forme décrite plus haut. Vous devez séparer les ```composantes I et Q``` et calculer le hash ```SHA256``` résultant:
```
hash = SHA256(i_0 | i_1 | ... | i_n | q_0 | q_1 | ... | q_n)
flag = FCSC{<hash>}
```
Je commence à faire les tests.<br/>
```py
>>> f = open("challenge.iq", "rb")
>>> print(f.read(4))
b' \xce\xbb>'
```
On voit les 4 premiers octets du fichier.<br/>
On sait que ce sont des nombres flottants.<br/>
```py
>>> import struct
>>> f = open("challenge.iq", "rb")
>>> float = f.read(4)
>>> print(struct.unpack("f", float))
(0.3668069839477539,)
```
Bien, maintenant je suis sûr que ce sont des nombres flottants.<br/>
Je fais une boucle afin d'itérer à travers tous les bytes.<br/>
J'utilise le ```modulo 2``` pour disperser les ```composantes I et Q```.<br/>
```py
file = open("challenge.iq", "rb")
nb_floats = 0
i_bytes = []
q_bytes = []
while True:
	iq = file.read(4)
	if not iq:
		break
	nb_floats += 1
	if(nb_floats % 2) == 0:
		i = iq
		i_bytes.append(i)
	else:
		q = iq
		q_bytes.append(q)
print(len(i_bytes))
print(len(q_bytes))
```
Quand j'exécute le script je vois:<br/>
```cmd
C:\Users\g3zb0yy\AppData\Local\Programs\Python\Python38>python main.py
1024
1024
```
Les deux bytearrays ont ```1024``` octets de taille.<br/>
Il me reste plus qu'à assembler les deux frames dans deux bytearrays et de les concaténer.<br/>
Pour finir on hash le résultat en ```SHA256```.<br/>
## Script
```py
import hashlib

file = open("challenge.iq", "rb")
nb_floats = 0
i_bytes = []
q_bytes = []
while True:
	iq = file.read(4)
	if not iq:
		break
	nb_floats += 1
	if(nb_floats % 2) == 0:
		i = iq
		i_bytes.append(i)
	else:
		q = iq
		q_bytes.append(q)

i_frame = bytearray()
q_frame = bytearray()
for i in range(1024):
    i_frame += bytearray(i_array[i])
    q_frame += bytearray(q_array[i])

concat_final = q_frame + i_frame
final = hashlib.sha256(concat_final).hexdigest()
print(final)
```
J'exécute le script et comme par magie.<br/>
```cmd
C:\Users\g3zb0yy\AppData\Local\Programs\Python\Python38>python main.py
843161934a8e53da8723047bed55e604e725160b868abb74612e243af94345d7
```
Le flag est donc ```FCSC{8613f75e44d1d4c18dab8d967b0f3613244f6f1d0b5399ccd7f80d4d6a821cd2}```
