import mysql.connector
import os
import time

class Navigaattori:
	def navigointi(self, syote):
		if "peruuta" in syote or "lopeta" in syote: return True
		return False

	def kyllaEi(self, syote):
		if "kyllä" in syote or "ei" in syote: return True
		return False

class Kanta:
	def __init__(self):
		self._yhteys = mysql.connector.connect(
			host = "localhost",
			user = "kayttaja",
			passwd = "salasana1",
			database = "tilaukset"
		)
		self._kursori = self._yhteys.cursor()

	def __enter__(self):
		return self

	def __exit__(self, exc_type, exc_val, exc_tb):
		self.close()

	@property

	def kursori(self):
		return self._kursori

	def toteuta(self):
		self.kursori.commit()

	def hae(self, syote):
		self.kursori.execute(syote)
		return self.kursori.fetchall()

	def haeYksi(self, syote):
		self.kursori.execute(syote)
		return self.kursori.fetchone()

	def lisaa(self, syote):
		self.kursori.execute()

	def sulje(self):
		self.yhteys.close()

class Asiakkuus:
	def __init__(self):
		self.kanta = Kanta()
		self.asiakas = None

	def lisaaAsiakas(self):
		tarkastaja = Tarkastaja(self.asiakasLista("asiakasid"))
		navi = Navigaattori()
		while(True):
			asiakas = input("\nKenelle tilaus (valitse id-numero)?: ")
			if navi.navigointi(asiakas): break
			if tarkastaja.onkoNumero(asiakas) or tarkastaja.onkoOlemassa(asiakas): continue
			else: break
		if navi.navigointi(asiakas): return True
		self.asiakas = asiakas

	def asiakasLista(self, arvot):
		asiakasLista = self.kanta.hae("SELECT " + arvot + " FROM asiakkaat WHERE aktiivinen = 1;")
		return asiakasLista

	def naytaAsiakkaat(self):
	       	for y in self.asiakasLista("asiakasid, etunimi, sukunimi, katuosoite, kunta"):
	               	print("Asiakas: (id: " + str(y[0]) + ") " + str(y[1]) + " " + str(y[2]) + " (" + str(y[3]) + ", " + str(y[4]) + ")")

	def naytaAsiakas(self):
		naytaAsiakas = self.kanta.haeYksi("SELECT etunimi, sukunimi FROM asiakkaat WHERE asiakasid = " + self.asiakas + ";")
		return str(naytaAsiakas[0] + " " + naytaAsiakas[1])

	def __str__(self):
		return self.asiakas

class Tuotteet:
	def __init__(self):
		self.kanta = Kanta()

	def tuotelista(self, arvot):
		tuotelistaus = self.kanta.hae("SELECT " + arvot + " FROM tuotteet WHERE maara > 0;")
		return tuotelistaus

	def listaaTuotteet(self):
		for x in self.tuotelista("tuoteid, nimi, kuvaus, hinta, yksikko, valmistaja"):
			print(str(x[0]) + ": " + str(x[1]) + " (" + str(x[5]) + ") || " + str(x[2]) + " (hinta: " + str(x[3]) + "e / " + str(x[4]) + ")")

	def listaaTuote(self, tuoteId, arvot):
		return self.kanta.haeYksi("SELECT " + arvot + " FROM tuotteet WHERE tuoteid = " + tuoteId + ";")

	def tuoteMaara(self, tuote):
		return self.kanta.haeYksi("SELECT maara FROM tuotteet WHERE tuoteid = " + tuote + ";")

