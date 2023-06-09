
  

# Documentation Api-Allopico

## Rest Resources :

**City** : Represent application list cities deserved for delivery  

**User** : User auth, profile 

**UserLocation** : user gps location 

**Product** : Represent application list product 

**Cart** : Represent temp user list product  

**Command** : Represent user list product validated before payment

**Payment** : Represent a command payment 

**PaymentNotification** : Represent the Payment provider notification

**Delivery** : Represent Command Delivery 


```mermaid  
erDiagram
    User ||--|{ UserLocation : hasMany
    User ||--|{ Cart : hasMany
    
    Cart ||--|{ Product : hasMany
    Cart ||--|| Command : hasOne
    
    Command ||--|| Payment : hasOne
    Command ||--|| Delivery : hasOne
    Command ||--|| City : hasOne
    
    Payment ||--|| PaymentNotification : hasOne
    
   
    
    City {
        int id
        string name
	float lat "48.862725"
	float long "2.287592"
	string lat_long "48.862725,2.287592"
    }
        
    UserLocation {
        int id
        int user_id
	float lat "48.862725"
	float long "2.287592"
	string lat_long "48.862725,2.287592"
    }
    
    User {
        int id
        string username
        string email
        string password
        string address
    }
    
    
    Product {
		int id  
		int cart_id  
		String name  
		Int price  
	}
    
    Cart {
	     int id
	     int user_id
	     datetime date "2023-03-25 12:30:00, YYYY-MM-DD H:i:s"
	}
    
    Command {
	     int id
	     int cart_id
	enum status "WAITING_PAID, PAID, WAITING_DELIVERY, CONFIRMED_DELIVERY, DELIVERY_FINISH, TERMINATED"
	datetime date "2023-03-25 12:30:00"
	}
    
     
     Payment {
            int id
	     int command_id  
		 datetime date "2023-03-25 12:30:00"
		 enum status "WAITING, FAILED, SUCCESS"
		 enum provider "STRIPE, PAYPLUG, PAYPAL"
		 json provider_extra_data "{stripe_id: 1, stripe_user_id: 1, date: 2022-01-01, STATUS: OK}"
    }
    
    PaymentNotification {
	     int id
	     int payment_id
		 enum status  "WAITING, FAILED, SUCCESS"
	}
	    
    Delivery {
	     int id
	     int command_id  
	     
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

> City - UserLocation - Product - Cart - Command - Payment - PaymentNotification - Delivery

> list - create - show - update - delete - listFiltered

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

User->>+UserLocation: Set my Location

User->>+City: check my location availability

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

