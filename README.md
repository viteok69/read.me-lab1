# Lucrare de laborator nr.1
Realizat de studentul Badia Victor\
Grupa: I2301

21.02.2025

## 1. Scopul lucrării
Această lucrare de laborator vă familiarizează cu virtualizarea sistemelor de operare și configurarea unui server HTTP virtual.

Crearea unui server virtual într-o mașină virtuală și conectarea acesteia la prin SSH, setarea unui server HTTP Apache, setarea unei baze de date MySQL și setarea SGBD PhpMyAdmin și CMD Drupal.

## 2. Pașii pentru instalarea unei mașini virtuale

### 2.1 Instalarea QEMU

În primul rând am instalat MSYS2 MINGW64  pentru a instala QEMU cu ajutorul comenzii: 
```bash
pacman -S mingw-w64-x86_64-qemu
```
### 2.2 Setarea fișierelor pentru mașina virtuală

Descărcăm distributivul Debian pentru servere pentru arhitectura x64 (fără interfață grafică).

Vom crea un disc virtual pe care vom instala sitemul de operare.

![1](https://github.com/user-attachments/assets/0cbc054d-6d9a-407c-ab7a-3631ef7aa874)

Mutăm sistemul de operare și discul virtual într-un folder aparte

![2](https://github.com/user-attachments/assets/3b380406-c8f3-4ffe-bfab-a9079afa153c)

### 2.3 Instalare Debian

Instalăm SO Debian pe mașina virtuală. Pentru aceasta executăm comanda:

```bash
qemu-system-x86_64 -hda debian.qcow2 -cdrom dvd/debian.iso -boot d -m 2G
```

![3](https://github.com/user-attachments/assets/427ba1b2-0df3-45ab-812d-2933e932f2c0)

Apare fereastra următoare:\
Alegem Graphical install.

![4](https://github.com/user-attachments/assets/a255659b-7b09-4a44-a5a3-4be28ec57f17)

Alegem limba sistemului:

![5](https://github.com/user-attachments/assets/5d0f7e26-b88f-4f1c-b68a-b94696d56a79)

Alegem regiunea noastră:

![6](https://github.com/user-attachments/assets/3c63fbf5-6a54-4fab-afdf-b831f31f5d98)

Alegem limba tastaturii:

![7](https://github.com/user-attachments/assets/f84f3f3e-a6b3-4ca0-bf24-e6abc5731f87)

Introducem următoarele date:

- Hostname:

![9](https://github.com/user-attachments/assets/aef8ac2e-ced7-4f4c-9f19-e369720f6eff)

- Root password:

![10](https://github.com/user-attachments/assets/21739bc3-64d7-4217-8912-ac6611b45474)

- Nume utilizator: 

![11](https://github.com/user-attachments/assets/a108fc18-4622-4540-adc7-efbacd19c7fe)

Selectăm Guided - use entire disk, după alegem unicul disc.

![12](https://github.com/user-attachments/assets/d942ab4f-fa7d-4722-8fbf-4fc6a56eedb7)

Punem toate fișierele pe o partiție.

![13](https://github.com/user-attachments/assets/a87b130c-ca54-4eee-a787-445d8a8cd255)

Alegem butonul Yes.

![14](https://github.com/user-attachments/assets/281feef9-a462-4b01-88b4-2fe693e52910)

Așteptăm finalizarea procesului de instalare a sistemului:

![15](https://github.com/user-attachments/assets/769b8702-76d6-4959-af23-041d8f7a97ac)

Alegem serverul:

![16](https://github.com/user-attachments/assets/349ddb36-2155-45dc-8975-1aa1892e0cc2)

Alegemăm următoarele colecții suplimentare pentru instalare în sistemul nostru:

![17](https://github.com/user-attachments/assets/02a52819-5c31-4b7a-8ae6-5693efe07c8f)

Finisăm instalarea:

![18](https://github.com/user-attachments/assets/804f519b-828b-43fe-b0f2-d606ca19cd2c)

### 2.4 Rulare

În directorul unde se afla discul sistemului de operare (qcow) rulăm următoarea comanda:

```bash
qemu-system-x86_64 -hda debian.qcow2 -m 2G -smp 2 -device e1000,netdev=net0 
-netdev user,id=net0,hostfwd=tcp::1080-:80,hostfwd=tcp::2222-:22
```

Sistemul de operare se va porni și va apărea GRUB, care va alege Debian. 

Ajungem la prompt-ul cu login. Acum, putem să minimizăm mașina virtuală, deoarece nu vom lucra în ea

![19](https://github.com/user-attachments/assets/3b1f08a6-4a4f-471a-a13c-a68423914e13)

Deschidem un terminal și ne conectăm la mașina virtuală cu ajutorul la SSH:

```bash
ssh localhost -p 2222 -l user
```

La întrebarea propusă scriem yes:

```bash
The authenticity of host '[localhost]:2222 ([127.0.0.1]:2222)' can't be established.
ED25519 key fingerprint is SHA256:YooaxWsyHepQqEwFugXr9B4/2rQAkJn178sx3j62/6Q.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Am întrat în terminalul mașinii virtuale:

![20](https://github.com/user-attachments/assets/25c89b56-562b-436f-8f1e-4abf8f6e684f)

Ne conectăm ca administrator pentru a facea update la sistemul de operare:

```bash
#Schimbăm profilul utilizatorului pe superutilizator
su

#Facem update la sistem
apt update -y
```
![21](https://github.com/user-attachments/assets/229ba41a-3532-4859-b930-c7cd40c161e6)

Instalăm LAMP în mașina virtuală:

```bash
apt install -y apache2 php libapache2-mod-php php-mysql mariadb-server mariadbclient unzip
```

Descărcăm SGBD PhpMyAdmin:

```bash
wget https://files.phpmyadmin.net/phpMyAdmin/5.2.2/phpMyAdmin-5.2.2-all-languages.zip
```

Descărcăm CMS Drupal:

```bash
wget https://ftp.drupal.org/files/projects/drupal-10.0.5.zip
```

Dezarhivăm fișierele descărcate în directoarele:
- 1. SGBD PhpMyAdmin ==> /var/www/phpmyadmin
- 2. CMS Drupal ==> /var/www/drupal

```bash
mkdir /var/www
unzip phpMyAdmin-5.2.2-all-languages.zip
mv phpMyAdmin-5.2.2-all-languages /var/www/phpmyadmin
unzip drupal-10.0.5.zip
mv drupal-10.0.5 /var/www/drupal
```

## 3. Configurarea serverului pe mașina virtuală.

Creăm din linia de comandă baza de date drupal_db și utilizatorul bazei de date cu numele nostru:

![22](https://github.com/user-attachments/assets/3d66055e-a123-4ff2-9a05-dbe5b74d6e3e)

În directorul /etc/apache2/sites-available creăm fișierul 01-phpmyadmin.conf cu conținutul din imagine:

![23](https://github.com/user-attachments/assets/f7d15c21-42d9-4ffd-92a3-7ac940ae556b)

În directorul /etc/apache2/sites-available creăm fișierul 02-drupal.conf cu conținutul din imagine:

![24](https://github.com/user-attachments/assets/e20d1074-5ce6-45cc-9f99-cead804621a2)

Pentru a activa configurațiile, executăm comenzile:

```bash
/usr/sbin/a2ensite 01-phpmyadmin
/usr/sbin/a2ensite 02-drupal
```

Repornim Apache HTTP Server:

```bash
systemctl reload apache2
```

Adăugăm în fișierul /etc/hosts următoarele linii:

```bash
127.0.0.1 phpmyadmin.localhost
127.0.0.1 drupal.localhost
```

Verificăm disponibilitatea site-urilor pe http://drupal.localhost:1080 și
http://phpmyadmin.localhost:1080 din browser:

![25](https://github.com/user-attachments/assets/945bf963-7f13-4c00-a7c2-a9fa21251de1)


![Phpmyadmin](26.png)

Ne logăm în PhpMyAdmin cu user passowrd. După configurăm Drupal.

Alegem Standard Installation:

![standard](27.png)

Instalăm extensiile necesare:

```bash
apt install php8.2-dom php8.2-gd php8.2-simplexml php8.2-xml php-mbstring
```

## Concluzie

În cadrul acestui laborator, am configurat o mașină virtuală cu Debian, transformând-o într-un server prin instalarea stack-ului LAMP, a sistemului de gestionare a bazelor de date PhpMyAdmin și a platformei de management de conținut Drupal.

De asemenea, am configurat serverul Apache pentru a găzdui două site-uri, PhpMyAdmin și Drupal, pe care le-am instalat și setat corespunzător.

## Întrebări

1. Cum putem descărca un fișier în consolă folosind utilitarul wget?

Comanda wget primește ca argument link-ul către fișierul pe care dorim să-l descărcăm.
De exemplu, pentru a descărca imaginea de instalare Debian, utilizăm comanda:

```bash
wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-11.6.0-amd64-netinst.iso
```

2. De ce este necesar ca fiecare site să aibă propria bază de date și un utilizator dedicat?

Fiecare site necesită o bază de date separată pentru a-și gestiona informațiile independent.
Dacă mai multe site-uri ar folosi aceeași bază de date, ar putea apărea riscul ca datele unui site să fie accesate sau modificate de altul, afectând securitatea și integritatea informațiilor.

3. Cum putem modifica portul utilizat pentru accesarea sistemului de gestionare a bazelor de date?

Portul serverului local este determinat de configurația mașinii virtuale în momentul rulării acesteia.

4. Care sunt avantajele virtualizării?

- Permite configurarea și testarea unui sistem de operare fără a afecta direct sistemul gazdă.
- Oferă posibilitatea de a testa diverse aplicații și setări într-un mediu izolat.
- În caz de erori, sistemul poate fi restaurat rapid la o stare anterioară, reducând timpul necesar pentru remediere.

5. De ce este important să setăm corect ora și zona de timp pe un server?

Sincronizarea corectă a orei și zonei de timp este esențială pentru evitarea erorilor la gestionarea datelor între server și client.
O setare incorectă poate duce la interpretări greșite ale informațiilor și la afişarea incorectă a acestora.

6. Cât spațiu ocupă un sistem de operare instalat pe o mașină virtuală?

După instalarea serverului împreună cu un SGBD, PhpMyAdmin și Drupal, discul virtual ocupă aproximativ 3.8 GB.

7. Cum se recomandă să fie partiționat discul unui server și de ce?

O partiționare optimă îmbunătățește performanța și mentenanța sistemului. Recomandările sunt:

- /boot – 1 GB (kernelul și fișierele de pornire)
- /home – 20-50 GB (fișierele utilizatorilor)
- /var – 5-20 GB (loguri, baze de date, cache)
- /tmp – 2-5 GB (fișiere temporare)
- /data – dimensiune variabilă pentru baze de date și fișiere
- /srv – dimensiune variabilă, în funcție de tipul serverului

Această metodă de partiționare:

Crește performanța și facilitează întreținerea.
Reduce impactul defectării unui disc.
Permite verificarea și înlocuirea rapidă a unui disc fără a afecta întregul sistem.




