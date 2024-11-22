# Library API

The Library API is a robust backend service designed to manage a library's resources efficiently. It provides endpoints to manage books, authors, users, and borrowing records. The API uses JWT authentication for secure access and token management to enhance security.

## Features

- **JWT Authentication**: Secure access to the API with JSON Web Tokens.
- **Token Revocation**: Ensures tokens are valid for only one use, enhancing security.
- **CRUD Operations**: Manage users, authors, book, and book-author relationships.
- **Error Handling**: Comprehensive error messages for better debugging and client understanding.

## Technologies Used

- **PHP**: Core backend logic using the Slim framework.
- **Slim Framework**: Lightweight and fast framework for PHP.
- **Firebase JWT**: Library for handling JSON Web Tokens.
- **PSR-7**: HTTP message interfaces for standardized request and response handling.

---

## Authentication (JWT)

### Overview

The API uses JWT for secure authentication:
- Clients must include a token in the `Authorization` header of each request.
- Tokens are validated, and revoked after a single use to prevent reuse.
- Revoked tokens are tracked using PHP sessions.

### Token Management

- **Generation**: Tokens are generated after successful login.
- **Validation**: Middleware ensures the token is valid and not expired.
- **Revocation**: Tokens are invalidated after their first use.

---

#  CRUD OPERATIONS
### A1. USER AUTHENTICATE
  - **Endpoint:** `/user/authenticate`  
  - **Method:** `POST`  
  - **Description:** 
    Validates user credentials by checking the provided username and password against the database. If authentication is successful, a JSON Web Token (JWT) is generated and returned for secure session handling.  - **Sample Request(JSON):**
      ```json
          {
            "username": "janedoe",
            "password": "securepassword123"
          }
      ```
  - **Response:**
      - **On Success**
          ```json
              {
                  "status": "success",
                  "token": "<TOKEN>",
                  "data": null
              }
          ```
      - **On Failure (Authenthication Failed):**
          ```json
              {
                  "status": "fail",
                  "data": {
                      "title": "Authentication Failed!"
                  }
              }
          ```
---
### A2. USER REGISTER
- **Endpoint:** `/user/register`  
- **Method:** `POST`  
- **Description:**  
Creates a new user account by accepting a unique username and password. Ensures both inputs are at least five characters long. The password is hashed before storing it securely in the database.
- **Sample Request (JSON):**
    ```json
    {
        "username": "janedoe",
        "password": "mypassword123"
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "data": null
        }
        ```

    - **On Failure (Invalid Input):**
        ```json
        {
            "status": "fail",
            "data": "Username and password must be at least 5 characters long and not empty"
        }
        ```

    - **On Failure (Duplicae Username):**
        ```json
        {
            "status": "fail",
            "data": "Username already exists"
        }
        ```
---
### A3. USER VIEW
- **Endpoint:** `/user/read`  
- **Method:** `GET`  
- **Description:**  
Retrieves a list of all registered users, displaying their IDs and usernames. A valid JWT token must be provided to access this endpoint.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Sample Request:**
    - **No request body is required. Authentication is handled via the Authorization header.**

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "userid": 1,
                    "username": "johndoe"
                },
                {
                    "userid": 2,
                    "username": "janedoe"
                }
            ]
        }
        ```
    - **On Failure (Unauthorized):**
        ```json
        {
            "status": "fail",
            "data": "Unauthorized: Token not provided"
        }
        ```

    - **On Failure (No Users Found):**
        ```json
        {
            "status": "fail",
            "data": "No users found"
        }
        ```
---
### A4. USER UPDATE
- **Endpoint:** `/user/update`  
- **Method:** `PUT`  
- **Description:**  
  Allows an authenticated user to update their username and password. The new credentials must meet the minimum length requirements and the user's ID is used to identify the record for updating.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "userid": "1",
        "username": "newusername",
        "password": "newpassword123"
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "User updated successfully"
        }
        ```

    - **On Failure (User Not Found):**
        ```json
        {
            "status": "fail",
            "data": "User with ID 1 does not exist."
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Username and password must be at least 5 characters long and not empty"
        }
        ```
---
### A5. USER DELETE
- **Endpoint:** `/user/delete`  
- **Method:** `DELETE`  
- **Description:**  
  Deletes a specific user based on their ID. The endpoint requires a valid JWT token and verifies the existence of the user before performing the deletion.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "userid": "2"
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "User deleted successfully"
        }
        ```

    - **On Failure (User Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "User with ID 2 does not exist."
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "User ID must be provided."
        }
        ```
