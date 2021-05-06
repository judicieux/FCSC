# BaguetteVPN 2/2
On se retrouve pour la partie la plus compliquée du challenge.<br/>
## Résolution
Pour commencer, brêve analyse du fichier ```./baguettevpn_server_app_v0.py```.<br/>
Cette fonction devrait nous print le flag.<br/>
Cependant, vous verrez qu'on sera confronté à plusieurs problèmes.<br/>
```py
@app.route("/api/secret")
def admin():
    if request.remote_addr == '127.0.0.1':
        if request.headers.get('X-API-KEY') == 'b99cc420eb25205168e83190bae48a12':
            return jsonify({"secret": os.getenv('FLAG')})
        return Response('Interdit: mauvaise clé d\'API', status=403)
    return Response('Interdit: mauvaise adresse IP', status=403)
```
