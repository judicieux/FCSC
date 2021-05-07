# File Format
<img src="https://media.discordapp.net/attachments/768928242467340328/840127831895834644/unknown.png"/><br/>
<h3>Ce challenge n'était pas très compliqué.</h3><br/>
## Résolution
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
