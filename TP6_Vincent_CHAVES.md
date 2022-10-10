Vincent CHAVES - 3ICS

*************************************************
## /!\ Je tiens à préciser que comme vu avec Monsieur BEZET-TORRES, ma 2ème VM neuve avait des problèmes dès le début qui m'ont beaucoup retardé pour ce TP et la suite : impossibilité d'installer des paquets donc le serveur DHCP, jusqu'à 30 secondes parfois pour éxecuter une simple commande cd, impossibilité de faire un apt update, etc. J'ai finalement inversé la VM cliente et la VM serveur pour pouvoir installer le serveur DHCP, mais ça m'a tout de même posé problème lorsque je devais installer des paquets sur la 2ème VM (désormais cliente). J'ai pris du retard sur les derniers TP à cause de ça.
*************************************************

# TP 6 - Services réseau

## Exercice 1. Adressage IP

| Sous-réseau | Adresse du sous-réseau | Masque | Nb d'adresses voulues | Nb d'adresses disponibles | Adresse de broadcast | Adresse de la  première machine | Adresse de la dernière machine |
|:-----------:|:----------------------:|:------:|:---------------------:|:-------------------------:|:--------------------:|:-------------------------------:|:------------------------------:|
|      1      | 172.16.0.0             | /26    | 38                    | 62                        | 172.16.0.63          | 172.16.0.1                      | 172.16.0.62                    |
|      2      | 172.16.0.64            | /26    | 33                    | 62                        | 172.16.0.127         | 172.16.0.65                     | 172.16.0.126                   |
|      3      | 172.16.0.128           | /26    | 52                    | 62                        | 172.16.0.191         | 172.16.0.129                    | 172.16.0.190                   |
|      4      | 172.16.0.192           | /26    | 35                    | 62                        | 172.16.0.255         | 172.16.0.193                    | 172.16.0.254                   |
|      5      | 172.16.1.0             | /26    | 34                    | 62                        | 172.16.1.63          | 172.16.1.1                      | 172.16.1.62                    |
|      6      | 172.16.1.64            | /26    | 37                    | 62                        | 172.16.1.127         | 172.16.1.65                     | 172.16.1.126                   |
|      7      | 172.16.1.128           | /27    | 25                    | 30                        | 172.16.1.159         | 172.16.1.129                    | 172.16.1.158                   |

## Exercice 2. Préparation de l’environnement