---


### B1. AUTHOR CREATE
- **Endpoint:** `/authors/create`  
- **Method:** `POST`  
- **Description:**  
  Adds a new author to the database. The author’s name must be unique, at least five characters long, and cannot be blank.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "name": "Jane Austen"
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Author created successfully"
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Author name must be more than 4 characters and cannot be blank."
        }
        ```
---
### B2. AUTHOR UPDATE
- **Endpoint:** `/authors/update`  
- **Method:** `PUT`  
- **Description:**  
  This API endpoint allows updating the information of an existing author. The request payload must include the `authorid` (the ID of the author to be updated) and the new `name` of the author. The name must be at least 5 characters long. The operation is protected by a JWT token, which will be rotated and returned upon a successful request.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "authorid": "3",
        "name": "Mark Twain"
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Author updated successfully"
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Author name must be more than 4 characters and cannot be blank."
        }
        ```

    - **On Failure (Author Not Found):**
        ```json
        {
            "status": "fail",
            "data": "Author with ID 3 does not exist."
        }
        ```
---
### B3. AUTHOR VIEW
- **Endpoint:** `/authors/read`  
- **Method:** `GET`  
- **Description:**  
  Retrieves a list of all authors in the database, displaying their unique IDs and names.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Sample Request:**
    - **No request body is required. Authentication is handled via the Authorization header.**

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "authorid": 1,
                    "name": "Jane Austen"
                },
                {
                    "authorid": 2,
                    "name": "Charles Dickens"
                },
                ...
            ]
        }
        ```

    - **On Failure (No Authors Found):**
        ```json
        {
            "status": "fail",
            "data": "No authors found"
        }
        ```
---
### B4. AUTHOR DELETE
- **Endpoint:** `/authors/delete`  
- **Method:** `DELETE`  
- **Description:**  
  Deletes an existing author from the database based on their unique ID.
- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
      "authorid": 4
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Author deleted successfully"
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Author ID must be provided."
        }
        ```

    - **On Failure (Author Not Found):**
        ```json
        {
            "status": "fail",
            "data": "Author with ID 4 does not exist."
        }
        ```
---


### C1. BOOK CREATE
- **Endpoint:** `/books/create`  
- **Method:** `POST`  
- **Description:**  
  Creates a new book record in the database. The request requires the title of the book and the authorid (the ID of the author) to associate the book with. The operation is protected by a JWT token, and the response will include a new token as well as a confirmation message.

- **Authentication:**  
  Requires a valid JWT token. The JWT token must be passed in the Authorization header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "title": "She's dating a gangster",
        "authorid": 1
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book created successfully"
        }
        ```

    - **On Failure (Book Title Invalid):**
        ```json
        {
            "status": "fail",
            "data": "Book title must be more than 4 characters and cannot be blank."
        }
        ```

    - **On Failure (Author Not Found):**
        ```json
        {
            "status": "fail",
            "data": "Author not found"
        }
        ```
---
### C2. BOOK VIEW
- **Endpoint:** `/books/read`  
- **Method:** `GET`  
- **Description:**  
  Retrieves a list of all books in the database, displaying their book ID, title, author ID, and author name.

- **Authentication:**  
  Requires a valid JWT token. The JWT token must be passed in the Authorization header as a Bearer token.

- **Sample Request:**
    - **No request body is required. Authentication is handled via the Authorization header.**

- **Response:**
    - **On Success (Books Retrieved):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "bookid": 1,
                    "title": "Greed",
                    "authorid": 1,
                    "author_name": "Robert Costco"
                },
                {
                    "bookid": 2,
                    "title": "The Greath Death",
                    "authorid": 2,
                    "author_name": "Burly Monac"
                },
                ...
            ]
        }
        ```

    - **On Failure (No Books Found):**
        ```json
        {
            "status": "fail",
            "data": "No books found"
        }
        ```
---

### C3. BOOK UPDATE
- **Endpoint:** `/books/update`  
- **Method:** `DELETE`  
- **Description:**  
  Updates the information of an existing book. The request must include the bookid (the ID of the book to be updated), the new title of the book, and the authorid. The name must be at least 5 characters long.

