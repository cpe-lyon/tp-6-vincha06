Vincent CHAVES - 3ICS

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




