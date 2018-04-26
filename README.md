# Gestion de l'appel
V0.2 20180323

# Introduction

## Front iOS

* Système de gestion de pointage en entreprise.
* L'employé flash un QR code correspondant à l'emplacement de son activité. 
* Le QR code change toutes les 30 secondes pour éviter les envois par SMS. 
* La vérification est double avec un beacon qui doit être à proximité.

## Back Symfony

* Gestion des utilisateurs
    * utilisateurs de test (username, password) ```tinkywinky, qwerty``` et ```lala, password```
* Gestion des tokens
* Génération des QR codes
* Rapport (statistiques sur les retards, absences sur les 30 derniers jours flottants)

# Entités
Note : Tous les users sont dans tous les événements. Pas de back-office.
* Event (codé en dur dans la database)
    * Name string 40
    * Location 64 bits (FK)
    * Date Datetime
* User
    * Token string 40
    * Name string 50
    * Email string 300
    * Password sha512 string 150 
* Location
    * Id 64 bits
    * Beacon (mineur + majeur) 32 bits
    * QRCode? String 40 octets
    * description Text textuelle 

# API

##	POST /api/login

### Input

* Email string 300
* Password hashed sha512 string 

```json
{
	"Email": "1234567890123456789012345678901234567890",
	"Password": "1234567890123456789012345678901234567890"
}
```

### Return

* Token string 40

```json
{
	"token": "1234567890123456789012345678901234567890"
}
```

##	POST /api/refreshToken

### Input:

* token

```json
{
	"token": "1234567890123456789012345678901234567890"
}
```

### Return:

* token

```json
{
	"token": "1234567890123456789012345678901234567890"
}
```

##	GET /api/getLocation (salle prévue)

### Input

* token string 40

```json
{
	"token": "1234567890123456789012345678901234567890"
}
```


### Return

* date string ISO 8601
* location string 40

```json
{
	"date": "",
	"location": "Salle 7"
}
```

##	POST /api/checkIn

### Input

* QRCodeData string 40
* date string ISO 8601
* beaconCollection \[int\]
* Token string 40

```json
{
	"QRCodeData" : "OK",
	"date": "",
	"beaconCollection":
		[
			12,
			44, 
			128
		]
	},
	"Token": "1234567890123456789012345678901234567890"
}
```

### Return

* String status
	* HTTP status 200 "OK"
	* HTTP status 404 "KO"

200
```json
{
	"response" : "OK"
}
```

404
```json
{
	"response" : "KO"
}
```


# Web

## GET /

Select a location. Posts to /getQRCode

## POST /getQRCode

### input

* location string

Displays a QRCode relevent to the location and refreshes every 30 seconds.