- **Authentication:**  
  Requires a valid JWT token. The JWT token must be passed in the Authorization header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "bookid": 1,
        "title": "The Great Death",
        "authorid": 1
    }
    ```

- **Response:**
    - **On Success (Book Updated):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book updated successfully"
        }
        ```

    - **On Failure (Book Not Found):**
        ```json
        {
            "status": "fail",
            "data": "Book with ID <Book_ID> does not exist."
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Book title must be more than 4 characters and cannot be blank."
        }
        ```
---

### C4. BOOK DELETE
- **Endpoint:** `/books/delete`  
- **Method:** `DELETE`  
- **Description:**  
  Deletes an existing book from the database based on their unique ID.

- **Authentication:**  
  Requires a valid JWT token. The JWT token must be passed in the Authorization header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "bookid": 4
    }
    ```

- **Response:**
    - **On Success (Book Deleted):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book deleted successfully"
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Book ID must be provided."
        }
        ```
        
    - **On Failure (Book Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Book with ID 4 does not exist."
        }
        ```
---




### D1. BOOK-AUTHOR CREATE
- **Endpoint:** `/book_authors/create`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint creates a new book-author relationship by linking a specific book and author based on bookid and authorid. The request is protected by JWT authentication, and a new JWT token will be returned upon successful execution.

- **Authentication:**  
  A valid JWT token is required for access. The token must be passed in the Authorization header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "bookid": "1",
        "authorid": "1"
    }
    ```

- **Response:**
    - **On Success (Book-Author Relationship Created):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book-author relationship created successfully"
        }
        ```

    - **On Failure (Book Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Book does not exist"
        }
        ```

    - **On Failure (Author Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Author does not exist"
        }
        ```

    - **On Failure (Foreign Key Constraint Violation):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "Foreign key constraint violation",
                "details": "Error message from the database"
            }
        }
        ```
---
### D2. BOOK-AUTHOR VIEW
- **Endpoint:** `/book_authors/read`  
- **Method:** `GET`  
- **Description:**  
  This endpoint retrieves all book-author relationships from the database, along with the details of the books and authors involved in each relationship.

- **Authentication:**  
  A valid JWT token is required for access. The token must be passed in the Authorization header as a Bearer token.
  
- **Sample Request:**
    - **No request body is required. Authentication is handled via the Authorization header.**

- **Response:**
    - **On Success (Book-Author Relationships Found):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "collectionid": "2",
                    "bookid": "3",
                    "authorid": "3",
                    "book_title": "Book Title",
                    "author_name": "Author Name"
                },
                {
                    "collectionid": "5",
                    "bookid": "2",
                    "authorid": "2",
                    "book_title": "Another Book",
                    "author_name": "Another Author"
                }
            ]
        }
        ```

    - **On Failure (No Book-Author Relationships Found):**
        ```json
        {
            "status": "fail",
            "data": "No book-author relationships found"
        }
        ```
---
### D3. BOOK-AUTHOR UPDATE
- **Endpoint:** `/book_authors/update`  
- **Method:** `PUT`  
- **Description:**  
  This endpoint allows updating an existing book-author relationship by specifying the collectionid, bookid, and authorid. The relationship for the given collectionid will be updated accordingly.

- **Authentication:**  
  A valid JWT token is required for access. The token must be passed in the Authorization header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "collectionid": "1",
        "bookid": "1",
        "authorid": "1"
    }
    ```

- **Response:**
    - **On Success (Book-Author Relationship Updated):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book-author relationship updated successfully"
        }
        ```

    - **On Failure (Book Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Book does not exist"
        }
        ```

    - **On Failure (Author Not Found):**
        ```json
        {
            "status": "fail",
            "data": "Author does not exist"
        }
        ```

    - **On Failure (Foreign Key Constraint Violation):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "Foreign key constraint violation",
                "details": "<Error_Message>"
            }
        }
        ```
---
### D4. BOOK-AUTHOR DELETE
- **Endpoint:** `/book_authors/delete`  
- **Method:** `DELETE`  
- **Description:**  
  This endpoint deletes an existing book-author relationship identified by the collectionid. Once deleted, the book-author relationship is removed from the database.

- **Authentication:**  
  A valid JWT token is required for access. The token must be passed in the Authorization header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "collectionid": "1"
    }
    ```

- **Response:**
    - **On Success (Book-Author Relationship Deleted):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book-author relationship deleted successfully"
        }
        ```

    - **On Failure (Database Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```
---
