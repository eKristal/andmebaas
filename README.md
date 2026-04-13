## 1. Paigalduskeskkond ja ligipääs

**Kasutatud keskkond:** WSL2 + UBUNTU.

Ülesande alguses lõin kasutaja nimega kasutaja, alles hiljem märkasin et kasutajanimes peab olema minu nimi.  
Lahendusena ma hetkel lisasin käsuga `sudo adduser` enda nimelise kasutaja.

Keskkonnale pääseb ligi konsooli kaudu.


## 2. MariaDB server

**Kasutatud käsud:**

`sudo apt update` -- uuendus
`sudo apt install mariadb-server` -- MariaDB allalaadimine
`mariadb --version` -- MariaDB versiooni kuvamine
`sudo systemctl restart mariadb` -- Teenuse restart (et muudatused rakenduksid)
`sudo systemctl status mariadb` -- Teenuse oleku kontrollimine (aktiivne v ei)

**Kas teenud käivitub automaatselt?**  
Jah, sest `systemctl status mariadb` käsk kuvab et teenus on enabled ja aktiivne.

**MariaDB versioon:**  
`mariadb  Ver 15.1 Distrib 10.11.14-MariaDB, for debian-linux-gnu (x86_64) using  EditLine wrapper`


## 3. MariaDB turvaseadistus

Unix_Socket = Yes
Remove anonymus Users = Yes
Disallow remotely root login = Yes
Remove test database and access to it = Yes
Reload privilege tables now = Yes


## 4. Võrguturve

`bind-address = 127.0.01`   -- Oli olemas, ei muutnud midagi
`local-infile = 0`          -- Pidin juurde lisama, oli failist puudu
`skip-name-resolve`         -- Võtsin ainult `#` eest ehk tegin selle rea aktiivseks.


## 5. MariaDB kontroll

`ss -tlnp | grep 3306` -- Kontrollib, kas MariaDB kasutab seda porti. (`ss -tlnp | grep mariadb` ei andnud mul tulemust)


## 6. GitHub repo kloonimine

Kuna mul enda repos seda ei olnud, siis võtsin õpetaja repo kloonimiseks. 
Kasutasin järgnevaid käske:

`git clone https://github.com/metshein/cr.git`   -- kloonimiseks
`cd cr`  -- läksin kausta, mida kloonisin

## 7. SQL andmed andmebaasi

Kui olin läinud kloonitud repo kausta (cr), siis hakkasin sealt SQL faile otsima.
Kasutasin järgnevaid käske:

`find . -iname "*.sql!`   -- otsin repost .sql laiendiga faile
`head -n 20 db/cr.sql`    -- 20 tähendab mitu rida näitab, käsk oli vajalik et näha mis tüüpi SQL failiga on tegemist
`head -n 20 db/cars_mockaroo.sql`   -- kasutasin selleks, et näha kumb fail sisaldab varukoopiat
`grep -i "CREATE DATABASE\|USE \|CREATE TABLE" db/cr.sql | head -n 20`  -- kasutasin selleks, et aru saada, kas fail loob ise andmebaasi või ainult tabeli
`grep -i "INSERT INTO" db/cr.sql | head -n 20` -- kasutasin selleks, et näha, kas fail sisaldab ka andmeid

Mul oli vajalik andmebaas luua, kasutasin seda käsku: `sudo mariadb -u root -e "CREATE DATABASE cr;"`
Siis ma importisin sql faili selle käsuga: `sudo mariadb -u root cr < db/cr.sql`  -- kasutasin selleks et luua tabel cars koos andmetega.

Seejärel ma kontrollisin, kas õnnestus. 
Kontrollisin nende käskudega: 
`sudo mariadb -u root -e "SHOW DATABASES;"`  -- näitab kõiki mariadb andmebaase
`sudo mariadb -u root -e "USE cr; SHOW TABLES;"`  -- näitab cr andmebaasis olevaid taeleid.
`sudo mariadb -u root -e "SELECT COUNT(*) FROM cr.cars;"`  -- Loendab cars read, vajalik et kontrollida, kas andmed imporditi.


## 8. Kuvatõmmised

**Mariadb andmebaas:**  
![Sisselogitud MariaDB](images/mariadb-login.png)

**Port 3306:**  
![Port 3306](images/port-3306.png)

**Bind aadress**  
![bind_address kontroll](images/bind-address.png)
