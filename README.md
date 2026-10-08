# COMP 440 Course Project, Phase 1

Java + Swing + MySQL. User registration and login with SQL injection prevention
and hashed passwords.

**YouTube demo:** _paste your video URL here before submitting_

**Team number:** _fill in, and rename the zip to COMP440_TeamNo_P1.zip_

## What is here

```
sql/schema.sql                       database, user table, least privilege account
db.properties                        JDBC settings, edit these
src/p1/App.java                      entry point
src/p1/db/Database.java              connection factory
src/p1/model/User.java               user row
src/p1/security/PasswordHasher.java  PBKDF2 hashing and verification
src/p1/security/InputValidator.java  format rules for signup
src/p1/dao/UserDao.java              all SQL, prepared statements only
src/p1/dao/DuplicateFieldException.java
src/p1/ui/LoginFrame.java            login window
src/p1/ui/SignupDialog.java          registration form
src/p1/ui/HomeFrame.java             post login window
```

## Setup

1. Download the MySQL Connector/J jar (mysql-connector-j-x.x.x.jar) and put it in
   a `lib/` folder in this directory.
2. Open `sql/schema.sql` in MySQL Workbench and run it. It creates the
   `comp440_p1` database, the `user` table, and a limited application account.
3. Change the password in `schema.sql` and in `db.properties` so they match and
   are not the placeholder.

## Build and run

Linux or macOS:

```
javac -d bin $(find src -name "*.java")
java -cp "bin:lib/*" p1.App
```

Windows:

```
javac -d bin src\p1\*.java src\p1\db\*.java src\p1\model\*.java src\p1\security\*.java src\p1\dao\*.java src\p1\ui\*.java
java -cp "bin;lib/*" p1.App
```

Run from this directory so `db.properties` is found.

## How the two security requirements are met

**SQL injection.** Every statement in `UserDao` is a `PreparedStatement` with `?`
placeholders. No user value is ever concatenated into SQL text. The driver sends
the parameter separately from the query, so the database parses the query once
and treats the input purely as data. `InputValidator` is a second layer that
rejects malformed input early; it is not what stops the attack.

**Hashed passwords.** `PasswordHasher` uses PBKDF2 with HMAC SHA256, 210,000
iterations, and a fresh 16 byte random salt per user. The stored column holds
`pbkdf2_sha256$iterations$salt$hash`, roughly 90 characters. The plaintext
password never reaches the database and never appears in a query. Login loads
the stored hash by username and recomputes, so no password comparison happens in
SQL.

Two smaller touches worth mentioning in the demo: uniqueness is enforced by the
database constraints rather than a SELECT then INSERT, which removes the race
condition between two simultaneous signups; and login returns the same message
for an unknown username and a wrong password, with matching timing, so the form
cannot be used to discover which usernames exist.

## Demo script for the video

1. Show `schema.sql` and the created table in Workbench.
2. Sign up a normal user. Show the row appearing in Workbench with an unreadable
   password column.
3. Sign up a second user with the same username. Show the failure. Repeat with a
   duplicate email, then a duplicate phone.
4. Try a signup with mismatched password confirmation. Show the failure.
5. Log in with the correct password. Show the home screen.
6. Log in with the wrong password. Show the failure.
7. The injection attempts. Type each of these and show that login still fails:

   | Field    | Input                  |
   |----------|------------------------|
   | Password | `any' OR '1'='1`       |
   | Username | `admin'--`             |
   | Username | `foo'; DROP TABLE user;--` |

   After the last one, refresh the table in Workbench to show it is still there.
   Explain that the input was bound as a parameter, so it was searched for as a
   literal username and never parsed as SQL.
8. Optionally show that the application account cannot drop the table even if a
   statement did get through.

## Submission checklist

- [ ] YouTube URL pasted at the top of this file
- [ ] Team number filled in
- [ ] `db.properties` password changed from the placeholder
- [ ] Zip named `COMP440_TeamNo_P1.zip` with your team number
- [ ] Zip includes `src/`, `sql/`, `lib/`, `db.properties`, and this README
