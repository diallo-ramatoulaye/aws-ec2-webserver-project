# aws-ec2-webserver-project
Déploiement d'un serveur web Apache sur AWS EC2 - Projet pratique CLF-C02


# 🚀 Déploiement d'un serveur web sur AWS EC2

Projet pratique réalisé dans le cadre de ma préparation à la certification **AWS Cloud Practitioner (CLF-C02)**.

## 🎯 Objectif

Déployer un serveur web fonctionnel sur une instance Amazon EC2, en configurant manuellement l'infrastructure réseau et le serveur, du lancement de l'instance jusqu'à l'accès public via navigateur.

## 🏗️ Architecture

- **Amazon EC2** : instance t2.micro (Amazon Linux 2023, free tier)
- **Security Group** : ouverture des ports 22 (SSH) et 80 (HTTP)
- **Key Pair** : authentification SSH par paire de clés RSA (.pem)
- **Apache (httpd)** : serveur web installé et configuré sur l'instance

## 🛠️ Étapes détaillées et commandes utilisées

### 1. Création de la paire de clés (Key Pair)

Dans la console AWS → EC2 → Key Pairs → Create key pair (type RSA, format .pem).
Le fichier `ma-cle-projet1.pem` a été téléchargé localement.

### 2. Lancement de l'instance EC2

Dans la console AWS → EC2 → Launch instance :
- AMI : Amazon Linux 2023
- Type d'instance : t2.micro (free tier)
- Key pair : `ma-cle-projet1`
- Security Group créé avec :
  - SSH (port 22) ouvert depuis mon IP
  - HTTP (port 80) ouvert depuis Internet

### 3. Préparation de la clé SSH (Windows PowerShell)

Sous Windows, `chmod` n'existe pas. On utilise `icacls` pour restreindre les permissions du fichier `.pem` :

```powershell
icacls.exe ma-cle-projet1.pem /reset
icacls.exe ma-cle-projet1.pem /grant:r "$($env:USERNAME):(R)"
icacls.exe ma-cle-projet1.pem /inheritance:r
```

### 4. Connexion SSH à l'instance

```powershell
ssh -i ma-cle-projet1.pem ec2-user@3.141.14.144
```

(Le `-i` indique le chemin vers la clé privée, `ec2-user` est l'utilisateur par défaut pour Amazon Linux.)

### 5. Mise à jour du système et installation d'Apache

Une fois connecté en SSH sur l'instance :

```bash
sudo dnf update -y
sudo dnf install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
sudo systemctl status httpd
```

`enable` permet à Apache de redémarrer automatiquement si l'instance reboot.
`status` confirme que le service est bien **active (running)**.

### 6. Création de la page web

Création directe du fichier HTML via le terminal (évite les soucis d'édition interactive avec nano) :

```bash
sudo bash -c 'cat > /var/www/html/index.html << "EOF"
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Mon premier serveur AWS</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #232f3e;
            color: white;
            text-align: center;
            padding-top: 100px;
        }
        h1 { color: #ff9900; }
    </style>
</head>
<body>
    <h1>Mon premier serveur web sur AWS EC2</h1>
    <p>Deploye par Rama dans le cadre de sa preparation a la certification AWS Cloud Practitioner</p>
</body>
</html>
EOF'
```

Vérification du contenu :
```bash
cat /var/www/html/index.html
```

### 7. Test d'accès public

Dans un navigateur, accès via l'adresse IP publique de l'instance :http://3.141.14.144
La page s'affiche correctement.
