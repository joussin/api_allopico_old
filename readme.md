
  

# Documentation Api-Allopico

## Rest Resources :

**City** : Represent application list cities deserved for delivery (name, coords, ...)

**User** : auth, profile (username, email, password, address etc)

**Product** : Represent application list product (name , desc, price ...)

**Cart** : Represent temp user list product (product_ids[] , date)

**Command** : Represent user list product validated before payment (Cart_id, date, status : WAITING_PAID, PAID, WAITING_DELIVERY, CONFIRMED_DELIVERY, DELIVERY_FINISH, TERMINATED)

**Payment** : Represent a command payment (command_id, date, amount , provider (stripe, payplug), provider_extra_data ...)

**PaymentNotification** : Represent the Payment provider (stripe, payplug etc) payment notification response

**Delivery** : Represent Command Delivery (USER_ADDRESS, USER_ID, DATE_CREATION, DATE_ESTIMATED_DELIVERY, COMMAND_ID, DATE_FINISH)


```mermaid  
erDiagram
    User ||--o{ Cart : hasMany
    Cart ||--o{ Product : hasMany
    Cart ||--o{ Command : hasOne
    Command ||--o{ Payment : hasOne
    Payment ||--o{ PaymentNotification : hasOne
    Command ||--o{ Delivery : hasOne
    
    City {
        string id
    }
    
    User {
        string id
        string username
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
	}
    
    Command {
	     int id
	     int cart_id  
		string status
	}
    
    Payment {
	     int id
	     int command_id  
		string status
	}
    
    PaymentNotification {
	     int id
	     int payment_id
	}
	    
    Delivery {
	     int id
	     int command_id  
	     string status
	}
```  




## Liste des servcies:

### Api REST api-allopico:

- host: 0.0.0.0:8000

- Swagger: http://0.0.0.0:8000/api/docs/index.html

- bdd:

  - dev: dev_api_allopico


#### **Api crud des resources suivantes** : 

> City - Product - Cart - Command - Payment - PaymentNotification - Delivery

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

User->>+Cities: Set my Location & check my location availability

User->>+Products: list all Products

Products->>+User: Products[]
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
