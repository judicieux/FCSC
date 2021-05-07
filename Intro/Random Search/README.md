# Random Search
<img src="https://media.discordapp.net/attachments/768928242467340328/840138995673071616/unknown.png"/><br/>
Challenge web super basique, on doit trouver un moyen de voler le cookie de l'administrateur.<br/>
## Résolution
En visitant le site qui nous est donné, je vois deux points intéressants.<br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/840139522376335370/unknown.png?width=1440&height=550"/><br/>
On a un contexte d'XSS plutôt récurrent où on peut contacter l'administrateur.<br/>
On a notamment un searchbar à notre disposition. Je décide m'attaquer dessus.<br/>
Premièrement je teste cette payload ```<svg/onload=prompt();>```.<br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/840140153799311380/unknown.png?width=1440&height=470"/><br/>
Le searchbar est vulnérable aux attaques XSS. On a donc notre point d'injection.<br/>
