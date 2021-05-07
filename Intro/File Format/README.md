# File Format
<img src="https://media.discordapp.net/attachments/768928242467340328/840127831895834644/unknown.png"/><br/>
Ce challenge n'était pas très compliqué. Il suffisait de comprendre l'énoncé et d'écrire un script adéquat.<br/>
## Résolution
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
- Le fichier challenge.iq contient un signal représenté sous la forme décrite plus haut. Vous devez séparer les composantes I et Q et calculer le hash SHA256 résultant:
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
## Script
```py
import numpy as np
import struct
import hashlib

file = open("challenge.iq", "rb")
nb_of_float = 0
i_array = []
q_array = []
while True:
        i_q = file.read(4)
        if not i_q:
                break
        nb_of_float += 1
        if (nb_of_float % 2) == 0:
                i = i_q
                i_array.append(i)
        else:
                q = i_q
                q_array.append(q)

test = len(i_array)
test2 = len(q_array)

print("Nombre de I : " + str(test))
print("Nombre de Q : " + str(test2))

'''
for float in i_array:
        test = struct.unpack('f', float)
        print (test)
'''
frame_i = bytearray()
frame_q = bytearray()
for i in range(1024):
        frame_i += bytearray(i_array[i])
        frame_q += bytearray(q_array[i])

concat_final = frame_q + frame_i
final = hashlib.sha256(concat_final).hexdigest()
print(final)
```
