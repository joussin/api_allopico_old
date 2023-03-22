
  

# Documentation Api-Allopico

## Resources Rest :

- Users : auth, profile (username, email, password, address etc)

  

- Cities : Represent application list cities deserved to shipping (name, coords, ...)

  

- Products : Represent application list product (name , desc, price ...)

  

- Carts : Represent temp user list product (product_ids[] , date)

  

- Commands : Represent user list product validated before payment (Cart_id, date, status) status : (waiting_paid, paid, waiting_shipping, confirmed_shipping, shipping_finish, terminated)

  

- Payments : Represent a command payment (command_id, date, amount , provider (stripe, payplug), provider_extra_data ...)

  

- PaymentNotifications : Represent the Payment provider (stripe, payplug etc) payment notification response

  

- Shippings : Represent Command shipping (user_address, user_id, date_creation, date_estimated_shipping, command_id, date_finish)



## Rest Resources Models:

  

```mermaid
classDiagram
class User{

+int id

+String username

+String email

+String password

+String address

  

+register()

+login()

+logout()

+profile()

}
```
  
  
```mermaid
classDiagram
class Cart{

+int id

}
```
  
```mermaid
classDiagram
class Product{

+int id

+int cart_id

+String name

+Int price

}
```
  
```mermaid
classDiagram
class Command{

+int id

+int cart_id

}
```
  
```mermaid
classDiagram
class Payment{

+int id

+int command_id

}
```
  
  
  
  
```mermaid
classDiagram
class PaymentNotification{

+int id

+int payment_id

}
```

## Liste des servcies:

### api-allopico:

- host: 0.0.0.0:8000

- Swagger: http://0.0.0.0:8000/api/docs/index.html

- bdd:

- dev: dev_api_allopico

  
  

- Description :

>  **Api rest crud des resources** :

> Cities

> Products

> Carts

> Commands

> Payments

> PaymentNotifications

> Shippings

>  **Api User** :

> register

> login

> logout

> profile

>  **Api Payment** :

> initPayment

> finalizePayment

> 3DS

> confirmPaymentNotification : IPN

> confirmPayment

  

## Schema diagram :

ClientApplication : user app / public app

  

User : ClientApplication user

  

AdminApplication : livreur app / private app

  

Admin : AdminApplication user / livreur

  

### Flow Register/login, Gps location, list Products

  

```mermaid

  

sequenceDiagram

  

ClientApplication->>+User: Login/Register

  

User->>+Cities: Set my Location & check my location availability

  

User->>+Products: list all Products

  

Products->>+User: Products[]

  

```

  

### Flow Command and payment : User Side

  

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

  
  

### Flow Command : Admin Side

  

```mermaid

  

sequenceDiagram

  

Admin->>+Command: List command to ship

  

Admin->>+Shipping: get Command address

  

Admin->>+Command: validate Command shipping (Command confirmed_shipping)

  

Admin->>+Shipping: Shipping date_estimated_shipping = now + 30min

  

```

  
  
  

### Flow Shipping : User side

  

```mermaid

  

sequenceDiagram

  

User->>+Command: Check Commands status

  

User->>+Shipping: Check Shippings date_estimated_shipping

  

User->>+Command: Commands status

  

User->>+Command: User validate shipping (Command terminated)

  

```

  
  

### Flow Shipping : Admin Side

  

```mermaid
sequenceDiagram

  

Admin->>+AdminApplication: Livreur validate ship.

  

AdminApplication->>+Shipping: Shippings date_finish=now

  

AdminApplication->>+Command: Command shipping_finish

  

```