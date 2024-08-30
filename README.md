# Backend API with Laravel and MongoDB

## Project Overview
This project is a backend API developed using Laravel and MongoDB. It provides a contact management system where users can upload, create, read, update, and delete contacts.

## Requirements
- PHP 8.2 
- Composer
- Laravel 11.x
- MongoDB
- XAMPP or any other server stack

## Requirements
- PHP 8.2 or higher
- Composer
- Laravel 11.x
- MongoDB

## Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/00Leiner/Assessment/tree/main/api
   cd api
   ```

2. Install dependencies:
   ```bash
   composer install
   ```

3. Add the `jenssegers/mongodb` package:
   ```bash
   composer require jenssegers/mongodb
   ```

4. Create a `.env` file:
   ```bash
   cp .env.example .env
   ```

5. Run migrations (if applicable):
   ```bash
   php artisan migrate
   ```

## Environment Configuration
Edit the `.env` file to set up your database connection. Here’s an example configuration for MongoDB:
```dotenv
DB_CONNECTION=mongodb
DB_DSN=mongodb://127.0.0.1:27017
DB_DATABASE=contacts_db
```

## Database Connection
Ensure you have the following configuration in `config/database.php`:
```php
'mongodb' => [
    'driver' => 'mongodb',
    'dsn' => env('DB_DSN', 'mongodb://127.0.0.1:27017'),
    'database' => env('DB_DATABASE', 'contacts_db'),
],
```

## Models
### Contact Model
The `Contact` model is located in `app/Models/Contact.php`. It extends the `Jenssegers\Mongodb\Eloquent\Model` and defines the `contacts` collection.
```php
namespace App\Models;

use Jenssegers\Mongodb\Eloquent\Model as Eloquent;

class Contact extends Eloquent
{
    protected $collection = 'contacts';
    
    protected $fillable = [
        'name',
        'email',
        'phone',
    ];
    
    public static $rules = [
        'name' => 'required|string|max:255',
        'email' => 'required|email|unique:contacts,email',
        'phone' => 'required|string',
    ];
}
```

## Controllers
### Contact Controller
The `ContactController` handles API requests related to contacts located in `app/Http/Controller/ContactController.php`. It includes methods for uploading, retrieving, updating, and deleting contacts. 

## Routes
Define your API routes in `routes/web.php`:
```php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ContactController;
use Illuminate\Http\Request;

Route::post('/upload', [ContactController::class, 'upload']);
Route::get('/contacts', [ContactController::class, 'index']);
Route::post('/contacts', [ContactController::class, 'store']);
Route::get('/contacts/{id}', [ContactController::class, 'show']);
Route::put('/contacts/{id}', [ContactController::class, 'update']);
Route::delete('/contacts/{id}', [ContactController::class, 'destroy']);
```

## Usage
To start the application, run the following command:
```bash
php artisan serve
```
Access the API at `http://127.0.0.1:8000/`.

### Use Cases
- **Upload Contacts**: Send a POST request to `/upload` with a JSON file containing contact information.
- **Retrieve All Contacts**: Send a GET request to `/contacts`.
- **Create a New Contact**: Send a POST request to `/contacts` with contact details in the request body.
- **Get Contact by ID**: Send a GET request to `/contacts/{id}`.
- **Update Contact**: Send a PUT request to `/contacts/{id}` with updated details.
- **Delete Contact**: Send a DELETE request to `/contacts/{id}`.

## Error Handling
Ensure to handle errors in the controller and return appropriate responses in case of validation or processing errors.





# Contact Management Python Service

This Python service watches a specified directory for new JSON files containing contact information. When a new file is detected, it processes the file to validate, normalize, and store the contact data in a MongoDB database.

## Features

- Watches a designated directory for new contact JSON files.
- Validates contact data according to predefined schemas.
- Normalizes phone numbers to a standard format.
- Ensures email uniqueness before inserting into the database.
- Handles faulty JSON files gracefully.
- Continuously monitors the directory for new files.

## Requirements

- Python 3.x
- MongoDB
- Required Python packages:
  - `pymongo`
  - `watchdog`
  - `jsonschema`
  - `python-dotenv`

## Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/00Leiner/Assessment/tree/main/service
   cd service
   ```

2. Install the required packages:

   ```bash
   pip install pymongo watchdog jsonschema python-dotenv
   ```

3. Create a `.env` file in the root directory and configure your MongoDB connection settings:

   ```plaintext
   DB_CONNECTION=<your_connection_name>
   DB_DSN=<your_mongo_dsn>
   DB_DATABASE=<your_database_name>
   ```

## Code Overview

### Key Functions and Classes

- **`connect_to_mongo()`**: Establishes a connection to the MongoDB database using credentials from the `.env` file.

- **`normalize_phone(phone)`**: Normalizes phone numbers to the format `(XXX) XXX-XXXX` based on specified patterns.

- **`ContactFileHandler`**: Inherits from `FileSystemEventHandler` and handles events related to file creation.
  - **`on_created(event)`**: Triggered when a new file is created in the watched directory. Starts a new thread to process the file.
  - **`process_last_file()`**: Checks for existing files in the directory and processes the most recently modified file.
  - **`process_file(file_path)`**: Reads, validates, and processes contact data from the given JSON file. Inserts valid contacts into the MongoDB database.

### Starting the Service

Run the service by executing the following command in your terminal:

```bash
python service.py
```

The service will start watching the `storage/app/contacts/` directory for new JSON files. 

## JSON File Format

The JSON files should contain an array of contact objects in the following format:

```json
[
  {
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "+1-123-456-7890"
  },
  {
    "name": "Jane Smith",
    "email": "jane@example.com",
    "phone": "987-654-3210"
  }
]
```

### Important Notes

- Ensure that the phone numbers in the JSON files are in the supported formats (`+1-XXX-XXX-XXXX` or `XXX-XXX-XXXX`).
- The service will automatically remove processed files from the directory.


