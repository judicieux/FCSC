# BaguetteVPN 2/2
On se retrouve pour la partie la plus compliquée du challenge.<br/>
## Résolution
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
En fouillant le site, j'apperçois un point d'injection pottentiellement vulnérable à une SSRF.<br/>
Le paramètre ```/api/image?fn=``` permet de read des fichiers internes.<br/>
Je décide donc d'exploiter ce paramètre, en me munissant de ce cheatsheet plutôt complet: <a href="https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery">Link</a>.<br/>
En premier temps j'essaye  ```/api/image?fn=127.0.0.1```.<br/>
Malheureusement la requête ne passe pas, j'ajoute donc un ```@``` pour bypass les weak parsers.<br/>
