PHP PDF Upload Service with JWT Authentication
This project provides a simple PHP-based service for uploading PDF files, protected by JSON Web Token (JWT) authentication.

Prerequisites
PHP 7.4+

Composer (for dependency management)

A web server (like Apache or Nginx) with PHP configured

Installation
Clone or download this project.

Navigate to the project directory in your terminal.

Install the required JWT library using Composer:

composer require firebase/php-jwt

Create an uploads directory in the project root and ensure your web server has write permissions for it.

mkdir uploads
chmod -R 775 uploads

Configuration
PHP ini settings
For a 200MB file limit, you must adjust your php.ini file. This is crucial for handling large files.

Find and modify the following directives:

; Maximum allowed size for uploaded files.
upload_max_filesize = 200M

; Maximum size of POST data that PHP will accept.
; This should be slightly larger than upload_max_filesize.
post_max_size = 205M

; Maximum time in seconds a script is allowed to run.
; Increase this to prevent timeouts during large uploads.
max_execution_time = 300

; Maximum time in seconds a script is allowed to parse input data.
; Increase this as well.
max_input_time = 300

After changing these settings, restart your web server (e.g., sudo systemctl restart apache2).

Usage
The service requires two steps:

Generate a JWT Token: Use the generate_token.php script to get a token.

Upload a PDF File: Use the generated token to authorize the upload via upload_pdf.php.

Step 1: Generate a JWT Token
You can access generate_token.php directly in your browser or with cURL. This will return a JSON object containing your token.

Using cURL:

curl -X GET http://localhost/path/to/generate_token.php

You should receive a response like this:

{
    "message": "Token generated successfully.",
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC9leGFtcGxlLm9yZyIsImF1ZCI6Imh0dHA6XC9cL2V4YW1wbGUuY29tIiwic3ViIjoidXNlci0xMjM0NSIsImlhdCI6MTYyMjI2MDc2MywibmJmIjoxNjIyMjYwNzYzfQ.hG722zPz45dG8T4s..."
}

Copy the token value for the next step.

Step 2: Upload a PDF File
To upload a file, you'll send a POST request to upload_pdf.php with the file data and the JWT token in the Authorization header.

Using cURL:

Replace [YOUR_TOKEN] with the token you copied and /path/to/your/file.pdf with the path to the PDF you want to upload.

curl -X POST \
  -H "Authorization: Bearer [YOUR_TOKEN]" \
  -F "pdf_file=@/path/to/your/file.pdf" \
  http://localhost/path/to/upload_pdf.php

Success Response:

{
    "message": "File uploaded successfully.",
    "file_name": "upload_60b00511855e95.74020162.pdf",
    "file_path": "/var/www/html/uploads/upload_60b00511855e95.74020162.pdf"
}

Failure Responses:

Invalid or missing token:

{"message": "Authorization failed: Signature verification failed"}

File too large:

{"message": "File size exceeds the 200MB limit."}

Wrong file type:

{"message": "Invalid file type. Only PDF files are allowed."}
