# LinuxPalvelimet-h11-prod
    Kirjoittanut: Jesse Reunamo
    Kurssi:       Linux-palvelimet
    Linkki:       https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/

## a) klo: 10:40
Päivitetään tiedot paketeista apt-get update komennolla.

    sudo apt-get update
    
Palvelimen käyttämiseen tarvitaan apache2 weppipalvelin, mikäli sinulla ei ole sitä asennettuna voit asentaa ja käynnistää sen komennoilla:

    sudo apt-get -y install apache2
    sudo systemctl start apache2
    
Voit kokeilla toimiiko palvelin menemällä selaimeen ja kirjoittamalla http://localhost/ osoitteeksi. Vaihtoehtoisesti testaukseen voit käyttää komentoa curl 'http://localhost/'.

Kannattaa päivittää apachen esimerkkisivu. Se onnistuu komennolla:

    echo "Insert your page content here" | sudo tee /var/www/html/index.html

Luodaan sisältöä weppisivua varten:

    cd
    mkdir -p publicwsgi/sivuco/static/
    echo "This will be my page content" | tee publicwsgi/sivuco/static/index.html
  
Luodaan .conf tiedosto sivulle

    sudoedit /etc/apache2/sites-available/sivuco.conf
    
     <VirtualHost *:80>
	      Alias /static/ /home/jesser/publicwsgi/sivuco/static/
	      <Directory /home/jesser/publicwsgi/sivuco/static/>
		      Require all granted
	      </Directory>
     </VirtualHost>
    
