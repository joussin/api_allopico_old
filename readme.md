# Documentation Api-Allopico

# Stack

Domain: http://jouss.in/

Auth: laravel

payment: payPlug

Api Rest: laravel

db: mysql

Hébergement: ma droplet digital ocean

App Android: flutter

api google map

api IGN : https://geoservices.ign.fr/documentation/services/api-et-services-ogc


# Documentation tools

https://stackedit.io/app#

https://mermaid.live/

https://app.diagrams.net/

https://kroki.io/examples.html

https://kroki.io/mermaid/svg/eNq1VV1vokAUfcZfccMzNoh17fJGBFtSvyK4mz6RiYztbPgwA3bbaP_7XoYvAWv7skQnw_XM3HPOvTNSbjLyzEnYA3w2CeVwOvX7pyPM4i1JWRyBDi8kmZPoXUCqsICdwPB9TpMkRy0j2tlnQnja2kMMIl5gVjz2D9s2rEacYBKHIYn8ZpocVfxSAFfkPaRR2gS2MCYN2Cvl71dBE5ZeB1xULoaSQ5PSIk7Zjp2b2lhUkSo8ER5WsBqXU8nIHcU0exgmYz6sHqtIknIWPUNEQtqTdkFMUgjwK9_e3dz90MbaSK7CMeJk7Ua7G49-ahguliLcy3-r1ig1KsvxUWVrdsY1XlnkgMo8DMuTQ5LGIcrEl5iDXzqALmNIrrf_bwoaAsRQVvUrEUGhNhMyfexJZ6bvDkHgkWIfeaDC-kBRHPLBVmBvCoxHqqrhnLPk3Mn66HyjspmJorqtOA0JC9rBPUmSvzH32_GCZBWm0SEEHgcUa7Nx3OXcWitgWjP7l7V-UsAw5_aiwzhv-eIEH3uSVHHOKifetniSS6Mkp25NAbARsOdsK94-WhfEMXf2zAep00el_eCTlKYMt80m2BGqNuyrw742goGmD1VdVRV4wqc_n_dNEx50pmMBGjmLI34t7bkYYViSkvSAlf5t2K69uPdWhm0qkI9lrDZxslxM7fXcMs9i5cyb2gvbeVDAtdZoteFaJvL7WlZTRDGWt1DdTJ8qylXnorKSwLdSSnBJvgJD01FgatgzCw1wNpOJ5Tg1fM_jV-Zjk8uOu7ZXVubU02q2uc8nxkxA_yR4kZRQj76lnHjIhYB8zHp3T5GsDgMFireiF0Qo46wDctb66gA_SMI13I2jw_Lxo9O-l27na-Xf5_iqnRsmgNzSLdwQ5ZG6N_3VLjuvycWmB7lzYUrFddk9Dd6W01zbJ8W8sIQmOMeJ71WJWotvR58u3rGIJS-dBeOyW6UeZH9q_wBP3UyH



# DATABASE

## Rest Resources / DB models :

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


---


---

# Api REST 

## api-allopico:

- host: 0.0.0.0:3333
- Domain: jouss.in

- Swagger: http://0.0.0.0:3333/api/docs/index.html
- Swagger: http://jouss.in/api/docs/index.html

- bdd:

  - dev: dev_api_allopico



#### **Api resource User** :

> POST /user/register()
> POST /user/login()
> GET/user/logout()

> GET /user/profile()
> PUT /user/profile()


#### **Crud sur les resources suivantes** :

> Resources:

  -  Product 
  -  Cart 
  -  Command 
  -  Delivery

> GET /resource/list 
> POST /resource/create 
> GET /resource/show 
> PUT /resource/update 
> DELETE /resource/delete 
> GET /resource/listFiltered

#### **Api Geo : Location - City - Address** :

api google map
api IGN : https://geoservices.ign.fr/documentation/services/api-et-services-ogc


CRUD:

> GET User Location collection
> POST add Location to User 
> GET Location
> GET Location/all
> POST Location


> GET User Address collection
> POST add Address to User
> GET Address
> GET Address/all
> POST Address

> GET City
> GET City/all
> POST City


AUTRES ENDPOINTS:

> GET /GEO/lat_long_to_address()
> GET /GEO/lat_long_to_city()
> GET /GEO/address_to_city()
> GET /GEO/city_to_lat_long()
> GET /GEO/address_to_lat_long()
> GET /GEO/city_to_address() 
> GET /GEO/address_full_to_address_object() 
> GET /GEO/address_object_to_address_full()



#### **Api resources : Payment - PaymentNotification** :

> initPayment() - finalizePayment() - 3DS() - confirmPaymentNotification() (IPN) - confirmPayment()



---

# Application Flows 


# Diagram Flows : User side

ClientApplication : public Customer application

User : Customer

### Flow Register/login, Gps location, list Products

  

```mermaid
sequenceDiagram


ClientApplication->>+User: Login/Register

User->>-ClientApplication: logged

activate Location
ClientApplication->>+Location: Set my Location


Location->>-Address: Set my address from location

activate Address
Address-->>-ClientApplication: get address

ClientApplication->>+City: check my location availability

City-->>-ClientApplication: message error if location unavailability

ClientApplication->>+Product: list all Products

Product->>-ClientApplication: Product[]
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
  
ClientApplication->>+Delivery: Check Delivery date_estimated_delivery

Delivery->>-ClientApplication: show date_estimated_delivery


ClientApplication->>+DeliveryUser: Get Delivery man (User) Location

DeliveryUser->>-ClientApplication: show Location


User->>+Command: User validate Delivery (Command status TERMINATED)

Command->>-ClientApplication: Show Command as TERMINATED

```

# Diagram Flows : DeliveryUser side

DeliveryApplication : private livreur application

DeliveryUser : livreur


### Flow Command
```mermaid
sequenceDiagram

DeliveryUser->>+Command: List command to Delivery

DeliveryUser->>+Delivery: get Command address

DeliveryUser->>+Command: validate Command Delivery (Command status CONFIRMED_DELIVERY)

DeliveryUser->>+Delivery: Delivery date_estimated_delivery = now + 30min
```


### Flow Delivery 
```mermaid
sequenceDiagram

DeliveryUser->>+DeliveryApplication: Livreur starting Delivery. DeliveryUser->addLocation()

DeliveryApplication->>+Delivery: Delivery date_finish=now

DeliveryApplication->>+Command: Command DELIVERY_FINISH

```



# Diagram Flows : Admin side

AdminApplication : private Admin application

AdminUser : Admin de l'app

