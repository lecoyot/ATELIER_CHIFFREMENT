# Atelier – Chiffrement/Déchiffrement (Python `cryptography`) dans GitHub Codespaces

## 1) Lancer le projet dans Codespaces
- Fork / clone ce repo
- Bouton **Code** → **Create codespace on main**

## 2) Installer la bibliothèque Python Cryptographie
```bash
pip install -r requirements.txt
```
## 3) Partie A – Chiffrer/Déchiffrer un texte
```
python app/fernet_demo.py
```
**Quel est le rôle de la clé Fernet ?**  
La clé Fernet est une clé symétrique secrète de 256 bits encodée en Base64, utilisée pour chiffrer et authentifier les données avec AES et HMAC issue de la bibliothèque python cryptography. Un token Fernet (c'est à dire le résultat chiffré) contient :  
```
| Version | Timestamp | IV | Ciphertext | HMAC |
```
* Version (1 octet) : Valeur actuelle : 0x80
* Timestamp (8 octets) : Permet l'expiration des tokens
* IV (16 octets) : Généré aléatoirement - Garantit que deux messages identiques produisent des ciphertexts différents
* Ciphertext (variable) : Résultat du chiffrement AES-128-CBC qui contient les données
* HMAC (32 octets) : Protège contre toute modification
  
## 4) Partie B – Chiffrer/Déchiffrer un fichier
Créer un fichier de test :  
```
echo "Message Top secret !" > secret.txt
```
Chiffrer :
```
python app/file_crypto.py encrypt secret.txt secret.enc
```
Déchiffrer :
```
python app/file_crypto.py decrypt secret.enc secret.dec.txt
cat secret.dec.txt
```
**Que se passe-t-il si on modifie un octet du fichier chiffré ?**  
 
**Pourquoi ne faut-il pas commiter la clé dans Git ?**   

## 5) Atelier 1 :
Dans cet atelier, la clé Fernet n'est plus générée dans le code mais stockée dans un Repository Secret Github. Ecrivez un nouveau programme **python app/fernet_atelier1.py** qui utilisera une clé Fernet caché dans un Secret GitHub pour encoder et décoder vos fichiers.

## 6) Atelier 2 :
Les bibliothèques qui proposent un système complet, sûr par défaut et simple d’usage comme Fernet de la bibliothèse Cryptographie sont relativement rares. Toutefois, la bibliothèque PyNaCl via l'outil SecretBox est une très bonne alternative. **travail demandé :** Construire une solution de chiffrement/déchiffrement basé sur l'outils SecretBox de la bibliothèque PyNaCl.











### Que se passe-t-il si on modifie un octet du fichier chiffré ?

Comme vous l'avez vu dans la démonstration, la modification d'un seul octet dans un fichier chiffré Fernet (ou la plupart des schémas de chiffrement modernes avec authentification) entraîne une erreur lors du déchiffrement. Le message d'erreur sera probablement `InvalidToken` ou similaire. Cela se produit parce que Fernet utilise un mécanisme d'authentification (un code d'authentification de message, ou MAC) qui détecte toute altération du texte chiffré. Si même un seul octet est modifié, le MAC ne correspond plus, et le système de déchiffrement refuse de déchiffrer le message, protégeant ainsi l'intégrité des données.


### Pourquoi ne faut-il pas commettre la clé dans Git ?

Il est **crucial de ne jamais commettre la clé de chiffrement (comme la clé Fernet) dans un dépôt Git**, et ce pour plusieurs raisons de sécurité :

1.  **Exposition des secrets :** Une fois la clé commise dans Git, elle devient partie de l'historique du dépôt. Même si vous supprimez le fichier de la branche actuelle, il restera accessible dans l'historique des commits. Quiconque a accès au dépôt (y compris les forks ou les clones futurs) pourra récupérer cette clé.
2.  **Compromission des données chiffrées :** Si la clé est publique, toutes les données chiffrées avec cette clé sont instantanément et irréversiblement compromises. L'objectif du chiffrement est de protéger les informations sensibles ; si la clé est connue, cette protection est annulée.
3.  **Rotation difficile :** Si une clé exposée est utilisée dans plusieurs endroits, sa révocation et son remplacement deviennent un processus complexe et risqué, nécessitant de re-chiffrer toutes les données et de mettre à jour toutes les applications qui l'utilisent.
4.  **Bonnes pratiques de sécurité :** Les clés de chiffrement sont des secrets. Les secrets doivent être traités avec le plus grand soin, stockés dans des environnements sécurisés (comme des gestionnaires de secrets ou des variables d'environnement sur des serveurs sécurisés) et jamais inclus dans le code source ou les systèmes de contrôle de version publics.

En résumé, commettre une clé dans Git est une erreur de sécurité majeure qui peut avoir des conséquences désastreuses pour la confidentialité de vos données.
