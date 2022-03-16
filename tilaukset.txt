-- MariaDB dump 10.19  Distrib 10.5.12-MariaDB, for debian-linux-gnu (x86_64)
--
-- Host: localhost    Database: tilaukset
-- ------------------------------------------------------
-- Server version	10.5.12-MariaDB-0+deb11u1

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8mb4 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `asiakkaat`
--

DROP TABLE IF EXISTS `asiakkaat`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `asiakkaat` (
  `asiakasid` int(11) NOT NULL AUTO_INCREMENT,
  `etunimi` varchar(30) NOT NULL,
  `sukunimi` varchar(30) NOT NULL,
  `katuosoite` varchar(100) NOT NULL,
  `postinumero` char(5) NOT NULL,
  `kunta` varchar(30) NOT NULL,
  `puhelinnumero` varchar(10) NOT NULL,
  `sahkoposti` varchar(100) NOT NULL,
  `aktiivinen` tinyint(1) NOT NULL,
  PRIMARY KEY (`asiakasid`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8mb4;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `asiakkaat`
--

LOCK TABLES `asiakkaat` WRITE;
/*!40000 ALTER TABLE `asiakkaat` DISABLE KEYS */;
INSERT INTO `asiakkaat` VALUES (1,'Mikko','Mallikas','Kuminatie 23','01300','Vantaa','0401233123','miko.mallikas@mikonkotisivu.info',1),(2,'Seppo','Hovi','Kirkkokatu 1','00100','Helsinki','010100100','seppohovi@pianistit.fi',0),(3,'Vili','Vilperi','Mannerheimintie 123123213','00100','Helsinki','0501233123','vipeltaja@lastenohjelmat.fi',1),(4,'Seppo','Taalasmaa','Pihlajakatu','00100','Helsinki','0692331235','seppo@talkkarit.fi',1),(5,'Pelle','Hermanni','Sirkuskatu 2','99999','Sirkus','09434123','pelle@hermanni.info',1),(6,'George W.','Bush','Ameriikantie 1','88888','Ameriikka','234234','george@usapressat.fi',1);
/*!40000 ALTER TABLE `asiakkaat` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `maksut`
--

DROP TABLE IF EXISTS `maksut`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `maksut` (
  `maksuid` int(11) NOT NULL AUTO_INCREMENT,
  `maksutapaid` int(11) NOT NULL,
  `maksettu` tinyint(1) NOT NULL,
  `ajankohta` datetime NOT NULL,
  PRIMARY KEY (`maksuid`),
  KEY `maksutapaid` (`maksutapaid`),
  CONSTRAINT `maksut_ibfk_1` FOREIGN KEY (`maksutapaid`) REFERENCES `maksutavat` (`maksutapaid`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `maksut`
--

LOCK TABLES `maksut` WRITE;
/*!40000 ALTER TABLE `maksut` DISABLE KEYS */;
INSERT INTO `maksut` VALUES (1,1,0,'2022-03-10 14:12:44'),(2,2,1,'2022-03-11 23:45:00'),(3,3,0,'2022-03-09 01:01:50'),(4,6,0,'2022-03-13 13:07:27'),(5,6,0,'2022-03-13 13:17:59');
/*!40000 ALTER TABLE `maksut` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `maksutavat`
--

DROP TABLE IF EXISTS `maksutavat`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `maksutavat` (
  `maksutapaid` int(11) NOT NULL AUTO_INCREMENT,
  `maksutapa` varchar(20) NOT NULL,
  PRIMARY KEY (`maksutapaid`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8mb4;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `maksutavat`
--

LOCK TABLES `maksutavat` WRITE;
/*!40000 ALTER TABLE `maksutavat` DISABLE KEYS */;
INSERT INTO `maksutavat` VALUES (1,'Käteinen'),(2,'Kortti'),(3,'Verkkomaksu'),(4,'Mobilepay'),(5,'Tilisiirto'),(6,'Sekki');
/*!40000 ALTER TABLE `maksutavat` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `tilaukset`
--

DROP TABLE IF EXISTS `tilaukset`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `tilaukset` (
  `tilausid` int(11) NOT NULL AUTO_INCREMENT,
  `asiakasid` int(11) NOT NULL,
  `kasitelty` tinyint(1) NOT NULL,
  `tilauspvm` datetime NOT NULL,
  `toimituspvm` datetime NOT NULL,
  `toimitustapaid` int(11) NOT NULL,
  `kommentti` tinytext DEFAULT NULL,
  `maksuid` int(11) NOT NULL,
  PRIMARY KEY (`tilausid`),
  KEY `asiakasid` (`asiakasid`),
  KEY `toimitustapaid` (`toimitustapaid`),
  KEY `maksuid` (`maksuid`),
  CONSTRAINT `tilaukset_ibfk_1` FOREIGN KEY (`asiakasid`) REFERENCES `asiakkaat` (`asiakasid`),
  CONSTRAINT `tilaukset_ibfk_2` FOREIGN KEY (`toimitustapaid`) REFERENCES `toimitustavat` (`toimitustapaid`),
  CONSTRAINT `tilaukset_ibfk_3` FOREIGN KEY (`maksuid`) REFERENCES `maksut` (`maksuid`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `tilaukset`
--

LOCK TABLES `tilaukset` WRITE;
/*!40000 ALTER TABLE `tilaukset` DISABLE KEYS */;
INSERT INTO `tilaukset` VALUES (2,4,0,'2022-03-10 14:14:14','2022-03-20 01:01:01',2,'',1),(3,1,1,'2022-03-11 03:43:10','2022-03-20 01:01:01',1,'On se',2),(4,2,0,'2022-03-10 15:01:10','2022-03-20 01:01:01',4,'',3),(5,5,9,'2022-03-13 13:17:59','2022-03-20 01:01:01',3,'En ole kotona',5);
/*!40000 ALTER TABLE `tilaukset` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `tilaus`
--

DROP TABLE IF EXISTS `tilaus`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `tilaus` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `tilausid` int(11) NOT NULL,
  `tuoteid` int(11) NOT NULL,
  `maara` int(11) NOT NULL,
  `korvattavissa` tinyint(1) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `tilausid` (`tilausid`),
  KEY `tuoteid` (`tuoteid`),
  CONSTRAINT `tilaus_ibfk_1` FOREIGN KEY (`tilausid`) REFERENCES `tilaukset` (`tilausid`),
  CONSTRAINT `tilaus_ibfk_2` FOREIGN KEY (`tuoteid`) REFERENCES `tuotteet` (`tuoteid`)
) ENGINE=InnoDB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8mb4;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `tilaus`
--

LOCK TABLES `tilaus` WRITE;
/*!40000 ALTER TABLE `tilaus` DISABLE KEYS */;
INSERT INTO `tilaus` VALUES (1,2,6,23545,0),(2,2,2,2,1),(3,2,1,20,0),(4,3,5,65,0),(5,3,1,1,0),(6,3,4,2,0),(7,3,3,2,0),(8,4,3,2,0),(9,4,1,2,0),(10,4,6,20,1),(11,5,2,4,0),(12,5,9,1,1);
/*!40000 ALTER TABLE `tilaus` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `toimitustavat`
--

DROP TABLE IF EXISTS `toimitustavat`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `toimitustavat` (
  `toimitustapaid` int(11) NOT NULL AUTO_INCREMENT,
  `toimitustapa` varchar(50) NOT NULL,
  `palveluntuottaja` varchar(50) NOT NULL,
  PRIMARY KEY (`toimitustapaid`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8mb4;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `toimitustavat`
--

LOCK TABLES `toimitustavat` WRITE;
/*!40000 ALTER TABLE `toimitustavat` DISABLE KEYS */;
INSERT INTO `toimitustavat` VALUES (1,'Kotiinkuljetus','Posti'),(2,'Kotiinkuljetus','DHL'),(3,'Kotiinkuljetus','UPS'),(4,'Nouto','Oma'),(5,'Pakettiautomaatti','Posti'),(6,'Pakettiautomaatti','DHL');
/*!40000 ALTER TABLE `toimitustavat` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `tuotetyypit`
--

DROP TABLE IF EXISTS `tuotetyypit`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `tuotetyypit` (
  `tuotetyyppiid` int(11) NOT NULL AUTO_INCREMENT,
  `tuotetyyppi` varchar(30) NOT NULL,
  `sailytys` varchar(30) DEFAULT NULL,
  PRIMARY KEY (`tuotetyyppiid`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8mb4;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `tuotetyypit`
--

LOCK TABLES `tuotetyypit` WRITE;
/*!40000 ALTER TABLE `tuotetyypit` DISABLE KEYS */;
INSERT INTO `tuotetyypit` VALUES (1,'Vaatteet','Hylly'),(2,'Pakasteet','Pakaste'),(3,'Leikkeleet','Kylmiö'),(4,'Elektroniikka','Varasto'),(5,'Kirjat','Hylly'),(6,'Hedelmät','Hevi');
/*!40000 ALTER TABLE `tuotetyypit` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `tuotteet`
--

DROP TABLE IF EXISTS `tuotteet`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `tuotteet` (
  `tuoteid` int(11) NOT NULL AUTO_INCREMENT,
  `tuotetyyppiid` int(11) NOT NULL,
  `nimi` tinytext NOT NULL,
  `hinta` decimal(8,2) NOT NULL,
  `yksikko` varchar(10) NOT NULL,
  `maara` int(11) NOT NULL,
  `kuvaus` tinytext DEFAULT NULL,
  `valmistaja` varchar(50) NOT NULL,
  `valmistusmaa` varchar(30) NOT NULL,
  `ean` varchar(13) DEFAULT NULL,
  PRIMARY KEY (`tuoteid`),
  KEY `tuotetyyppiid` (`tuotetyyppiid`),
  CONSTRAINT `tuotteet_ibfk_1` FOREIGN KEY (`tuotetyyppiid`) REFERENCES `tuotetyypit` (`tuotetyyppiid`)
) ENGINE=InnoDB AUTO_INCREMENT=10 DEFAULT CHARSET=utf8mb4;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `tuotteet`
--

LOCK TABLES `tuotteet` WRITE;
/*!40000 ALTER TABLE `tuotteet` DISABLE KEYS */;
INSERT INTO `tuotteet` VALUES (1,1,'T-paita',1324.22,'kpl',4534,'Kaunis, valkoinen t-paita joka säähän','Pirkka','Kiina','45345643543'),(2,2,'Hernepussi',10.00,'kpl',453454,'Herkulliset tuoreet pakasteherneet','Martin pakasteherkut','Suomi','45345235gd'),(3,3,'Jogurtti',7.01,'kpl',2,'Mango','Valio','Ruotsi','4434-235gd'),(4,4,'Kuulokkeet',0.10,'kpl',224324323,'Laadukkaat vastamelukuulokkeet valoilla','Quality products Ltd','Intia','44'),(5,5,'Seppo Hovin elämänkerta',123123.20,'kpl',22323,'Kertomuksia Bumtsibumin takaa','Otava','Norja','2246gr'),(6,6,'Omena',12.90,'kg',1,'Pyöreä, vihreä omena','Adidas','Suomi','2243434-43'),(7,6,'Banaani',2.00,'kg',123,'Keltainen','Chikiitta','Alabama','235gstwt'),(8,6,'Omena',2.20,'kg',100,'Keltainen omena','Pirkka','Ruotsi','23f2523'),(9,4,'AbTronic',99.99,'kpl',34,'Ostosteeveestä tuttu','Ostostv','USofA','34wet34');
/*!40000 ALTER TABLE `tuotteet` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2022-03-16  8:06:18
