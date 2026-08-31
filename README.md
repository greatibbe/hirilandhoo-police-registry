# Hirilandhoo Police Registry v3

Unified Firebase web app for:
- Expat Registry
- Vehicle Registry
- Vessel Registry

## Included
- One shared login
- Officer/Admin dashboard
- Expat registry
- Vehicle registry with owner information
- Vessel registry with owner information
- Owner ID card camera/upload capture
- Optional browser-side OCR assist using Tesseract.js for owner ID images
- Annual fee number + expiry
- Insurance number + expiry
- Roadworthiness sticker number + expiry
- Expiry alerts on dashboard and inside vehicle/vessel registries
- Photo/document upload to Firebase Storage
- Printable individual records
- CSV export
- Audit log
- Firebase Authentication, Firestore, Storage
- GitHub/Firebase Hosting ready

## Firebase collections
- users/{uid}
- usernames/{username}
- registries/expats/records/{recordId}
- registries/vehicles/records/{recordId}
- registries/vessels/records/{recordId}
- auditLogs/{logId}

## Storage layout
- expats/{recordId}/...
- vehicles/{recordId}/...
- vessels/{recordId}/...

## Owner ID scan
The app can capture/upload an owner ID card image and run OCR in the browser using Tesseract.js.
OCR is an assistive step only. Officers should verify the extracted owner name and ID number against the original card before saving.

## Setup
1. Create a Firebase project.
2. Enable Email/Password Authentication.
3. Enable Firestore and Storage.
4. Paste Firebase Web App config into firebase-config.js.
5. Publish firestore.rules and storage.rules.
6. Add approved officer/admin profiles under users/{uid}.
7. Add username mappings under usernames/{username}.
8. Deploy to Firebase Hosting.

For operational police use, configure strong account controls, device security, backups, App Check, access reviews, and appropriate retention/privacy rules.


## User registration method (same method as the provided Dafthar ZIP)

1. On the login screen choose **Request access**.
2. Enter officer name, service number, real recovery email and password.
3. The app generates the username from initials + service number.
   Example: `Mohamed Ali` + `4655` -> `ma_4655`.
4. Firebase Authentication creates the account using the officer's real email.
5. Firestore creates `users/{uid}` with `status: pending` and `role: user`.
6. Firestore creates `usernames/{generated_username}` containing the recovery email.
7. The officer sees **Waiting for approval**.
8. An approved administrator opens the **Users** tab and approves or rejects the request.
9. After approval the officer gets access automatically.
10. Login uses the generated username, while Firebase still authenticates the real email behind the username mapping.

Password reset also follows the provided ZIP method:
- user submits a reset request using username
- admin opens **Resets**
- admin sends the Firebase password-reset email to the registered recovery email
