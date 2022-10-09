Vincent CHAVES - 3ICS
## (Je précise que ma 2ème VM neuve avait un problème de résolution de nom, impossibilité de faire apt update entre autres, ce qui m'a retardé pour ce TP et le TP5)

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
 


 
