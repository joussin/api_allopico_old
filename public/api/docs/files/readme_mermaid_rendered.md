# Documentation Api-Allopico

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

[![](https://mermaid.ink/img/pako:eNq9Vu-PsjgQ_lcaPuMu4vpj-WaEfV-y6hrRu2xCYvpC1d4hmFL2XqP-7zelRRA53U9HtNaZp52ZZ6ZTjlqQhESzNMJsijcM7_wYwbNMCUOnU6t1OqJxEmBOkxhZaIvTCY4PEnOR57gTGoYhI2kqYR8xud1phBmv7yLHXKNQM5aEWXADLCEnNEp2OxyHNVMKp3QKOsOHHYl5DVoD2SSiX4Qd7qNGlD9ANFMgx8KRa7-mCadrWiW4tuzimmIn57MEVqHKJeHlUc7FQ8EmDdHsvRSlnNF4g2K8EzvwdZRgjiL4-trL4GnQM_tm19cqqgTQvmY-mYN-99WUKrUJLFsp_WWtXkUKg-fSdq127jsqRBkEvAK5r42ylCc7CB_-JQyFBTOQARAVtqSR_yWq65jkWJTA48AixYEI7u1dGK_kZp1F0QqrvXytbaB5RiBk8AxKh_7WUb9rGCbMGU1rNFcO3rfqQDAsa6GmIDtMoxvpHqfpPwkLbxTK3VJO4myHWBIRkbylt_iYOHMd2c7Y_cOZf-poaE_caaPz6sioTnAU5Pj8EoFMsBQE0BZKBn3ulbVdwFyA7RkNlOB803iOBflVkiqSogTLLKEQc8Ip2BATUUWG2WkZnZbZRW3T6hiWYejoE57WZNKybfTTolYqS-zc1K0euXAdZk5syjHPRHH8OXQX7vTHajZ0bR3JsZCVZI8-pm_ufOLYFVkxW725U9f7qaOFM4eUDBeOLX39RpgNQRU_RcurVOGdCCUTMkiVOfRt-4BtJEVHHdvT0dvQHTtAi7ccjRzPu16zZ8kXDeG0-Jq3mLszR5D4ORsvf8jJcHzB_5VCzyrwK_KbM7wC1zAsPYpTsCfgv4XaOlL_VOnkIhGDhSAEs2W04QPuLIaLpWehj_dz4zFouiQeVcperqmeiStqwNUaGzlHlzTKrWqXz8PyrCbvP84OGG5o2QrafLBWASMy6jupb1hGUpjDJFxdLNY3eOne22BNY5pubxf1qwUP1MZIXsGarsHFBO0yhHepvNx9jW8J9FTNEnFj9rdYdgYcznjiHeJAs9Y4SomuZXthUr1-XaQkpDxhE_l2lr-kFUgn11yAcJdBLWrWUeOHvQBvaMoBHCTxmm6EPGMRiLec71Pr-VmonzaUb7NfT5C255SGW2gu26_X3nPP7A2w2SG9fgd3O50w-NV-HazNl_Y67BttE2vn8_lf7RIc-w?type=png)](https://mermaid.live/edit#pako:eNq9Vu-PsjgQ_lcaPuMu4vpj-WaEfV-y6hrRu2xCYvpC1d4hmFL2XqP-7zelRRA53U9HtNaZp52ZZ6ZTjlqQhESzNMJsijcM7_wYwbNMCUOnU6t1OqJxEmBOkxhZaIvTCY4PEnOR57gTGoYhI2kqYR8xud1phBmv7yLHXKNQM5aEWXADLCEnNEp2OxyHNVMKp3QKOsOHHYl5DVoD2SSiX4Qd7qNGlD9ANFMgx8KRa7-mCadrWiW4tuzimmIn57MEVqHKJeHlUc7FQ8EmDdHsvRSlnNF4g2K8EzvwdZRgjiL4-trL4GnQM_tm19cqqgTQvmY-mYN-99WUKrUJLFsp_WWtXkUKg-fSdq127jsqRBkEvAK5r42ylCc7CB_-JQyFBTOQARAVtqSR_yWq65jkWJTA48AixYEI7u1dGK_kZp1F0QqrvXytbaB5RiBk8AxKh_7WUb9rGCbMGU1rNFcO3rfqQDAsa6GmIDtMoxvpHqfpPwkLbxTK3VJO4myHWBIRkbylt_iYOHMd2c7Y_cOZf-poaE_caaPz6sioTnAU5Pj8EoFMsBQE0BZKBn3ulbVdwFyA7RkNlOB803iOBflVkiqSogTLLKEQc8Ip2BATUUWG2WkZnZbZRW3T6hiWYejoE57WZNKybfTTolYqS-zc1K0euXAdZk5syjHPRHH8OXQX7vTHajZ0bR3JsZCVZI8-pm_ufOLYFVkxW725U9f7qaOFM4eUDBeOLX39RpgNQRU_RcurVOGdCCUTMkiVOfRt-4BtJEVHHdvT0dvQHTtAi7ccjRzPu16zZ8kXDeG0-Jq3mLszR5D4ORsvf8jJcHzB_5VCzyrwK_KbM7wC1zAsPYpTsCfgv4XaOlL_VOnkIhGDhSAEs2W04QPuLIaLpWehj_dz4zFouiQeVcperqmeiStqwNUaGzlHlzTKrWqXz8PyrCbvP84OGG5o2QrafLBWASMy6jupb1hGUpjDJFxdLNY3eOne22BNY5pubxf1qwUP1MZIXsGarsHFBO0yhHepvNx9jW8J9FTNEnFj9rdYdgYcznjiHeJAs9Y4SomuZXthUr1-XaQkpDxhE_l2lr-kFUgn11yAcJdBLWrWUeOHvQBvaMoBHCTxmm6EPGMRiLec71Pr-VmonzaUb7NfT5C255SGW2gu26_X3nPP7A2w2SG9fgd3O50w-NV-HazNl_Y67BttE2vn8_lf7RIc-w)

---

# Api REST 

## api-allopico:

- host: 0.0.0.0:8000

- Swagger: http://0.0.0.0:8000/api/docs/index.html

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