class Ostoskori:
	def __init__(self, tuotteet):
		self.ostoskori = []
		self.navi = Navigaattori()
		self.tarkastaja = Tarkastaja(tuotteet.tuotelista("tuoteid"))
		self.tuotteet = tuotteet

	def lisaaTuote(self, tuote, maara, korvaus):
		lisays = tuote + ":" + maara + ":" + korvaus
		self.ostoskori.append(lisays)

	def onkoOstoskorissa(self, syote):
		for listalla in self.ostoskori:
			if listalla[0] == syote: return True

	def tilauksenTeko(self):
		while(True):
			tilattavaTuote = input("Valitse tuote (id-numero): ")
			if self.navi.navigointi(tilattavaTuote): break
			if self.tarkastaja.onkoNumero(tilattavaTuote): continue
			if self.tarkastaja.onkoOlemassa(tilattavaTuote): continue
			if self.onkoOstoskorissa(tilattavaTuote):
				print("Tuote on jo ostoskorissa!")
				continue
			else: break
		if self.navi.navigointi(tilattavaTuote): return True
		while(True):
			tilattavaMaara = input("Tilattava määrä: ")
			if self.navi.navigointi(tilattavaMaara): break
			if self.tarkastaja.onkoNumero(tilattavaMaara): continue
			if int(tilattavaMaara) <= 0: print("\nVähintään yksi tuote on tilattava")
			elif int(self.tuotteet.tuoteMaara(tilattavaTuote)[0]) >= int(tilattavaMaara): break
			else: print("\nValitettavasti varastossa ei ole niin paljon tuotteita")
		if self.navi.navigointi(tilattavaMaara): return True
		while(True):
			tuotteenKorvaus = input("Saako tuotteen korvata (kyllä/ei)? ")
			if self.navi.navigointi(tuotteenKorvaus): break
			if not self.navi.kyllaEi(tuotteenKorvaus):
				print("\nVastattava 'kyllä' tai 'ei'")
				continue
			else: break
		if self.navi.navigointi(str(tilattavaTuote + tilattavaMaara + tuotteenKorvaus)): return True
		self.lisaaTuote(tilattavaTuote, tilattavaMaara, tuotteenKorvaus)
		lisattyTuote = self.tuotteet.listaaTuote(tilattavaTuote, "nimi, yksikko")
		print("\nLisätty tuote: " + str(lisattyTuote[0]) + " (" + tilattavaMaara + " " + str(lisattyTuote[1]) + "), korvattavissa: " + tuotteenKorvaus)
		time.sleep(1)

	def naytaOstoskori(self):
		print("\nOstoskorin sisältö:")
		for ostoskoriTuote in self.ostoskori:
			jakaja = ostoskoriTuote.split(":")
			tuotteenTiedot = self.tuotteet.listaaTuote(jakaja[0], "nimi, yksikko, valmistaja")
			print(str(tuotteenTiedot[0]) + " (" + str(tuotteenTiedot[2]) + ") " + str(jakaja[1]) + " / " + str(tuotteenTiedot[1]) + ", korvattavissa: " + str(jakaja[2]))

	def sisalto(self):
		return self.ostoskori

class Tavat:
	def __init__(self, taulu):
		self.kanta = Kanta()
		self.tavanId = ""
		self.taulu = taulu

	def listaaTavat(self, arvot):
		return self.kanta.hae("SELECT " + arvot + " FROM " + self.taulu +";")

	def naytaTavat(self):
		if self.taulu == "maksutavat":
			print("\nTilauksen maksutavat")
			for rivi in self.listaaTavat("maksutapaid, maksutapa"):
				print(str(rivi[0]) + ": " + str(rivi[1]))
		if self.taulu == "toimitustavat":
			print("\nTilauksen toimitustavat")
			for rivi in self.listaaTavat("toimitustapaid, toimitustapa, palveluntuottaja"):
				print(str(rivi[0]) + ": " + str(rivi[1]) + " (" + str(rivi[2]) + ")")

	def valitseTapa(self):
		if self.taulu == "maksutavat": tarkistaja = Tarkastaja(self.listaaTavat("maksutapaid"))
		if self.taulu == "toimitustavat": tarkistaja = Tarkastaja(self.listaaTavat("toimitustapaid"))
		navi = Navigaattori()
		while(True):
			tapaValinta = input("\nValitse numero): ")
			if navi.navigointi(tapaValinta): break
			if tarkistaja.onkoNumero(tapaValinta) or tarkistaja.onkoOlemassa(tapaValinta): continue
			else: break
		if navi.navigointi(tapaValinta): return True
		self.tavanId = tapaValinta

	def __str__(self):
		return self.tavanId

