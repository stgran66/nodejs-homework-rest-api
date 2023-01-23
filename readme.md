# Contacts REST API

> REST API written in Node.js used to store registered users contacts in MongoDB Atlas.

## Table of Contents

- [General Info](#general-information)
- [Technologies Used](#technologies-used)
- [Setup](#setup)
- [Usage](#usage)
- [Schemas](#schemas)

## General Information

This project was created for studying purposes. It is a REST API written in Node.js to store users' contacts in MongoDB Atlas.
In this project, I implemented user registration, authentication, and email confirmation. User gets a default avater when registered, and can be updated later, by user.
It should be used with a frontend app created to manage user contacts.

## Technologies Used

Node.js, Express, MongoDB, MongoDB Atlas, Mongoose, JEST

## Setup

To setup project just download repository and install dependencies. Example configuration file .env.example is in route directory. Your configuration should be stored in .env file in project route directory.

- PORT - can be ommited, by default will use 3000.
- DB_HOST - here should be a link with username and password to connect to MongoDB Atlas. Is provided by MongoDB Atlas control panel, when clicking connect button to cluster.
- SECRET_KEY - is some random characters sequence for hashing data
- SENDGRID_API_KEY - is an API key you get by registration in sendgrid API. It is used to send emails to users.

## Usage

Endpoints to use

### Authentication

#### POST method /api/auth/signup

Create a new user

- Parameters:
  No parameters
- Request body:
  {
  "email" : "some.valid@email.com"
  "password" : "atLeast6charsPassword"
  }
- Responses:
  201 - User created
  400 - Validation error
  409 - Email in use
  500 - Server error

#### POST method /api/auth/login

Log in User

- Parameters:
  No parameters
- Request body:
  {
  "email" : "your@email.com"
  "password" : "yourPassword"
  }
  - Responses:
    200 - User is logged in
    400 - Validation error
    401 - Email or password incorrect or email not verified
    500 - Server error

#### POST method /api/auth/logout

Log out user

- Parameters:
  Authorization(required) header: string - token issued to the current user.

- Responses:
  200 - User is logged out
  401 - Missing or incorrect header with authorization token
  500 - Server error

#### GET method /api/auth/current

Get information about the current user

- Parameters:
  Authorization(required) header: string - token issued to the current user.

- Responses:
  200 - Information found
  401 - Missing or incorrect header with authorization token
  500 - Server error

### User

#### PATCH method /api/users/

Update user subscription

- Parameters:
  No parameters
- Request body:
  {
  "subscription" : "starter" || "pro" || "business"
  }
- Responses:
  200 - Subscription updated
  400 - Validation error
  401 - Missing or incorrect header with authorization token
  500 - Server error

#### PATCH method /api/users/avatars

Update user avatar

- Parameters:
  Authorization(required) header: string - token issued to the current user.
  Content-Type header: multipart/form-data
- Request body:
  uploaded image file
- Responses:
  200 - Avatat updated
  401 - Missing or incorrect header with authorization token
  500 - Server error

#### GET method /api/users/verify/:verificationToken

Verify user email

- Parameters:
  verificationToken: send to users' email when signup
- Responses:
  200 - Verification success
  404 - User not found
  500 - Server error

#### POST method /api/users/verify

Resend verification email

- Parameters:
  no parameters
- Request body:
  {
  "email": "user@email.com"
  }
- Responses:
  200 - Verification success
  400 - Validation error or Email already verified
  404 - User not found
  500 - Server error

### Contacts

#### GET method /api/contacts/

Get all users contacts

- Parameters:
  Authorization(required) header: string - token issued to the current user.
  page (optional): number of page to display
  limit (optional): contacts on one page
  favorite (optional): true || false - filter contacts by favorite
- Responses:
  200 - Contacts found
  401 - Missing or incorrect header with authorization token
  404 - There is no such user collection
  500 - Server error

#### GET method /api/contacts/:id

Get one users' contact by id

- Parameters:
  Authorization(required) header: string - token issued to the current user.
  id: contact id
- Responses:
  200 - Contacts found
  401 - Missing or incorrect header with authorization token
  404 - There is no such user contact
  500 - Server error

#### POST method /api/contacts/

Add contact to users' collection

- Parameters:
  Authorization(required) header: string - token issued to the current user.
  id: contact id
- Request body:
  {
  "name": "contactName",
  "email" : "contact@email.com",
  "phone" : "contactPhoneNumber"
  "favorite": true || false - optional, false by default
  }
- Responses:
  201 - Contact added
  400 - Validation error
  401 - Missing or incorrect header with authorization token
  500 - Server error

#### DELETE method /api/contacts/:id

Delete contact from users' collection

- Parameters:
  Authorization(required) header: string - token issued to the current user.
  id: contact id

- Responses:
  200 - Contact deleted
  401 - Missing or incorrect header with authorization token
  404 - Contact not found
  500 - Server error

#### PUT method /api/contacts/:id

Update contact from users' collection

- Parameters:
  Authorization(required) header: string - token issued to the current user.
  id: contact id
- Request body:
  {
  at least one key from schema
  }
- Responses:
  200 - Contact updated
  400 - Validation error
  401 - Missing or incorrect header with authorization token
  500 - Server error

#### PATCH method /api/contacts/:id/favorite

Update contact favorite status

- Parameters:
  Authorization(required) header: string - token issued to the current user.
  id: contact id
- Request body:
  {
  "favorite" : "true" || "false
  }
- Responses:
  200 - Contact favorite status updated
  400 - Validation error
  401 - Missing or incorrect header with authorization token
  500 - Server error

## Schemas

### User

- password:
  required
  string
  at least 6 char length
- email:
  required
  string
  unique
  email address
- subscription:
  string
  enum: ["starter", "pro", "business"]
  default: "starter"
- token:
  string
  default: null
  backend-generated authentication token
- avatarURL:
  required,
  string,
  backend-generated url to users' avatar
- verified:
  required,
  boolean,
  default: false
- verificationToken:
  required
  string
  backend-generated token for email verification

### Contact

- name:
  required
  string
- email:
  string
  email adress
- phone:
  string
  phone number
- favorite:
  required
  boolean
  default: false
- owner:
  required,
  backend-generated user ID
