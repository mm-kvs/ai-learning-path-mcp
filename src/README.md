# Mergington High School Activities API

A super simple FastAPI application that allows students to view and sign up for extracurricular activities.

## Features

- View all available extracurricular activities
- Teacher-only student registration and unregistration
- Teacher login with expiring, HTTP-only sessions

## Getting Started

1. Install the dependencies:

   ```
   pip install fastapi uvicorn
   ```

2. Run the application:

   ```
   python app.py
   ```

3. Open your browser and go to:
   - API documentation: http://localhost:8000/docs
   - Alternative documentation: http://localhost:8000/redoc

## API Endpoints

| Method | Endpoint                                                          | Description                                                         |
| ------ | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| GET    | `/activities`                                                     | Get all activities with their details and current participant count |
| POST   | `/auth/login`                                                      | Log in as a teacher                                                  |
| GET    | `/auth/me`                                                         | Check the current teacher session                                    |
| POST   | `/auth/logout`                                                     | End the current teacher session                                     |
| POST   | `/activities/{activity_name}/signup?email=student@mergington.edu` | Register a student (teachers only)                                   |
| DELETE | `/activities/{activity_name}/unregister?email=student@mergington.edu` | Unregister a student (teachers only)                              |

## Teacher Accounts

Teacher accounts are configured in `teachers.json` next to `app.py`. Passwords are stored as PBKDF2-SHA256 hashes, not as plaintext. Generate a hash with:

```
python -c 'import getpass, hashlib, secrets; salt = secrets.token_hex(16); password = getpass.getpass("Password: "); digest = hashlib.pbkdf2_hmac("sha256", password.encode(), bytes.fromhex(salt), 310000).hex(); print(f"pbkdf2_sha256$310000${salt}${digest}")'
```

Add an entry using the generated value:

```json
{
   "teachers": [
      {
         "username": "teacher",
         "password_hash": "pbkdf2_sha256$310000$SALT$HASH"
      }
   ]
}
```

Use a unique password and keep the credentials file private. Teacher sessions expire after eight hours; student views remain available without logging in.

## Data Model

The application uses a simple data model with meaningful identifiers:

1. **Activities** - Uses activity name as identifier:

   - Description
   - Schedule
   - Maximum number of participants allowed
   - List of student emails who are signed up

2. **Students** - Uses email as identifier:
   - Name
   - Grade level

All data is stored in memory, which means data will be reset when the server restarts.
