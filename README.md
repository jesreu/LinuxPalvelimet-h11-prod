# LinuxPalvelimet-h11-prod
    Kirjoittanut: Jesse Reunamo
    Kurssi:       Linux-palvelimet
    Linkki:       https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/

## a) 10:40
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

Korjaukset:

	
## Lähteet:

    https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/
    https://terokarvinen.com/2022/deploy-django/
    