![conffitietoo](https://user-images.githubusercontent.com/112503770/222385967-db06674a-a93e-431e-b4c2-ccebf9b5d1d8.png)

Otetaan sivu käyttöön a2aensite komennolla. Otetaan oletus-sivu pois käytöstä a2dissite komennolla. Lopuksi käynnistetään apache2 uudelleen, jotta muutokset tulevat näkyviin.

	sudo a2ensite sivuco.conf
	sudo a2dissite 000-default.conf 
	sudo systemctl restart apache2

klo: 11:35

Tarkistetaan, että sivusto toimii kirjoittamalla http://localhost/static/ osoitteeksi selaimeen. Varmistukseksi ajan vielä `curl` komennon:

![sivutoimii](https://user-images.githubusercontent.com/112503770/222390312-f4eb9632-33bc-4b19-8eae-8a2fdeaa4262.png)

Otetaan virtualenv djangoa varten käyttöön. Alempana on asennuskomento, navigointi aiemmin luotuun hakemistoon ja itse /env/ ympäristön luominen.

	sudo apt-get -y install virtualenv
	cd
	cd publicwsgi/
	virtualenv -p python3 --system-site-packages env

Käynnistetään virtuaaliympäristö ja tarkistetaan, että pip asennetaan virtuaaliympäristöön.

	source env/bin/activate
	which pip

Kuvassa näkyy komentopäätteen vasemmalle ilmestynyt (env), joka tarkoittaa että toimimme virtuaaliympäristössä. Kuvasta voimme myös nähdä, että pip asentuu juuri niihin kansioihin jotka loimme aiemmin.

![pip ja env](https://user-images.githubusercontent.com/112503770/222392308-9002e044-736d-4168-b2f5-febfcc22691c.png)

Kirjoitetaan asennettavaksi tulevat paketit tekstitiedostoon. Kuvassa yhdellä rivillä asennettava ohjelma django.

	micro requirements.txt

![rekuirements](https://user-images.githubusercontent.com/112503770/222393065-2709f4dd-5e26-4589-bd9e-09159c554c91.png)

Pakettien asentamiseen ajamme komennon.

	pip install -r requirements.txt
	
Kuvassa näkyy onnistunut asennus terminaalissa.

![asennuskuve](https://user-images.githubusercontent.com/112503770/222394218-d9c3262a-a498-4c37-b6e2-8e47824c00c9.png)

Voimme vielä kokeilla asennuksen onnistumista komennolla `django-admin --version`. Kirjoittamisen hetkellä komento vastaa `4.1.7`.

Projektin aloittamiseksi voit ajaa komennon:

	django-admin startproject sivuco
	
Huomaamme, että kirjoittajan tapauksessa kyseinen nimi on jo käytössä, joten joudumme vaihtamaan projektille toisen nimen ja tekemään muutamat korjaukset aiemmin tiedostoihin.

![rojektikaytos](https://user-images.githubusercontent.com/112503770/222395605-48733083-a336-4ba9-9ee5-9984ecfef523.png)

Ongelman korjaamiseksi varmasti voitaisiin poistaa aiempi projekti, mutta harjoittelun kannalta voi olla parempi käydä muokkaamssa tässä raportissa luotuja tiedostoja. Lista ajetuista komennoista ja # kertoo tarkemmin mitä tapahtuu.

	cd								#navigointia
	cd publicwsgi/
	mv sivuco/ jepico/						#vaihtaa kansion nimen.
	sudoedit /etc/apache2/sites-available/sivuco.conf		#päivitetään .conf tiedosto
	<VirtualHost *:80>
	      Alias /static/ /home/jesser/publicwsgi/jepico/static/
	      <Directory /home/jesser/publicwsgi/jepico/static/>
		      Require all granted
	      </Directory>
     	</VirtualHost>
	cd /etc/apache2/sites-available/				#navigointia
	sudo mv sivuco.conf jepico.conf					#tiedoston nimen vaihto
	cat jepico.conf						 	#testausta
	sudo a2dissite sivuco.conf					#apachen asetukset
	sudo a2ensite jepico.conf
	sudo systemctl restart apache2
	curl http://localhost/static/					#testi (onnistui)
	cd publicwsgi/							#palataan virhettä edeltävään tilanteeseen
	source env/bin/activate
		
Nyt voimme kokeilla ajaa virheen aiheuttaneen komennon uudelleen.

	django-admin startproject jepico
	
Edelleen tulee sama virhe, joten vaihdetaan projektin nimeä.

	django-admin startproject jepi
	
Onnistui, oletan että joku onglema tiedosto polun kanssa mahdollisesti. Toisaalta oma ajatus oli että kyseessä on ongelma projektin nimen kanssa, koska projekti nimeltä sivuco tosiaan oli jo olemassa.

klo: 12.40

Päivitetään .conf tiedostoa

	sudoedit /etc/apache2/sites-available/jepico.conf
	
Käytetään editoinnin pohjana https://terokarvinen.com/2022/deploy-django/ sivulla olevaa pohjaa. TUSER on käyttäjäsi, TDIR tulee luotu django projekti, TWSGI django projekti ja TENV env kansio. 

	Define TDIR /home/jesser/publicwsgi/jepi
	Define TWSGI /home/tero/publicwsgi/jepi/jepi/wsgi.py
	Define TUSER jesser
	Define TVENV /home/jesser/publicwsgi/env/lib/python3.9/site-packages
	# See https://terokarvinen.com/2022/deploy-django/
	<VirtualHost *:80>
       		Alias /static/ ${TDIR}/static/
        	<Directory ${TDIR}/static/>
                	Require all granted
        	</Directory>
       		WSGIDaemonProcess ${TUSER} user=${TUSER} group=${TUSER} threads=5 python-path="${TDIR}:${TVENV}"
        	WSGIScriptAlias / ${TWSGI}
       		<Directory ${TDIR}>
             		WSGIProcessGroup ${TUSER}
             		WSGIApplicationGroup %{GLOBAL}
            		WSGIScriptReloading On
             		<Files wsgi.py>
               			Require all granted
             		</Files>
        	</Directory>
	</VirtualHost>
	Undefine TDIR
	Undefine TWSGI
	Undefine TUSER
	Undefine TVENV

![confkuve](https://user-images.githubusercontent.com/112503770/222408803-2e31483c-0659-42ac-82d7-e8a30bc95b3a.png)

Ajetaan asennuskomento jotta saadaa projekti ymmärtämään wsgi komentoja.

	sudo apt-get -y install libapache2-mod-wsgi-py3
	
Testataan ilmenikö ongelmia
	
	/sbin/apache2ctl configtest
	
![syntax ok](https://user-images.githubusercontent.com/112503770/222409374-bd9ffded-bb36-4508-a30c-a9155114738e.png)
	
Syntaksi ok, joten voidaan jatkaa käynnistämällä apache uudelleen.

	sudo systemctl restart apache2
	
Nyt voimme kokeilla toimiiko asennus menemällä http:/localhost/ osoitteeseen.

![henloworl](https://user-images.githubusercontent.com/112503770/222409816-878799c4-dd96-494a-9fd5-b8bfd60197a2.png)

Hurraa! asennus on onnistunut.

klo: 13:00

Nyt teemme vielä viimeistelyt projektille käymme poistamassa virheidenetsintätilan (debug).

	cd
	cd publicwsgi/jepi/jepi
	micro settings.py

Käymme muokkaamassa muutamaa riviä:
	
	DEBUG = False
	ALLOWED_HOSTS = ["localhost"]
	
Nyt voimme ajaa päivitys komennot.

	touch jepi/wsgi.py
	sudo systemctl restart apache2

Nyt huomaamme, että http:/localhost/ antaa virheviestin.

![not foon](https://user-images.githubusercontent.com/112503770/222412391-0f511c6f-daa2-4914-8e13-9c32ce47873b.png)

Toisaalta voimme navigoida http:/localhost/admin/, ja se antaa kirjautumis vaihtoehdon.

![keerjautuminen](https://user-images.githubusercontent.com/112503770/222412736-2df27a83-d00f-4060-87c7-69ce3015c5f4.png)

Kirjautumisen kokeilemiseksi voimme ajaa komennot:

	cd publicwsgi
	cd jepi
	./manage.py makemigrations
	./manage.py migrate
	./manage.py createsuperuser

Voimme nyt koikeilla kirjautumista, joka toimii hyvin ks. kuva.

![kirjautustoimii](https://user-images.githubusercontent.com/112503770/222413686-81960eaf-fba4-4ef8-96a8-8a3ae2dcdbc8.png)

klo: 13:17

Laitetaan vielä tyylitiedostot toimimaan, jotta käyttäjäkokemus paranee.

	cd publicwsgi
	cd jepi/jepi/
	micro settings.py

Lisätään muutama rivi:

	import os
	STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
	
![importti](https://user-images.githubusercontent.com/112503770/222414947-4d86d726-0a98-492c-8a18-c5857b690b4c.png)![stati root](https://user-images.githubusercontent.com/112503770/222414958-bd858722-f5ec-422d-9680-22098f0eb968.png)

Nyt voimme ajaa komennon:
	
	./manage.py collectstatic

![collect static](https://user-images.githubusercontent.com/112503770/222415332-0eed5f37-5130-4b81-bb1d-4cb21523eeac.png)

Avaamalla http:/localhost/admin/ nyt voimme nähdä tyylit toiminnassa.

![tyylitoimii](https://user-images.githubusercontent.com/112503770/222415666-85804e93-0e48-45bd-b33d-9e3dbbad7988.png)

Lopetus: klo 13:26

## Lähteet:

    https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/
    https://terokarvinen.com/2022/deploy-django/
    
