Student Management System (JDBC Project)

This project is a Java/JSP student management system using JDBC and MySQL. It includes an admin module to manage student records.

Features:

* Admin panel
* Admin login & logout
* Insert student details
* Update student details
* Delete student records
* Retrieve student details
* Database connection setup

Technologies Used:

* Java
* JDBC
* Servlets
* JSP
* JSTL
* HTML
* Bootstrap
* MySQL

## Database setup

1. Run `data/init.sql` against your local MySQL server. It creates the `SalesForce` database, the required tables, and a starter admin account.
2. Set `SMS_DB_PASSWORD` to the MySQL password before starting Tomcat. Optionally set `SMS_DB_USERNAME` (defaults to `root`) and `SMS_DB_URL` (defaults to `jdbc:mysql://127.0.0.1:3306/SalesForce?useSSL=false&serverTimezone=UTC`).
3. Deploy the web application to Tomcat 9 and open the application in a browser.

The starter login is `admin@gmail.com` / `admin123`. Change this password before using the app beyond local development.

## Subjects, marks, and leaderboard

Use the **Subjects & Marks** page to add subjects and enter or update a student's marks. Marks are out of 100; 33 or higher passes. The leaderboard ranks students by the average of their recorded subject marks, with tied averages sharing a rank. The `subjects` and `student_marks` tables are included in `data/init.sql`.

## Student portal, complaints, and parent calls

For an existing database, run `data/upgrade-student-portal.sql` once before deployment. New databases get the same tables and columns from `data/init.sql`. Existing student accounts have no password hash; an admin must edit each account and set a temporary password before that student can sign in.

Admins set a temporary password when adding student. Students sign in from the second option on the landing page and must change the temporary password on first login. Passwords are stored as PBKDF2 hashes. Students can view only their own subject report, submit subject-specific mark discrepancy complaints, follow admin responses, save a parent phone number in international E.164 format, and view the leaderboard. Admins can review and respond to complaints.

For an existing database, run `data/upgrade-scheduled-parent-calls.sql` to add scheduled-call storage. New databases get this table from `data/init.sql`.

For parent calls, configure `TWILIO_ACCOUNT_SID`, `TWILIO_AUTH_TOKEN`, `TWILIO_FROM_NUMBER`, and `TWILIO_VOICE_URL` in the Tomcat process environment. The voice URL must point to a Twilio-accessible TwiML endpoint with the call message. Admins can call a parent immediately or schedule an automatic call for a failed subject. Scheduled times are entered and displayed in Indian Standard Time (Asia/Kolkata); pending calls can be cancelled, and Tomcat checks for due calls every 15 seconds. Calls are recorded per failed mark to prevent duplicates; if a request outcome is uncertain, check the Twilio console before taking further action. Twilio credentials are not stored in the repository.

## Deploy to Render

This repository includes a Dockerfile and Render Blueprint for Tomcat 9. The Blueprint creates a free web service in Render's Singapore region. Free services can sleep when idle.

The app requires a MySQL server that is reachable from Render; the local `127.0.0.1` database in `.env` cannot be used by a cloud deployment. Create a `SalesForce` database on an external MySQL provider, run `data/init.sql` against it, and enter its JDBC URL, username, and password when Render prompts for `SMS_DB_URL`, `SMS_DB_USERNAME`, and `SMS_DB_PASSWORD`. Keep these values in Render's environment settings and out of GitHub. Render does not provide managed MySQL.

To start deployment, connect this GitHub repository in Render and apply its `render.yaml` Blueprint. Parent-call features also need the four Twilio environment variables described above; they can be added in Render after deployment.
