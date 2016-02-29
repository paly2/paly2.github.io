---
layout: post
title: Utiliser SMTP avec python (et apprendre à l'implémenter pour d'autres langages) pour envoyer des e-mails
---

Le protocle SMTP permet de communiquer avec un serveur... SMTP pour envoyer des e-mails. Vous vous êtes peut-être déjà demandé comment envoyer automatiquement des messages depuis un programme : c'est ce que nous allons faire en utilisant le module python `smtplib` et en comprenant le fonctionnement du protocole pour le rendre facile à implémenter dans d'autres langages (sans utiliser de bibliothèque), voire pour ré-implémenter le module python `smtplib`.

# 1. Le serveur SMTP

Puisque vous voulez envoyer un message, je me permets de supposer que vous disposez de l'adresse e-mail du destinataire. Elle se termine en `@<nom_de_domaine>`. Exemples de noms de domaines: `gmail.com`, `sfr.fr`, `laposte.net`... Ils disposent de sous-domaine, c'est-à-dire d'un "mot" supplémentaire à l'avant séparé du domaine principal par un point.

Pour recevoir des e-mails depuis un serveur, on utilise un serveur POP3 (avec généralement donc le sous-domaine pop, donc par exemple pour gmail `pop.gmail.com` (bien que `pop.gmail.com` soit *aussi* un serveur SMTP, mais bon, c'est parce que chez Google ils ne savent plus dans quel serveur ils tombent tellement ils en ont).

Mais ce qui nous intéresse dans notre cas, c'est d'envoyer des e-mails, et pour trouver l'adresse d'un serveur SMTP, utilisez le sous-domaine en `smtp.` et *inmanquablement* ça marchera (ou du moins, je n'ai jamais vu de cas contraire).

Revenons à notre programme. Si vous disposez de l'adresse e-mail, en Python, vous pouvez récupérer le nom de domaine ainsi (la variable `email_adress` contient l'adresse e-mail du destinataire, au cas où quelqu'un ne l'aurait pas déduit tout seul):

`domain_name = email_adress.split('@')[1]`  
`smtp_server_name = 'smtp.'+domain_name`

N'oubliez pas que je vous montre en python, mais que rien ne vous empêche de le faire dans un autre langage ! En C par exemple, ce serait plutôt (il faut que `string.h` soit inclu, ne l'oubliez pas si vous utilisez ce bout de code):

`char* domain_name = NULL;`  
`char smtp_server_name[1000] = "smtp.";`  
`domain_name = strchr(email_adress, '@')+1;`  
`strcat(smtp_server_name, domain_name);`

# 2. Qui suis-je ?

## 2.1. Mon adresse IP

La première commande SMTP envoyée au serveur contient notre adresse IP. Mais comment la connaître, me demanderez-vous ? Eh bien, on pourrait se rendre sur un site comme [celui-là](whatismyip.com), mais si vous n'avez pas une IP statique, ça risque de changer si vous redémarrez votre routeur.  
Mais en fait, cette IP n'est pas d'une importance capitale. Si vous envoyez `127.0.0.1` ou `localhost`, qui correspond normalement à votre boucle locale (en gros, si vous dites au serveur SMTP "Je suis moi !"), ça marchera très bien et il ne devrait pas plus vous embêter (mais s'il le fait quand même, alors vous serez obligé de connaître votre vraie IP publique).

Pour le moment, on va se contenter donc de : votre IP = 127.0.0.1 (sauf si votre serveur SMTP refuse vraiment).

## 2.2. Mon adresse e-mail

Théoriquement, un serveur SMTP n'est pas obligé de vous demander votre adresse e-mail, mais en pratique, il est quasiment impossible de faire sans. Vous aurez donc besoin d'une adresse e-mail. Mais si vous contiez rester anonyme, ne vous inquiétez pas, normalement ça marche même si l'adresse e-mail que vous donnez n'existe pas :>>

## 2.3. Authentification ?!

Ici, ça peut commencer à se corser. Soit le port 25 de votre serveur est ouvert et si vous l'employez *et que le serveur respecte les normes* (deuxième condition qui n'est malheuresement pas toujours le cas...), pour n'aurez pas besoin d'authentification et vous pouvez passer à l'étape suivante. Mais si vous êtes obligé d'employer le port 587 (ou 465, mais on en reparlera plus tard de celui-là), vous devrez vous authentifier.  
Si vous n'avez pas de compte chez votre serveur SMTP, vous devrez en créer un. Et là, pfiouuu, finis tous les espoirs de rester anonyme (désolé...) ; mais c'est pas si grave, vous n'avez qu'à remplir votre questionnaire de fausses informations si vraiment vous tenez à l'anonymat pour une raison obscure...

Vous devrez donc obtenir un mot de passe et un indentifiant. Ensuite, si vous voulez implémenter un client SMTP **dans un autre langage sans utiliser le module python smtplib**, il va falloir les encoder en base64. Ça tombe bien, en python on a une fonction toute faite (même si ça ne nous servira pas parce que en python on a déjà la bibliothèque `smtplib`, c'est juste pour vous montrer. Je vous invite à en apprendre plus sur l'encodage base64 [ici](https://fr.wikipedia.org/wiki/Base64)). Dans le code suivant, les variables `username` et `password` contiennent respectivement votre nom d'utilisateur et votre mot de passe:

`username_base64 = username.encode('base64')`  
`password_base64 = username.encode('base64')`

# 3. Le module python smtplib

## 3.1. Création de l'objet

Il est temps de faire un peu de python !

Vous vous en doutez, la première chose à faire est d'importer le module smtplib (normalement il est pré-installé) :

`import smtplib`

La deuxième chose à faire est de créer un objet `smtplib.SMTP`. Le constructeur peut prendre plusieurs paramètres, qui sont tous optionnels (quoique le premier soit optionnel mais... indispensable.), dans l'odre : l'adresse du serveur SMTP (vous savez, celle qui commence par `smtp.`), le port que vous utilisez, votre propre adresse IP, et le temps maximal de connexion (que j'appellerai désormais *timeout*).

Vous pouvez commencer par créer un objet en précisant simplement l'adresse du serveur SMTP au constructeur, ainsi que le port 25. À la fin on exécute la méthode quit(), et on mettra le code entre les deux.

`import smtplib`  

`server = smtplib.SMTP('<serveur SMTP>', 25)`  
`# On mettra le code ici...`  
`server.quit()`

Testez votre programme. Si vous obtenez une erreur `Connexion timed out`, c'est peut-être que le serveur SMTP ne propose pas d'utiliser le port 25 (sans authentification) : essayez avec le port 587 (mais il vous faudra alors forcément une authentification, revoyez le chapitre 2.3. si ça ne vous parle pas). Si ça ne fonctionne toujours pas avec toujours la même erreur, dernière solution : le port 465 avec SSL. Il vous faudra alors un objet SMTP_SSL, mais ne vous inquiétez pas, il possède exactement les mêmes méthodes que l'objet SMTP.

`import smtplib`  

`server = smtplib.SMTP_SSL('<serveur SMTP>')`  
`# On mettra le code ici...`  
`server.quit()`

## 3.2. C'est le moment de s'authentifier...

Si vous êtes connecté sur le port 25, il est probable que vous puissiez passer cette étape. Cependant, si vous obtenez une erreur style `Authentication needed` (le texte peut changer selon le serveur), il faudra que vous reveniez ici.

Pour s'authentifier, il suffit d'utiliser la méthode `server.login(identifiant, mot de passe)`. Ils ne doivent pas être encodés en base64, la méthode `login` le fera automatiquement. Pensez bien à ajouter la ligne **entre** les lignes de déclaration de l'objet `server` et la ligne `server.quit()`. Maintenant, je ne le préciserai plus.

## 3.3. L'en-tête du message

Un message doit toujours commencer par une série de champs, un par ligne. Cela s'appelle l'en-tête.

Si vous ne faites pas d'en-tête, il se peut (et il est probable) que le serveur rejette votre message. Mettez au moins un en-tête `Date`, `Content-Type`, `Content-Transfer-Encoding` et `To` ; ensuite vous pourrez (et peut-être devrez) en rajouter `From`, `Message-ID`, ou encore `Mime-Version` (et j'en passe...).

Le format de l'en-tête `Date` doit être d'un format très particulier, que l'on peut obtenir avec :

`import time`  
`f_time = time.asctime(time.localtime(time.time())).split()`  
`date = 'Date: '+ f_time[0]+', '+f_time[2]+' '+f_time[1]+' '+f_time[4]+' '+f_time[3]+' +0100' +'\r\n'`

Je vous fais ici un exemple typique d'en-tête, vous devrez remplacer les valeurs entre chevrons (<>) ; mais vous pouvez laisser les autres, qui sont généralement très bien.

`header = ''`  
`header+=date`  
`header+='From: <votre adresse e-mail>\r\n'`  
`header+='To: <adresse e-mail du destinataire>\r\n'`  
`header+='Content-Type: text/plain; charset=US-ASCII\r\n'`  
`header+='Content-Transfer-Encoding: 7bit\r\n'`

Vous êtes peut-être étonnés par les `\r\n`, il s'agit simplement de caractères spéciaux permettant de signaler la fin d'une ligne.

## 3.4. Enfin le message !

Vous pouvez maintenant faire votre message complet !

`msg = header+'\r\n <contenu de votre message> \r\n'`

## 3.5. Et pour finir...

Maintenant, pour envoyer votre message, il suffit d'utiliser la méthode `sendmail` pour envoyer votre message :

`server.sendmail('<votre adresse e-mail>', '<adresse e-mail du destinataire>', msg)`

## 3.6. En résumé

Ça commence peut-être à faire beaucoup de méthodes... Il est temps de les rassembler.

`import smtplib
`import time

`server = smtplib.SMTP('<serveur SMTP>', 25)

`f_time = time.asctime(time.localtime(time.time())).split()
`date = 'Date: '+ f_time[0]+', '+f_time[2]+' '+f_time[1]+' '+f_time[4]+' '+f_time[3]+' +0100' +'\r\n'

`header = ''`  
`header+=date`  
`header+='From: <votre adresse e-mail>\r\n'`  
`header+='To: <adresse e-mail du destinataire>\r\n'`  
`header+='Content-Type: text/plain; charset=US-ASCII\r\n'`  
`header+='Content-Transfer-Encoding: 7bit\r\n'`  

`msg = header+'\r\n <contenu de votre message> \r\n'`  

`server.sendmail('<votre adresse e-mail>', '<adresse e-mail du destinataire>', msg)`  

`server.quit()`

# 4. Le protocole SMTP, pour ceux qui n'ont pas de bibliothèque toute faite ou pour ceux qui sont juste curieux :p

## 4.1. La rencontre client/serveur

Ce que fait le constructeur de l'objet `smtplib.SMTP` si on ne lui précise que l'adresse du serveur SMTP et le port, c'est en premier lieu se connecter. Alors, le serveur envoie normalement un message de bienvenue au client, qui commence par le code 220 s'il n'y a pas d'erreur et qui est généralement suivi d'un peu de texte.  
Ensuite, et c'est toujours le constructeur qui s'en charge, le client envoie une commande `HELO` (pour les vieux clients) ou `EHLO` (pour les plus récents), suivie de l'adresse IP du client. La première sert juste à préciser l'adresse IP et le serveur répond avec une simple ligne commençant par le code 250, alors que la deuxième demande aussi au serveur de renvoyer la liste des extensions supportées (comme `STARTTLS` qui permet de crypter la communication, ou plus intéressant, `LOGIN`, dont nous reparlerons dans le chapitre suivant).

## 4.2. "Mot de passe ou on entre pas."

Si le serveur désire une authentification (et c'est son droit si on est sur le port 587), il supporte normalement l'extension `LOGIN AUTH`, `LOGIN PLAIN`, ou les deux. Si le client est connecté sur le port 587, donc, il va envoyer :
- Soit une commande `LOGIN PLAIN` suivie du nom d'utilisateur, voire du nom d'utilisateur ET du mot de passe encodés en base64 sur une seule ligne ;
- Soit une commande `LOGIN AUTH`, à laquelle le serveur devrait répondre par `334 VXNlcm5hbWU6` (ce qui veut dire "Username:" en base64), puis le client envoie son indentifiant (encodé en base64 évidemment), puis le serveur répond par `334 UGFzc3dvcmQ6` (ce qui veut dire "Password:" en base64), puis le client envoie son mot de passe (toujours en base64).

Le serveur envoie ensuite un message qui commence par 235 si l'authentification a réussi, ou un 535 si elle a échoué. J'espère que vous êtes dans le premier cas, sinons vous ne pouvez pas continuer...

## 4.3. De qui, pour qui ?

Le client envoie ensuite deux commandes `MAIL FROM: <adresse e-mail de l'expéditeur>` et `RCPT TO: <adresse e-mail du destinataire>` (en laissant les chevrons (<>) pour une fois), auxquelles le serveur répond normalement par des 250 si tout est bon.

## 4.4. Le message

Le client envoie maintenant une commande `DATA` pour signaler qu'il va envoyer le message en lui-même. Le serveur répond par un 354, et le client envoie le message, c'est-à-dire, dans l'ordre :
- L'en-tête
- Le corps du message
- Un point seul sur une ligne pour signaler la fin du message

Le serveur devrait ensuite répondre avec un 250.

## 4.5. Good-bye, see you later !

Le client envoie un `QUIT`, le serveur répond 221, et c'est fini.

## 4.6. Perspectives

Avec ces informations sur le protocle SMTP, et avec éventuellement des informations complémentaires trouvées un peu partout, vous devriez être capable de créer votre propre bibliothèque SMTP ! Bien qu'il en existe déjà pour pratiquement tous les langages, je pense que ce serait intéressant.
