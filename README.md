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

timestamp: 11:35

Tarkistetaan, että sivusto toimii kirjoittamalla http://localhost/static/ osoitteeksi selaimeen. Varmistukseksi ajan vielä `curl` komennon:

![sivutoimii](https://user-images.githubusercontent.com/112503770/222390312-f4eb9632-33bc-4b19-8eae-8a2fdeaa4262.png)


## Lähteet:

    https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/
    https://terokarvinen.com/2022/deploy-django/
    
