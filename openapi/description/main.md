This is the tentative API specification for **Busan Science High School Alumni Website, *Make BSS Great Again***.
Note that this project does not have any official relationship between Busan Science High School.
Therefore, if you have any questions/suggestions/complaints, please contact our team with the email or contact information listed above.

# Get Started
All API calls require secured connection (only HTTPS).
To use the APIs, the users need to retrieve access token by signin with username and password (basic authentication).
As of now, we do not support multi-factor authentication, but before we service the application/website MFA will be supported.

Details of the Security Schemes are described below.

# Notes
- Auth API Completed
- User API
  - POST /user Completed
  - GET /user completed
  - GET /user/{username} completed
  - PUT /user/{username} completed
  - PUT /user/primary-email/{username} completed
  - POST /user/verify-email completed
  - PUT /user/verify-email/{ticketID} completed
  - GET /user/find-username completed
  - POST /user/find-username/email completed
  - POST /user/password-reset completed
  - PUT /user/password-reset/{ticketID} completed
  - POST /user/report/{username} completed

# SecuritySchemes
<SecurityDefinitions />