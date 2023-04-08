# docker-compose-eventer
Docker related files for EvEnter project

RabbitMQ

U folderu rabbitmq, nalazi se docker-compose.yml fajl u kom s enalazi potrebna konfiguracija za dovlacenje docker image-a, 
kreiranje mreze, pokretanje kontejnera sa odgovarajucom konfiguracijom.

objasnjenje docker-compose fajla (preuzeto i prevedno sa https://x-team.com/blog/set-up-rabbitmq-with-docker-compose/):
image: naziv image-a koji dovlacimo, u ovom slucaju, koristimo Alpine implementaciju RabbitMQ-a sa pluginom za managenemt; 
	 Alpine distro je ona koja se koristi ako zelimo da stedimo memoriju na disku.
container_name: naziv kontejnera koji ce biti startovan od dovucenog image-a
ports: lista portova na host masini koju mapiramo u portove u kontejneru; navedeni portovi sluze za interakciju sa brokerom i web UI
volumes: mapiranje foldera log and data iz kontejnera u nase lokalne foldere; 
	   ovo nam omogucuje da vidimo fajlove direktno u lokalnom eksploreru umesto da se kacimo na kontejner i tako pristupamo folderima direktno
networks: ime doker mreze koju ce kontejner da koristi; ovo nam pomaze u razdvajanju mreznih konfiguracija za kontejnere na razlicitim mrezama;
	    mi cemo koristiti samo jednu mrezu, ali svakako niej zgoreg navesti mrezu, a i kao primer za neciju potrebu za vise mreza


Pokretanje kontejnera: -iz komandne linije/powershell/bash pozicionirati se u folder u kom se nalazi docker-compose.yml fajl
			     -pokrenuti sledecu komandu docker-compose up

Moze postojati problem prilikom podizanja vezano za prava pristupa i upisivanja u log vajlove koji su navedeni u volumes.
Takodje, ako folderi nisu kreirani, tada ih treba kreirati, a ako postoje i dalje problemi, dodeliti useru neophodne privilegije nad folderima.
Nakon pokretanja kontejnera, otici na adresu http://localhost:15672, na kojoj se nalazi web UI za nas rabbitmq broker
Ulogovati se sa guest/guest, jer je to defaultni user i pass.



Oracle db

U folderu db, nalazi se docker compose fajl za dovlacenje i kreiranje baze podataka koju koristimo za nas servis.
Takodje, slede instrukcije nakon dovlacenja i pokretanja baze podataka.
U docker-compose fajlu, potrebno je postaviri novu sifru umesto <your-password>


Ono sto smo definisali u docker compose fajlu, moze 'peske' da se odradi sledecim komandama 
(ukoliko neko jos nije upoznat sa docker-compose i ovo mu je intuitivnije, medjutim, 
sam docker-compose je pravljen da olaksa dovlacenje pokretanje i definisanje potrebnih stvari, umesto da koristimo posebne komande)

docker pull gvenzl/oracle-xe
docker run -d -p 1521:1521 --name oracle-db -e ORACLE_PASSWORD=<your password> gvenzl/oracle-xe
docker exec <container name|id> resetPassword <your password>

--Iz oracle SQLDevelopera konektujemo se na bazu sa sledecim parametrima:

Port: 1521
Service name: XEPDB1
Database App User: system
Database App Password: <your password>

Mozemo se i direktno konektovati na bazu kroz kontejner sledecim komandama:

$ docker exec -it <id-kontejnera> bash
$ sqlplus "/ as sysdba"

tj. u jednoj komandi $ docker exec -it oracle-db sqlplus / as sysdba

Nakon konekcije uraditi test: select sysdate from dual;

Kreiranje novog usera:
--ALTER SESSION SET CONTAINER=XEPDB1;
CREATE USER TEST IDENTIFIED BY test QUOTA UNLIMITED ON USERS;
GRANT CONNECT, RESOURCE TO TEST;








Korisno:
Postoji online tool koji pretvara docker run komande u docker-compose fajlove, 
pa moze da posluzi nekome ko nije siguran u ispravnost svojih napisanih komandi. 
Tool je na sledecem linku:
https://www.composerize.com/