### 1. VM éteintes, utilisez les outils de configuration de VirtualBox pour mettre en place l’environnement
décrit ci-dessus.
- Je modifie les interfaces réseaux sur mes 2 VMs :
- Client :
- ![image](https://user-images.githubusercontent.com/113091304/194260982-3f8974f4-6c1f-499b-a57d-1bb6d203dba9.png)
- Serveur :
- ![image](https://user-images.githubusercontent.com/113091304/194261310-f32acab7-904e-4939-9607-4add586854d0.png)
- (Après des problèmes sur la VM CHAV2 j'ai finis par inverser les configurations ci-dessus pour utiliser la première VM comme serveur)

### 2. Démarrez le serveur et vérifiez que les interfaces réseau sont bien présentes. A quoi correspond l’interface appelée lo ?
- Client :
- ![image](https://user-images.githubusercontent.com/113091304/194266770-24917f51-d77c-4322-956f-ef942095326e.png)
- Serveur :
- ![image](https://user-images.githubusercontent.com/113091304/194266838-e2c33e13-e666-48ea-990d-a4185eb9e1c4.png)
- Les interfaces réseau sont bien présentes.
- L'interface lo représente la boucle locale localhost.

### 3. Dans les versions récentes, Ubuntu installe d’office le paquet cloud-init lors de la configuration du système. Ce paquet permet la configuration et le déploiement de machines dans le cloud via un script au démarrage. Nous ne nous en servirons pas et sa présence interfère avec certains services (en particulier le changement de nom d’hôte) ; par ailleurs, vos machines démarreront plus rapidement. Désinstallez complètement ce paquet (il faudra penser à le faire également sur le client ensuite.)
- ![image](https://user-images.githubusercontent.com/113091304/194271154-a6240cd2-2af5-4e03-ae68-f1047e6ebfe9.png)
- ![image](https://user-images.githubusercontent.com/113091304/194271956-ac07fe78-2f18-4a94-b436-a55d4d9d20e9.png)
- Je fais pareil sur le client.

### 4. Les deux machines serveur et client se trouveront sur le domaine tpadmin.local. A l’aide de la commande hostnamectl renommez le serveur (le changement doit persister après redémarrage, donc cherchez les bonnes options dans le manuel !). On peut afficher le nom et le domaine d’une machine avec les commandes hostname et/ou dnsdomainname ou en affichant le contenu du fichier /etc/hostname.
- ![image](https://user-images.githubusercontent.com/113091304/194277613-2e1ca571-c666-416b-975a-dde4c37172ef.png)
- Après le reboot :
- ![image](https://user-images.githubusercontent.com/113091304/194279023-655dc996-fdbd-4293-9bc0-fa852b4b8b2f.png)

## Exercice 3. Installation du serveur DHCP
### Un serveur DHCP permet aux ordinateurs clients d’obtenir automatiquement une configuration réseau (adresse IP, serveur DNS, passerelle par défaut…), pour une durée déterminée. Ainsi, dans notre cas, l’interfaces réseau de client doit être configurée automatiquement par serveur. Le réseau local tpadmin.local a pour adresse 192.168.100.0/24 (on aurait pu choisir une autre adresse ; attention, 192.168.1.0/24 est souvent réservée, par exemple par votre FAI).

### 1. Sur le serveur, installez le paquet isc-dhcp-server. La commande systemctl status isc-dhcp-server devrait vous indiquer que le serveur n’a pas réussi à démarrer, ce qui est normal puisqu’il n’est pas encore configuré (en particulier, il n’a pas encore d’adresses IP à distribuer).
- Pour installer le paquet je fais : <code> sudo apt-get install isc-dhcp-server
- Une fois installé, je lance la commande "systemctl status isc-dhcp-server" et cela me retourne qu'il n'a pas réussi à démarrer :
- ![image](https://user-images.githubusercontent.com/113091304/194728509-428cfb6e-a729-4985-9b77-615626ac4714.png)

### 2. Un serveur DHCP a besoin d’une IP statique. Attribuez de manière permanente l’adresse IP 192.168.100.1 à l’interface réseau du réseau interne. Vérifiez que la configuration est correcte.
- ![image](https://user-images.githubusercontent.com/113091304/194729143-6597c139-95c6-421e-adda-7394b7841214.png)

### 3. La configuration du serveur DHCP se fait via le fichier /etc/dhcp/dhcpd.conf. Faites une sauvegarde du fichier existant sous le nom dhcpd.conf.bak puis éditez le fi chier dhcpd.conf avec les informations suivantes
- Je crée le fichier de backup :
- ![image](https://user-images.githubusercontent.com/113091304/194729276-54574983-79df-4b17-9bb3-04995d1f89af.png)
- Je modifie le fichier de conf :
- ![image](https://user-images.githubusercontent.com/113091304/194730041-97c6a46b-2346-48f5-8454-295ababba4a9.png)

### A quoi correspondent les deux premières lignes ?
- <code> default-lease-time 120;</code> correspond au bail par défaut, c'est à dire qu'une adresse IP fournie par le serveur DHCP devra être renouvelée au bout de 120 secondes.
- <code> max-lease-time 600;</code> correspond au temps maximum de validité d'une adresse IP, ce après quoi elle ne sera plus attribuée. Le client devra alors faire une nouvelle demande de DHCP sans quoi il n'aura plus d'adresse IP.
 
### 4. Editez le fichier /etc/default/isc-dhcp-server afin de spécifier l’interface sur laquelle le serveur doit écouter.
- J'ajoute l'interface ens224 :
- ![image](https://user-images.githubusercontent.com/113091304/194730921-b6c4a0cf-5d1c-4e92-84db-a2f0fa9a183d.png)

### 5. Validez votre fichier de configuration avec la commande dhcpd -t puis redémarrez le serveur DHCP (avec la commande systemctl restart isc-dhcp-server) et vérifiez qu’il est actif.
- Malgré les 2 commandes effectuées (voir le screenshot ci-dessous), le serveur n'est toujours pas actif :
- ![image](https://user-images.githubusercontent.com/113091304/194782594-c1559a60-a3a9-46d0-a0a5-7f6655adac46.png)
- Après avoir lu les erreurs, j'active l'interface ens224 de cette manière :
- ![image](https://user-images.githubusercontent.com/113091304/194782800-0f221538-5b69-4f49-bb40-dd5b148dbcea.png)
- Je restart ensuite le serveur DHCP puis je vérifie son status :
- ![image](https://user-images.githubusercontent.com/113091304/194782864-b3135669-411c-4f38-85b2-12e3d80d735d.png)
- Il est enfin actif.
 
 ### 6. Notre serveur DHCP est configuré ! Passons désormais au client. Si vous avez suivi le sujet du TP 1, le client a été créé en clonant la machine virtuelle du serveur. Par conséquent, son nom d’hôte est toujours serveur. Vérifiez que la carte réseau du client est débranchée, puis démarrez le client (il est possible qu’il mette un certain temps à démarrer : ceci est dû à l’absence de connexion Internet). Comme pour le serveur, désinstallez ensuite cloud-init, puis modifiez le nom de la machine (elle doit s’appeler client.tpadmin.local).
- <code>sudo hostnamectl set-hostname client.tpadmin.local</code>
- Les commandes sudo sont longues à s'éxecuter alors je modifie le fichier hosts :
- ![image](https://user-images.githubusercontent.com/113091304/194783981-bc65231a-d559-4699-983b-79d8b56ca3db.png)
- Il n'y a désormais plus de latence
 
 ###7. La commande tail -f /var/log/syslog affiche de manière continue les dernières lignes du fichier de log du système (dès qu’une nouvelle ligne est écrite à la fin du fichier, elle est affichée à l’écran). Lancez cette commande sur le serveur, puis connectez la carte réseau du client et observez les logs sur le serveur. Expliquez à quoi correspondent les messages DHCPDISCOVER, DHCPOFFER, DHCPREQUEST, DHCPACK. Vérifiez que le client reçoit bien une adresse IP de la plage spécifiée précédemment.
- ![image](https://user-images.githubusercontent.com/113091304/194784669-15156a44-69ce-4c80-ad9c-e129b0147399.png)
- DHCP REQUEST = requête du client pour obtenir une adresse IP
- DHC PACK = confirmation de réception de la requête
- DHCP DISCOVER = découverte d'une adresse disponible
- DHC POFFER = envoi de l'adresse IP au client

 ### 8. Que contient le fichier /var/lib/dhcp/dhcpd.leases sur le serveur, et qu’afficle la commande dhcp-lease-list ?
- Le fichier /var/lib/dhcp/dhcpd.leases contient la liste des bails finis :
- ![image](https://user-images.githubusercontent.com/113091304/194785072-bef83ae6-501c-4a04-843d-ed6d41189d3d.png)
- La commande dhcp-lease-list affiche l'adresse MAC, IP et la date de fin du bail d'un client :
- ![image](https://user-images.githubusercontent.com/113091304/194785210-23c73b39-de60-402d-b6cf-c02a0cb3361c.png)

 ### 9. Vérifiez que les deux machines peuvent communiquer via leur adresse IP, à l’aide de la commande ping.
 - Du client au serveur, ça ping :
 - ![image](https://user-images.githubusercontent.com/113091304/194784960-5b52179b-9292-4dce-b1c2-a25f1dc05262.png)
 - Du serveur au client, ça ping :
 - ![image](https://user-images.githubusercontent.com/113091304/194784982-903633be-32d9-4b99-a6fb-371e7ae440cd.png)
 
 ### 10. Modifiez la configuration du serveur pour que l’interface réseau du client reçoive l’IP statique 192.168.100.20
- Je note l'adresse MAC du client :
- ![image](https://user-images.githubusercontent.com/113091304/194785514-b7578698-6cdb-4305-9f06-11319d7f32c9.png)
- Puis sur le serveur :
- ![image](https://user-images.githubusercontent.com/113091304/194785421-c3f552ca-d5d9-4ae7-a036-85995ecce3a7.png)
- ![image](https://user-images.githubusercontent.com/113091304/194785621-be46c6ba-79fe-49f6-acbc-1bd218fa7ac8.png)

 ### Vérifiez que la nouvelle configuration a bien été appliquée sur le client (éventuellement, désactivez puis réactivez l’interface réseau pour forcer le renouvellement du bail DHCP, ou utilisez la commande dhclient -v).
- Malgré ça, l'IP attribuée au client n'est pas celle voulue :
- ![image](https://user-images.githubusercontent.com/113091304/194786075-e1f51b61-fb56-49f5-8d37-bb9a85815b67.png)
- J'ai alors relancé le serveur DHCP avec la commande :
- ![image](https://user-images.githubusercontent.com/113091304/194785987-b8ea1ccf-3a02-4dfc-93d1-285f0243015d.png)
- Puis j'ai refais <code>dhclient -v</code> sur le client :
- ![image](https://user-images.githubusercontent.com/113091304/194786092-a5efdc15-b54b-4ed2-a777-4907611ca585.png)
- Cette fois l'IP est bien l'IP fixe voulue.

## Exercice 4. Donner un accès à Internet au client

### 1. La première chose à faire est d’autoriser l’IP forwarding sur le serveur (désactivé par défaut, étant donné que la plupart des utilisateurs n’en ont pas besoin). Pour cela, il suffit de décommenter la ligne net.ipv4.ip_forward=1 dans le fichier /etc/sysctl.conf. Pour que les changements soient pris en compte immédiatement, il faut saisir la commande sudo sysctl -p /etc/sysctl.conf.
- ![image](https://user-images.githubusercontent.com/113091304/194858111-991dc02d-8137-41f7-a04b-99176055fa50.png)
- ![image](https://user-images.githubusercontent.com/113091304/194858224-ac4c9fc0-27f3-4488-a0bb-fd640b626d41.png)
- ![image](https://user-images.githubusercontent.com/113091304/194858453-55a509c7-38bc-4563-88cd-f6d35af1b65c.png)

### 2. Ensuite, il faut autoriser la traduction d’adresse source (masquerading) en ajoutant la règle iptables suivante : sudo iptables --table nat --append POSTROUTING --out-interface enp0s3 -j MASQUERADE
- ![image](https://user-images.githubusercontent.com/113091304/194859952-6cfc258d-fc47-413d-b5d0-9d8dd73f2083.png)

## Exercice 5. Installation du serveur DNS

### 1. Sur le serveur, commencez par installer bind9, puis assurez-vous que le service est bien actif
- ![image](https://user-images.githubusercontent.com/113091304/194870626-49f47f29-8a7d-42e3-9f25-06f67a11d6ad.png)
- ![image](https://user-images.githubusercontent.com/113091304/194870781-345ef661-b509-41cb-9e4c-a3a8f70fa002.png)

### 2. A ce stade, Bind n’est pas configuré et ne fait donc pas grand chose. L’une des manières les simples de le configurer est d’en faire un serveur cache : il ne fait rien à part mettre en cache les réponses de serveurs externes à qui il transmet la requête de résolution de nom.  Le binaire (= programme) installé avec le paquet bind9 ne s’appelle ni bind ni bind9 mais named... Nous allons donc modifier son fichier de configuration : /etc/bind/named.conf.options. Dans ce fichier, décommentez la partie forwarders, et à la place de 0.0.0.0, renseignez les IP de DNS publics comme 1.1.1.1 et 8.8.8.8 (en terminant à chaque fois par un point virgule). Redémarrez le serveur bind9.
- ![image](https://user-images.githubusercontent.com/113091304/194881041-dacacc58-8336-476c-958a-6641d5b3923b.png)
- ![image](https://user-images.githubusercontent.com/113091304/194882805-8165accc-3c6d-429b-8ebb-e4892bc868ad.png)
 - ![image](https://user-images.githubusercontent.com/113091304/194882340-432f9a13-5322-4863-a6f1-02d1896a789a.png)
 
### 3. Sur le client, retentez un ping sur www.google.fr. Cette fois ça devrait marcher ! On valide ainsi la configuration du DHCP effectuée précédemment, puisque c’est grâce à elle que le client a trouvé son serveur DNS. 

### 4. Sur le client, installez le navigateur en mode texte lynx et essayez de surfer sur fr.wikipedia.org (bienvenue dans le passé...)
- ![image](https://user-images.githubusercontent.com/113091304/194886777-55d57174-bbea-4ec0-8249-a00433af9fe8.png)

## Exercice 6. Configuration du serveur DNS pour la zone tpadmin.local

### 1. Modifiez le fichier /etc/bind/named.conf.local et ajoutez les lignes suivantes
- ![image](https://user-images.githubusercontent.com/113091304/194892810-de6a83bb-54c0-4c79-a671-36c5249f6a83.png)
- ![image](https://user-images.githubusercontent.com/113091304/194893907-73f48993-d8ba-4490-8437-555d87965ade.png)

### 2. Créez une copie appelée db.tpadmin.local du fichier db.local. Ce fichier est un fichier configuration typique de DNS, constitué d’enregistrements DNS (cf. cours). Dans le nouveau fichier, remplacez toutes les références à localhost par tpadmin.local, et l’adresse 127.0.0.1 par l’adresse IP du serveur.
- ![image](https://user-images.githubusercontent.com/113091304/194894234-72c39e5c-cba6-4103-a00a-df563343832d.png)
- ![image](https://user-images.githubusercontent.com/113091304/194946018-ea5de3fc-f609-46de-9040-22bf96128aa6.png)

### 3. Maintenant que nous avons configuré notre fichier de zone, il reste à configurer le fichier de zone inverse, qui permet de convertir une adresse IP en nom
- ![image](https://user-images.githubusercontent.com/113091304/194895403-83ba9049-2f39-4a30-ad53-a57d3b934671.png)
- ![image](https://user-images.githubusercontent.com/113091304/194896019-c0968bbe-e19f-4fa2-96cc-c3f0fb7ce867.png)
 
### Créez ensuite le fichier db.192.168.100 à partir du fichier db.127, et modifiez le de la même manière
que le fichier de zone. Sur la dernière ligne, faites correspondre l’adresse IP avec celle du serveur
(Attention, il y a un petit piège !).
- ![image](https://user-images.githubusercontent.com/113091304/194943333-d7d12bf3-ef34-4f4b-b6a7-1bf3851f2b67.png)
- ![image](https://user-images.githubusercontent.com/113091304/194945221-806a664f-3e0d-42c1-8392-6693eee3c819.png)
 
### 4. Utilisez les utilitaires named-checkconf et named-checkzone pour valider vos fichiers de configuration
### Modifiez le fichier /etc/systemd/resolved.conf et décommentez la section DNS.
- Avant: 
- ![image](https://user-images.githubusercontent.com/113091304/194948287-a120b767-cab9-4dca-8aee-2617f3d08a08.png)
- Après:
- ![image](https://user-images.githubusercontent.com/113091304/194948361-eeb4a47f-dbb5-473b-8b17-405099ea2b89.png)
 
### 5. Redémarrer le serveur Bind9. Vous devriez maintenant être en mesure de « pinguer »les différentes machines du réseau.
- ![image](https://user-images.githubusercontent.com/113091304/194949005-47eef519-9f56-4af6-b71e-bb5ec2308a4d.png)














 

 
 

 
 


 
