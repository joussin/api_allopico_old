
  

# Documentation Api-Allopico

## Rest Resources :

#### User : auth, profile (username, email, password, address etc)
#### City : Represent application list cities deserved to shipping (name, coords, ...)

```mermaid  
erDiagram  

    User {
        string id
        string username
    }
    
    City {
        string id
    }
    
```

---

#### Product : Represent application list product (name , desc, price ...)
#### Cart : Represent temp user list product (product_ids[] , date)
#### Command : Represent user list product validated before payment (Cart_id, date, status) status : (waiting_paid, paid, waiting_shipping, confirmed_shipping, shipping_finish, terminated)
#### Payment : Represent a command payment (command_id, date, amount , provider (stripe, payplug), provider_extra_data ...)
#### PaymentNotification : Represent the Payment provider (stripe, payplug etc) payment notification response
#### Shipping : Represent Command shipping (user_address, user_id, date_creation, date_estimated_shipping, command_id, date_finish)


```mermaid  
erDiagram
    Cart ||--o{ Product : hasMany
    Cart ||--o{ Command : hasOne
    Cart ||--o{ Payment : hasOne
    Payment ||--o{ PaymentNotification : hasOne
    Command ||--o{ Shipping : hasOne
    
    Product {
		int id  
		int cart_id  
		String name  
		Int price  
	}
    
    Cart {
	     string id
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
	    
    Shipping {
	     int id
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

> City - Product - Cart - Command - Payment - PaymentNotification - Shipping

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

  

User->>+Command: Status Command as waiting_shipping

  

User->>+Payment: Confirm payment in DB (confirmPayment)

  

```

  
  
  

### Flow Shipping 

  

```mermaid

  

sequenceDiagram

  

User->>+Command: Check Commands status

  

User->>+Shipping: Check Shippings date_estimated_shipping

  

User->>+Command: Commands status

  

User->>+Command: User validate shipping (Command terminated)

  

```


# Schema diagram : Admin side

AdminApplication : livreur app / private app

Admin : AdminApplication user / livreur


### Flow Command 



```mermaid

  

sequenceDiagram

  

Admin->>+Command: List command to ship

  

Admin->>+Shipping: get Command address

  

Admin->>+Command: validate Command shipping (Command confirmed_shipping)

  

Admin->>+Shipping: Shipping date_estimated_shipping = now + 30min

  

```


### Flow Shipping 

  

```mermaid
sequenceDiagram

  

Admin->>+AdminApplication: Livreur validate ship.

  

AdminApplication->>+Shipping: Shippings date_finish=now

  

AdminApplication->>+Command: Command shipping_finish

  

```

