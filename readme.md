
  

# Documentation Api-Allopico

## Rest Resources :

**City** : Represent la liste des villes déservies.  

**User** : User auth, profile 

**Location** : Represent a gps location. Obtenu grâce au gps du tel. Le user possède une collection de Location.

**Address** : Represent a location address. Addresse complète en une chaine. Obtenu grâce à l'api G+ map à partir des coordonnées. coords -> full address

**Product** : Represent un produit de l'application.

**Cart** : Represent an user list of selected products. temporaire, modififiable ... persisté dans le but d'améliorer l'ux

**Command** : Represent a Cart validated before payment. Panier validé puis stocké en base en vue d'être réglé.

**Payment** : Represent a command payment.

**PaymentNotification** : Represent the Payment provider notification

**Delivery** : Represent Command Delivery 


```mermaid  
erDiagram
    User ||--|{ Location : hasMany
    Location ||--|| Address : hasOne
    User ||--|{ Cart : hasMany
    
    Cart ||--|{ Product : hasMany
    Cart ||--|| Command : hasOne
    
    Command ||--|| Payment : hasOne
    Command ||--|| Delivery : hasOne
    Command ||--|| City : hasOne
    Command ||--|| Address : hasOne
    
    Payment ||--|| PaymentNotification : hasOne
    
    Delivery ||--|{ User : hasOne

    
    
    City {
        int id PK
        string name
	float lat "48.862725"
	float long "2.287592"
	string lat_long "48.862725,2.287592"
    }
        
    Location {
        int id PK
        int user_id "Customer id or delivery man id"
        
	float lat "48.862725"
	float long "2.287592"
	string lat_long "48.862725,2.287592"
    }        
    
    Address {
        int id PK
        int location_id FK
	    string full_address "10 Rue de la Paix, 75002 Paris"
    }
    
    User {
        int id PK
        string username
        string email
        string password
        string address
        enum role "CUSTOMER, DELIVERY, ADMIN"
    }
    
    
    Product {
		int id PK  
		int cart_id FK
		String name  
		Int price  
	}
    
    Cart {
	     int id PK
	     int user_id FK
	     datetime date "2023-03-25 12:30:00, YYYY-MM-DD H:i:s"
	}
    
    Command {
	     int id PK
	     int cart_id FK
	enum status "WAITING_PAID, PAID, WAITING_DELIVERY, CONFIRMED_DELIVERY, DELIVERY_FINISH, TERMINATED"
	datetime date "2023-03-25 12:30:00"
	}
    
     
     Payment {
            int id PK
	     int command_id FK 
		 datetime date "2023-03-25 12:30:00"
		 enum status "WAITING, 3DS, FAILED, SUCCESS"
		 enum provider "STRIPE, PAYPLUG, PAYPAL"
		 json provider_extra_data "{stripe_id: 1, stripe_user_id: 1, date: 2022-01-01, STATUS: OK}"
    }
    
    PaymentNotification {
	     int id PK
	     int payment_id FK
		 enum status  "FAILED, SUCCESS, 3DS"
	}
	    
    Delivery {
	     int id PK
	     int command_id FK
	     int user_id FK "delivery man id"
	     
	     datetime date_creation "2023-03-25 12:30:00"
	     datetime date_estimated_delivery "2023-03-25 12:45:00"
	     datetime date_finish "2023-03-25 12:47:00"
	}
	
 

  
```  




## Liste des servcies:

### Api REST api-allopico:

- host: 0.0.0.0:8000

- Swagger: http://0.0.0.0:8000/api/docs/index.html

- bdd:

  - dev: dev_api_allopico


#### **Api crud des resources suivantes** : 

> City - Address - Location - Product - Cart - Command - Payment - PaymentNotification - Delivery

> list - create - show - update - delete - listFiltered

#### **Api Geo (avec api google map & api IGN : https://geoservices.ign.fr/documentation/services/api-et-services-ogc) : Location - City - Address** :

> lat_long_to_address() - lat_long_to_city() - address_to_city() - city_to_lat_long()

> address_to_lat_long() - city_to_address() - address_full_to_address_object() - address_object_to_address_full() 


#### **Api resource User** :

> register() - login() - logout() - profile()


#### **Api resource Payment** :

> initPayment() - finalizePayment() - 3DS() - confirmPaymentNotification() (IPN) - confirmPayment()

  

# Schema diagram : User side

ClientApplication : user app / public app

User : ClientApplication user

### Flow Register/login, Gps location, list Products

  

```mermaid
sequenceDiagram

ClientApplication->>+User: Login/Register

User->>+Location: Set my Location

Location->>+Address: Set my address from location

ClientApplication->>+City: check my location availability

User->>+Product: list all Products

Product->>+User: Product[]
```

  

### Flow Command and payment 
  

```mermaid
sequenceDiagram

User->>+Cart: Add Product to cart

User->>+Command: Validate Cart & create a Command

User->>+Payment: Validate Command & Choose payment provider

User->>+Payment: Show payment provider form (initPayment)

User->>+Payment: Validate payment (finalizePayment)

Payment->>+User: Payment notification (confirmPaymentNotification)

User->>+Command: Status Command as Paid in DB

User->>+Command: Status Command as waiting_delivery

User->>+Payment: Confirm payment in DB (confirmPayment)

```

### Flow Delivery 


```mermaid
sequenceDiagram

  

User->>+Command: Check Commands status

  

User->>+Delivery: Check Delivery date_estimated_delivery

  

User->>+Command: Commands status

  

User->>+Command: User validate Delivery (Command terminated)

  

```


# Schema diagram : Admin side

AdminApplication : livreur app / private app

Admin : AdminApplication user / livreur


### Flow Command
```mermaid
sequenceDiagram

  

Admin->>+Command: List command to ship

  

Admin->>+Delivery: get Command address

  

Admin->>+Command: validate Command Delivery (Command confirmed_delivery)

  

Admin->>+Delivery: Delivery date_estimated_delivery = now + 30min

  

```


### Flow Delivery 
```mermaid
sequenceDiagram

  

Admin->>+AdminApplication: Livreur validate ship.

  

AdminApplication->>+Delivery: Delivery date_finish=now

  

AdminApplication->>+Command: Command delivery_finish

  

```

