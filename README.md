# Technical Challenge

## Backend - Sample Launch Pizza Endpoints

### Overview

- User:
    - Create User: `POST /api/users`
    - Update Account: `PATCH /api/users/{userID}/account`
    - Query User: `GET /api/users/{userID}/account?<info e.g. type=name,email>`
    - Query User: `GET /api/users/{userID}/account`
- Guest:
    - Register: `POST /api/users/guests`  
        A temporary user that holds a contact email that will be deleted after a year. Login through email security code
- Menu:
    - List All: `GET /api/menu?<filter>`
    - List All: `GET /api/menu`
    - Query: `GET /api/menu/{id}`
- Cart:
    - Add to Cart: `POST /api/users/{userID}/cart`
    - List Cart: `GET /api/users/{userID}/cart?<filter>`
    - List Cart: `GET /api/users/{userID}/cart`
    - Modify Item: `PATCH /api/users/{userID}/cart/{itemID}`
    - Modify Item: `PUT /api/users/{userID}/cart/{itemID}`
    - Delete Item: `DELETE /api/users/{userID}/cart/{itemID}`
    - Delete All: `DELETE /api/users/{userID}/cart`
- Order:
    - Create Order: `POST /api/users/{userID}/orders`
    - Modify Order: `PATCH /api/users/{userID}/orders/{orderID}`
    - List Orders: `GET /api/users/{userID}/orders?<filter(e.g. start=&end=)>`
    - List Orders: `GET /api/users/{userID}/orders`
    - Order Detail: `GET /api/users/{userID}/orders/{orderID}` (can be used to track)
    - Live update: `ws /api/users/{userID}/orders/{orderID}`

> I just realized detailed documentation is not needed. Below are detailed documentation and examples

### Auth

#### Login

Login verifies the user's credential

##### **Request**

- Path: `/api/auth`

- Method: `POST`

- Body:

    | Parameter  | Type   | Description            |
    | :--------- | ------ | ---------------------- |
    | `username` | string | user's name            |
    | `password` | string | password in plain text |

    Example:

    ```json
    {
        "username": "username",
        "password": "123",
    }
    ```

##### Response

###### Success

- Status Code: `200 OK`
- Body:

    ```json
        {
            "token": "<jwt-token>",
            "expires": "<date>",
            "user": {
                "name": "username",
                "id": id,
            },
        }
    ```

###### Error

- Status Code: `403 Forbidden`
- Body:

    ```json
        {
            "error": "invalid credential"
        }
    ```

### User

#### Sign Up

Sign up lets customer to sign up an account

##### **Request**

- Path: `/api/users`

- Method: `POST`

- Body:

    | Parameter  | Type   | Description            |
    | :--------- | ------ | ---------------------- |
    | `username` | string | user's name            |
    | `password` | string | password in plain text |

    Example:

    ```json
    {
        "username": "username",
        "password": "123",
    }
    ```

##### Response

###### Success

- Status Code: `201 Created`
- Body:

    ```json
        {
            "user": {<user>},
            "url": "https://piazza.com/users/<userID>/account"
        }
    ```

###### Error

- Status Code: `400 Bad Request`
- Body:

    ```json
        {
            "error": "<reason>"
        }
    ```

### Order

#### Create Order

Create an order, auth token required

##### **Request**

- Path: `/api/users/{userID}/orders`

- Method: `POST`

- Body:

    | Parameter  | Type   | Description            |
    | :--------- | ------ | ---------------------- |
    | `Items` | array | ordered items            |

    Example:

    ```json
    {
        "items": [
            {
                "id": 123,
                "quantity": 2,
                "toppings": [
                    {
                        "id": 456,
                        "quantity": 1,
                        "unitPrice": 0.5,
                        "totalPrice": 1.00
                    }
                ],
                "unitPrice": 9.00,
                "totalPrice": 19.00
            },
            {
                "id": 777,
                "quantity": 1,
                "unitPrice": 10.00,
                "totalPrice": 10.00
            }
        ]
    }
    ```

##### Response

###### Success

- Status Code: `201 Created`
- Body:

    ```json
        {
            "orderID": <id>,
            "url": "https://pizza.com/api/<userID>/order/<orderID>"
        }
    ```

###### Error

- Status Code: `400 Bad Request`
- Body:

    ```json
        {
            "error": "<reason>"
        }
    ```

#### Order Detail

Query the detail of an order, auth token required

##### **Request**

- Path: `/api/users/{userID}/orders/{orderID}`

- Method: `GET`

- Body:

##### Response

###### Success

- Status Code: `200 OK`
- Body:

    ```json
        {
            "status": <status>,
            "placedOn": "date",
            "deliveredOn": "omitted if not delivered",
            "estimatedDelivery": "omitted if delivered",
            "liveUpdate": "websocket url, omitted if delivered",
            "by": <userID>,
            "address": "address",
            "payment": "debit/cash/credit/paypal",
            "bill": {
                "subtotal": <dollar>,
                "tax": <tax>,
                "shipping": <shipping>,
                "tips": <tips>,
                "grandtotal": <dollar>
            },
            "items": [<items>],
        }
    ```

###### Error

- Status Code: `400 Bad Request`, `404 Not Found`
- Body:

    ```json
        {
            "error": "<reason>"
        }
    ```