class Tilaus:
	def __init__(self, asiakas, paivamaara, maksutapa, toimitustapa, kommentti, tuotteet):
		self.kanta = Kanta()
		self.asiakas = asiakas
		self.paivamaara = paivamaara
		self.maksutapa = maksutapa
		self.toimitustapa = toimitustapa
		self.kommentti = kommentti
		self.tuotteet = tuotteet
		self.paivamaara = "2022-03-13 13:17:59"

	def haeMaksunId(self):
		return str(self.kanta.haeYksi("SELECT a.maksuid FROM maksut a, tilaukset b WHERE a.maksuid = b.maksuid AND b.asiakasid = " + self.asiakas + " AND ajankohta = '" + self.paivamaara + "' ORDER BY maksuid DESC LIMIT 1;")[0])

	def haeTilauksenId(self):
		return str(self.kanta.haeYksi("SELECT tilausid FROM tilaukset WHERE asiakasid = " + self.asiakas + " AND tilauspvm = '" + self.paivamaara + "' ORDER BY tilausid DESC LIMIT 1;")[0])

	def luoTilaus(self):
		print("INSERT INTO maksut (maksutapa, maksettu, ajankohta) VALUES (" + self.maksutapa + ", 0, '" + self.paivamaara + "');")
		print("INSERT INTO tilaukset (asiakasid, kasitelty, tilauspvm, toimitustapa, kommentti, maksuid) VALUES (" + self.asiakas + ", 0, '" + self.paivamaara + "', " + self.toimitustapa + ", '" + kommentti + "', " + self.haeMaksunId() + ");")
		for tuote in self.tuotteet:
			jako = tuote.split(":")
			print("INSERT INTO tilaus (tilausid, tuoteid, maara, korvattavissa) VALUES (" + self.haeTilauksenId() + ", " + jako[0] + ", " + jako[1] + ", " + jako[2] + ");")

class Tarkastaja:
	def __init__(self, lista):
		self.lista = lista

	def onkoNumero(self, tarkistettava):
		if not tarkistettava.isnumeric():
			print("\nSyötä (positiivinen) numeroarvo!")
			time.sleep(2)
			return True

	def onkoOlemassa(self, tarkistettava):
		tarkistus = 0
		for x in self.lista:
			if str(x[0]) == str(tarkistettava): 
				tarkistus = 1
		if tarkistus == 0:
			print("\nEi sellaista arvoa listalla!")
			time.sleep(2)
			return True
		return False

asiakkuusValittu = 0
asiakkuus = Asiakkuus()
kanta = Kanta()

while(True):
	os.system("clear")
	pvm = time.strftime("%Y-%m-%d %H:%M:%S")
	if asiakkuusValittu == 0:
		asiakkuus.naytaAsiakkaat()
		if asiakkuus.lisaaAsiakas(): break
		asiakkuusValittu = 1
	os.system("clear")
	print("Valittu asiakas: "+ asiakkuus.naytaAsiakas() +"\n")
	print("Toimenpiteet:")
	print("1: Luo tilaus\n2: Muokkaa tilausta\n3: Poista tilaus\n4: Valitse toinen asiakas\n5: Lopeta")
	valinta = input("Mitä tehdään? ")

	if valinta == "1":
		toisto = 0
		tuotteet = Tuotteet()
		uusiOstoskori = Ostoskori(tuotteet)
		tilausMaksutapa = Tavat("maksutavat")
		tilausToimitustapa = Tavat("toimitustavat")
		tilausNavi = Navigaattori()

		while(True):
			os.system("clear")
			print("Uusi tilaus\n\nTuotteet:")
			tuotteet.listaaTuotteet()
			if toisto > 0: uusiOstoskori.naytaOstoskori()
			print("\nKirjoita 'lopeta' lopettaaksesi tuotteiden lisääminen\n")
			if uusiOstoskori.tilauksenTeko(): break
			toisto += 1

		if toisto > 0:
			tilausMaksutapa.naytaTavat()
			if tilausMaksutapa.valitseTapa(): continue
			tilausToimitustapa.naytaTavat()
			if tilausToimitustapa.valitseTapa(): continue
			kommentti = input("\nTilauksen kommentti: ")
			if tilausNavi.navigointi(kommentti): continue
			uusiTilaus = Tilaus(str(asiakkuus), pvm, str(tilausMaksutapa), str(tilausToimitustapa), kommentti, uusiOstoskori.sisalto())
			uusiTilaus.luoTilaus()
		else: continue
		uusiteko = input("Uusi tilaus (kyllä/ei)? ")
		if uusiteko == 'ei': break

	elif valinta == "4":
		asiakkuusValittu = 0
		continue
	elif valinta == "5":
		break

kanta.sulje()
