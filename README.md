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

![Folder aparte](2.png)

### 2.3 Instalare Debian

Instalăm SO Debian pe mașina virtuală. Pentru aceasta executăm comanda:

```bash
qemu-system-x86_64 -hda debian.qcow2 -cdrom dvd/debian.iso -boot d -m 2G
```

![Instalare Debian](3.png)

Apare fereastra următoare:\
Alegem Graphical install.

![Debian2](4.png)

Alegem limba sistemului:

![Limba](5.png)

Alegem regiunea noastră:

![Regiune](6.png)

Alegem limba tastaturii:

![Limba](7.png)

Introducem următoarele date:

- Hostname:

![Hostname](9.png)

- Root password:

![Password](10.png)

- Nume utilizator: 

![Username](11.png)

Selectăm Guided - use entire disk, după alegem unicul disc.

![Partition](12.png)

Punem toate fișierele pe o partiție.

![1Partition](13.png)

Alegem butonul Yes.

![YES](14.png)

Așteptăm finalizarea procesului de instalare a sistemului:

![Instalare](15.png)

Alegem serverul:

![Server](16.png)

Alegemăm următoarele colecții suplimentare pentru instalare în sistemul nostru:

![Colectii](17.png)

Finisăm instalarea:

![Finish](18.png)

### 2.4 Rulare

În directorul unde se afla discul sistemului de operare (qcow) rulăm următoarea comanda:

```bash
qemu-system-x86_64 -hda debian.qcow2 -m 2G -smp 2 -device e1000,netdev=net0 
-netdev user,id=net0,hostfwd=tcp::1080-:80,hostfwd=tcp::2222-:22
```

Sistemul de operare se va porni și va apărea GRUB, care va alege Debian. 

Ajungem la prompt-ul cu login. Acum, putem să minimizăm mașina virtuală, deoarece nu vom lucra în ea

![qemu](19.png)

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

![virtual](20.png)

Ne conectăm ca administrator pentru a facea update la sistemul de operare:

```bash
#Schimbăm profilul utilizatorului pe superutilizator
su

#Facem update la sistem
apt update -y
```

![update](21.png)

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

![BD](22.png)

În directorul /etc/apache2/sites-available creăm fișierul 01-phpmyadmin.conf cu conținutul din imagine:

![file](23.png)

În directorul /etc/apache2/sites-available creăm fișierul 02-drupal.conf cu conținutul din imagine:

![drupal](24.png)

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

![Drupal](25.png)

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




