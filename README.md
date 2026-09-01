Hirilandhoo Police Registry v3
Unified Firebase web app for:
Expat Registry
Vehicle Registry
Vessel Registry
Included
One shared login
Officer/Admin dashboard
Expat registry
Vehicle registry with owner information
Vessel registry with owner information
Owner ID card camera/upload capture
Optional browser-side OCR assist using Tesseract.js for owner ID images
Annual fee number + expiry
Insurance number + expiry
Roadworthiness sticker number + expiry
Expiry alerts on dashboard and inside vehicle/vessel registries
Photo/document upload to Firebase Storage
Printable individual records
CSV export
Audit log
Firebase Authentication, Firestore, Storage
GitHub/Firebase Hosting ready
Firebase collections
users/{uid}
usernames/{username}
registries/expats/records/{recordId}
registries/vehicles/records/{recordId}
registries/vessels/records/{recordId}
auditLogs/{logId}
Storage layout
expats/{recordId}/...
vehicles/{recordId}/...
vessels/{recordId}/...
Owner ID scan
The app can capture/upload an owner ID card image and run OCR in the browser using Tesseract.js.
OCR is an assistive step only. Officers should verify the extracted owner name and ID number against the original card before saving.
Setup
Create a Firebase project.
Enable Email/Password Authentication.
Enable Firestore and Storage.
Paste Firebase Web App config into firebase-config.js.
Publish firestore.rules and storage.rules.
Add approved officer/admin profiles under users/{uid}.
Add username mappings under usernames/{username}.
Deploy to Firebase Hosting.
For operational police use, configure strong account controls, device security, backups, App Check, access reviews, and appropriate retention/privacy rules.

User registration method (same method as the provided Dafthar ZIP)
On the login screen choose Request access.
Enter officer name, service number, real recovery email and password.
The app generates the username from initials + service number.
Example: `Mohamed Ali` + `4655` -> `ma_4655`.
Firebase Authentication creates the account using the officer's real email.
Firestore creates `users/{uid}` with `status: pending` and `role: user`.
Firestore creates `usernames/{generated_username}` containing the recovery email.
The officer sees Waiting for approval.
An approved administrator opens the Users tab and approves or rejects the request.
After approval the officer gets access automatically.
Login uses the generated username, while Firebase still authenticates the real email behind the username mapping.
Password reset also follows the provided ZIP method:
user submits a reset request using username
admin opens Resets
admin sends the Firebase password-reset email to the registered recovery email
GitHub Pages fix included
This build explicitly assigns Firebase configuration to `window.firebaseConfig`.
After uploading:
Put the real Firebase Web App values into `firebase-config.js`.
Commit to GitHub.
Wait for GitHub Pages deployment.
Hard-refresh the page.
Add `username.github.io` to Firebase Authentication > Settings > Authorized domains.

v5
Dark blue theme and requested Expat, Vehicle, Vessel field changes. Demo data seeds once when each collection is empty and an admin signs in.

Version 6 - Delete Controls
What changed
Individual delete
Every approved officer can now delete a single registry record.
A Delete button appears:
beside each Expat record
beside each Vehicle record
beside each Vessel record
inside the record detail/printable view
The app asks for confirmation before deletion.
When a record is deleted, the app also attempts to remove files stored under that record's Firebase Storage folder.
The audit log records the action as:
`DELETE`
Bulk delete - Admin only
Administrators see:
a checkbox beside each visible registry record
Select all visible
Delete selected
Normal officers do not see these bulk controls.
Search and filters apply before bulk selection. Therefore, Select all visible selects only records currently displayed by the active search/filter.
Each record deleted through bulk delete is logged as:
`BULK_DELETE`
Important Firebase change
`firestore.rules` changed because ordinary approved officers now need permission to perform one-record deletes.
The registry rule is:
```text
match /registries/{registry}/records/{recordId} {
  allow read, create, update, delete: if approvedUser();
}
```
Publish the new Firestore Rules
Because the website is hosted on GitHub Pages, uploading `firestore.rules` to GitHub does NOT automatically change Firebase.
After updating GitHub:
Open Firebase Console.
Open Firestore Database.
Open Rules.
Copy the complete `firestore.rules` file from this project.
Paste it into Firebase.
Click Publish.
Without this step, individual Delete may show in the web app but Firebase can reject the operation.
How to change delete permissions later
Make single delete Admin only
Change Firestore rules to:
```text
match /registries/{registry}/records/{recordId} {
  allow read, create, update: if approvedUser();
  allow delete: if approvedAdmin();
}
```
Then in `index.html`, search:
```text
data-d=
```
That is the one-record Delete button.
You can wrap/show that button only when:
```javascript
profile?.role === "admin"
```
Remove bulk delete
In `index.html`, search for:
```text
bulkDeleteRecords
```
and:
```text
bulkbar
```
These contain the bulk-delete function and interface.
Change Delete confirmation text
Search `index.html` for:
```text
Delete this
```
for the single-delete message.
Search for:
```text
Permanently delete
```
for the bulk-delete message.
GitHub Pages update guide
Extract this ZIP.
Preserve your real Firebase values in `firebase-config.js`.
Upload/replace the project files in the GitHub repository.
Commit the changes to your GitHub Pages branch, normally `main`.
Wait for Pages build and deployment to finish.
Open the live site in an Incognito/InPrivate tab or hard refresh.
Publish the new Firestore Rules separately in Firebase Console.
Files you normally change
`index.html` - interface, forms, field names, demo records and app logic
`firebase-config.js` - Firebase project connection
`firestore.rules` - database access permissions
`storage.rules` - uploaded image/document access permissions
`README.md` - setup and change guide
`firebase.json` - Firebase Hosting configuration if Firebase Hosting is used later
Changing the application color
The app colors are defined near the top of `index.html` inside:
```css
:root { ... }
```
The main dark-blue values are controlled mainly by:
```css
--g
--g2
--blue
--bg
--card
--line
```
Changing fields
Search `index.html` for:
```javascript
const schemas={
```
Inside are:
`expats`
`vehicles`
`vessels`
Fields can be added or removed from their `sections` arrays.
After changing fields, test:
New record
Edit
Search
Print
CSV export
Alerts
Demo data
Search `index.html` for:
```javascript
seedDemoData
```
This is where the demo Expat, Vehicle and Vessel entries are defined.
Demo records are inserted only when:
the signed-in user is an Admin, and
that registry collection is empty.

Version 7 - Separate Registry Theme Colors
Each registry now has its own visual theme:
Expat Registry: Dark Green
Vehicle Registry: Dark Blue
Vessel Registry: Greeny-Blue / Teal
Home, Users, Resets and Audit screens: default Dark Blue
The theme changes automatically when an officer opens a registry.
Current theme values
Expat - Dark Green
```css
body.theme-expats {
  --g: #14532d;
  --g2: #0b351d;
  --blue: #1f6a3c;
  --bg: #eef6f0;
  --line: #c8d9cd;
}
```
Vehicle - Dark Blue
```css
body.theme-vehicles {
  --g: #0b2f5b;
  --g2: #061d3b;
  --blue: #164f86;
  --bg: #eef3f9;
  --line: #cbd6e3;
}
```
Vessel - Greeny-Blue / Teal
```css
body.theme-vessels {
  --g: #0b5b5b;
  --g2: #063b3e;
  --blue: #117a7a;
  --bg: #edf7f6;
  --line: #c4ddda;
}
```
How to change registry colors later
Open `index.html` and search for:
```text
Module-specific themes
```
Then edit the values under:
`body.theme-expats`
`body.theme-vehicles`
`body.theme-vessels`
`--g` is the main theme color.
`--g2` is the darker shade used for dark areas/gradients.
`--blue` controls accent elements.
`--bg` controls the page background.
`--line` controls borders.
GitHub update
Extract this ZIP.
Keep/copy your real Firebase configuration into `firebase-config.js`.
Replace the files in your GitHub repository.
Commit to `main`.
Wait for GitHub Pages deployment.
Hard refresh with `Ctrl + F5` or test in an Incognito window.
No new Firestore or Storage permission changes were introduced specifically for the color update.

Version 8 - Admin User Monitoring and Role Management
New Admin features
Online / offline status
Last seen time
Current module
Current activity (Viewing, Entering data, Editing record)
Total records created by each user
Entries created today
Pending access requests
Change application role directly inside User Monitor
Roles
Admin
Full access, bulk delete, user approval/rejection, password reset administration, User Monitor, role changes and Audit Log.
Officer
Can add, edit and delete individual records. Can search, view, print and export. No bulk delete or user administration.
Viewer
Read-only access. Can search, view, print and export. Cannot add, edit or delete.
Existing users with the old `user` role are treated as Officers.
Online status
The app writes these fields to `users/{uid}`:
`online`
`lastSeen`
`currentModule`
`activity`
`sessionStartedAt`
A user is considered online when `lastSeen` is within about 2 minutes. The app refreshes presence about every 60 seconds.
Data entry tracking
New record creation increments:
`entriesTotal`
`entriesToday`
`entriesTodayDate`
These counters begin from v8. Old records are not retroactively assigned to users.
Changing a role
Admin → User Monitor → Role dropdown → choose Viewer, Officer or Admin.
The currently signed-in administrator cannot remove their own Admin role from this screen.
Required Firebase Rules update
This version changes `firestore.rules`.
After updating GitHub Pages:
Open Firebase Console.
Firestore Database → Rules.
Paste the included `firestore.rules`.
Click Publish.
Viewer is read-only. Officer/Admin can create, update and delete registry records. Users can update only their own presence/activity/stat fields. Admin can manage full user profiles and roles.
Change online timeout
Open `index.html` and search for `function isOnlineProfile`.
Current value: `120000` milliseconds = 2 minutes.
For 5 minutes use `300000`.
Change heartbeat frequency
Search for `setInterval` near `startPresence`.
Current value: `60000` milliseconds = 60 seconds.
GitHub Pages deployment
Extract this ZIP.
Preserve/copy your real Firebase values into `firebase-config.js`.
Replace files in your GitHub repository.
Commit to `main`.
Wait for Pages deployment.
Hard refresh the website.
Publish the new Firestore rules manually in Firebase Console.
Files to edit later
`index.html` — interface, fields, roles, monitoring logic
`firebase-config.js` — Firebase connection
`firestore.rules` — database permissions
`storage.rules` — document/photo permissions
`README.md` — setup/change guide

Version 9 - Separate Upload and Camera Options
Every photo/document field now shows two separate choices:
Upload File
Opens the phone/computer file picker.
Supports images and PDF files for normal document fields.
Take Photo / Scan
Opens the device camera where supported.
Uses the rear camera through `capture="environment"`.
Intended for photographing ID cards, passports, work permit cards, registration papers, vehicle photos and vessel photos.
OCR scan fields
The Expat passport/work permit reader now has:
Upload Image
Take Photo / Scan
Read Document
The Owner ID reader now has:
Upload Image
Take Photo / Scan
Read ID
OCR is only an assistance feature. Officers must verify extracted details against the original document before saving.
How to change accepted file types
Open `index.html` and search for:
`accept="image/*,application/pdf"`
To allow images only, change it to:
`accept="image/*"`
How to change the camera
Search for:
`capture="environment"`
`environment` normally requests the rear camera.
To request the front camera, use:
`capture="user"`
How to rename the options
Search in `index.html` for:
`Upload File`
`Upload Image`
`Take Photo / Scan`
Change the text as required.
Deployment
Extract this ZIP.
Keep/copy your real Firebase settings into `firebase-config.js`.
Replace the files in your GitHub repository.
Commit to the `main` branch.
Wait for GitHub Pages deployment.
Hard refresh or test in a private/incognito browser.
Keep the previously published v8 Firestore rules, unless you have not yet published them.
No new Firestore or Storage permission change is required specifically for this upload/camera update.

Version 10 - Desktop Google Chrome Optimization
This version improves usability on desktop/laptop Google Chrome while preserving the mobile layout.
Desktop improvements
Wider application layout
Sticky navigation
Three-column forms on large screens
Better button and field spacing
Wider modals for desktop
Horizontal scrolling for wide registry rows
Improved document/photo card layout
Better handling for User Monitor, Resets and Audit screens
Desktop guidance for Upload File vs Take Photo / Scan
Recommended Chrome setup
Use the latest Google Chrome.
Use 100% browser zoom normally.
On smaller laptops, 90% zoom may be more comfortable.
Maximize the browser window.
Camera behavior on desktop
The HTML `capture="environment"` option is mainly designed for mobile browsers.
On desktop Chrome, Take Photo / Scan may open the normal file picker instead of a webcam capture screen.
Recommended:
Desktop/laptop: use Upload File
iPhone/Android/tablet: use Take Photo / Scan
If the layout looks too large
Press `Ctrl + 0` to reset Chrome zoom.
Maximize Chrome.
Press `Ctrl + F5` to hard refresh.
If needed, use `Ctrl + -` once for 90% zoom.
If columns are cut off
Registry rows can scroll horizontally inside the list area.
Use a trackpad horizontal swipe or the horizontal scrollbar.
If an old version keeps loading
Open the site in an Incognito window.
Press `Ctrl + F5`.
Check GitHub Actions → Pages build and deployment.
Confirm the latest deployment has a green check.
Confirm Pages publishes from `main` and `/(root)`.
How to change desktop form columns
Open `index.html` and search for:
```css
.grid{grid-template-columns:repeat(3,minmax(0,1fr))}
```
For two columns:
```css
.grid{grid-template-columns:repeat(2,minmax(0,1fr))}
```
How to change maximum desktop width
Search:
```css
.wrap{max-width:1500px
```
Change `1500px` to your preferred width.
Deployment
Extract this ZIP.
Keep/copy your real Firebase settings into `firebase-config.js`.
Replace the files in your GitHub repository.
Commit to `main`.
Wait for GitHub Pages deployment.
Hard refresh with `Ctrl + F5`.
No new Firestore or Storage rule changes are required specifically for this desktop-layout update.

Version 11 - Officer Activity, Role Assignment, Passport/Permit Reader and Expat Interactions
Admin User Monitor
Administrators can now explicitly assign a user role using:
User Monitor → choose role → Assign Role
Available roles:
Viewer
Officer
Admin
The role change is recorded in the Officer Activity Log.
Each user also has an Activities button. It opens the activity log filtered toward that officer.
Officer activity monitoring
The old Audit Log is expanded into an Officer Activity Log.
Tracked actions include:
module opened
record viewed
record printed
new-record form opened
edit form opened
record created
record updated
record deleted
bulk delete
role assignment/change
Expat police interaction
The Admin can filter by:
officer/search text
module
action type
The dashboard also shows:
activities shown
number of officers
number of modules
record-changing actions
Activity records include officer name, username/service number where available, module, action, record name and time.
Expat changes
Sex
`Gender` is renamed to Sex.
Date of birth
The calendar control was removed.
It is now a text field with format:
`YYYY-MM-DD`
The value can be typed manually or populated by passport/work-permit reading.
Passport expiry
The calendar control was removed.
It is now entered as:
`YYYY-MM-DD`
or populated from the passport MRZ.
Passport reader
The reader now attempts to recognize a standard passport MRZ.
For a clear passport bio-page image it can populate:
Full Name
Passport number
Nationality
Date of birth
Sex
Passport expiry
For best results:
Capture the complete passport bio page.
Make sure both MRZ lines at the bottom are visible.
Avoid glare.
Keep the passport flat.
Use sharp focus.
Verify every extracted value against the original passport.
Work permit reader
For a work permit card, OCR now attempts to recognize labelled fields such as:
Work permit number
Name
Nationality
Date of birth
Expiry / valid-until date
Passport number
Because work-permit layouts can vary, the app also keeps the recognized document text visible in the form. Officers should compare it with the physical document and correct fields before saving.
OCR is an assistance feature, not a substitute for document verification.
Police interactions with Expats
Open an Expat record and use:
Log Police Interaction
The officer records:
interaction type
location
notes
The application automatically records:
officer ID
officer name
username
service number
date/time
The Expat detail screen displays:
full interaction history
total number of interactions
number of interactions by each officer
Administrators can also find these in the Officer Activity Log using action:
`EXPAT_INTERACTION`
Important Firestore Rules update
Version 11 adds the nested collection:
`registries/expats/records/{recordId}/interactions`
You MUST publish the included `firestore.rules`.
Firebase Console:
Firestore Database
Rules
Replace with this project's `firestore.rules`
Publish
Approved Officers/Admins can create interaction logs.
Approved users can read them.
Only Admin can update/delete an interaction log.
How to change interaction types
Open `index.html` and search for:
`interactionType`
Edit the `<option>` values.
How to change passport nationality mapping
Search:
`nationalityMap`
Add or change ISO 3-letter country codes and display names.
Example:
```javascript
AUS:"Australian"
```
How to change activity retention
The Admin Activity screen currently loads the latest 500 audit records.
Search:
```javascript
limit(500)
```
Change the number as required.
For a production system with a large audit history, consider a separate reporting/archive process instead of loading an unlimited history in the browser.
Deployment
Extract the ZIP.
Preserve your real Firebase settings in `firebase-config.js`.
Replace the GitHub repository files.
Commit to `main`.
Wait for GitHub Pages deployment.
Hard refresh the browser.
Publish the new `firestore.rules` in Firebase Console.

Version 12 - Password Reset Request Cleanup
After an Admin approves a password-reset request and Firebase sends the reset email, the request is now deleted from the `password_reset_requests` collection.
The workflow is now:
User submits reset request.
Admin sees it under Resets.
Admin sends the reset email.
The action is recorded in the Officer Activity / Audit Log as `PASSWORD_RESET_APPROVED`.
The original reset request is deleted automatically.
This keeps the Reset screen limited to outstanding requests only.
How to change this later
Open `index.html` and search for:
`PASSWORD_RESET_APPROVED`
The request is removed using:
```javascript
await db.collection("password_reset_requests")
  .doc(row.dataset.reset)
  .delete();
```
To keep completed requests instead, replace the delete with an update such as:
```javascript
.update({status:"sent"})
```
Deployment
Extract this ZIP.
Preserve your real Firebase values in `firebase-config.js`.
Replace the files in your GitHub repository.
Commit to `main`.
Wait for GitHub Pages deployment.
Hard refresh with `Ctrl + F5`.
No new Firebase Rules change is required if the v11 Firestore rules are already published, because Admin already has permission to delete reset-request documents.

Version 13 - Role Assignment Fix, Mobile User Monitor and Dark Mode
Role assignment fixed
The Admin User Monitor now uses an explicit role workflow:
Open User Monitor.
Find the user.
Select:
Viewer
Officer
Admin
Tap/click Assign.
Confirm the change.
The app writes the new role to the user's Firestore profile.
A status message appears under the role control after saving.
The current signed-in administrator cannot change their own Admin role from this screen. This prevents accidental admin lockout.
Role controls on mobile
The Role section is no longer hidden on small screens.
On phones:
each user becomes a stacked card
the role dropdown is visible
the Assign button is visible
approve/reject/revoke controls remain visible
non-essential Current Activity and Data Entry columns may still be hidden to keep the card usable
If role assignment still fails
Check:
You are signed in with a Firestore user profile whose role is exactly:
`admin`
The user profile status is:
`approved`
The latest `firestore.rules` is published in Firebase Console.
The existing rules must allow Admin to update documents under:
`users/{userId}`
After changing Firebase projects, remember to publish the rules again in the NEW Firebase project.
Dark Mode
A new button appears in the application header:
`🌙 Dark`
`☀️ Light`
The selected mode is saved in the browser using `localStorage`.
Each registry still keeps its own accent color:
Expat: dark green
Vehicle: dark blue
Vessel: greeny-blue / teal
Dark Mode changes the page/card/input surfaces while preserving those module accents.
How to change Dark Mode colors
Open `index.html`.
Search:
```css
body.dark-mode
```
Important variables include:
```css
--bg
--card
--line
--muted
```
You can change these values to make Dark Mode lighter or darker.
How to make Dark Mode the default
Search:
```javascript
function initDarkMode()
```
Currently the browser uses the user's saved choice.
You can change the initial value if you want all first-time users to start in Dark Mode.
Deployment
Extract this ZIP.
Preserve/copy your real Firebase settings into `firebase-config.js`.
Replace files in your GitHub repository.
Commit to `main`.
Wait for GitHub Pages deployment.
Hard refresh using `Ctrl + F5`.
If you recently changed Firebase projects, publish the included `firestore.rules` and `storage.rules` in that new Firebase project.

Version 14 - Role Assignment Diagnostic Fix
This version changes role assignment so the app tells you exactly why it fails.
New Admin diagnostic
Open User Monitor.
At the top you will now see one of:
`✓ Admin permissions detected`
or
`⚠ Admin permissions NOT detected`
It also displays:
Firebase Auth UID
Firestore status
Firestore role
For role assignment to work it MUST show:
`status: approved`
and:
`role: admin`
Role assignment verification
When Admin taps Assign the app now:
checks that the current account is an approved Admin
checks that the target Firestore user document exists
writes the new role
reads the document back from Firestore
verifies that the new role was actually saved
records the change in the Audit Log
If Firebase rejects the update, the exact Firebase error code/message is displayed.
Most common errors
permission-denied
Your signed-in account is not recognized by Firestore as an Admin, or the correct rules were not published.
Check:
Firestore → users → YOUR AUTH UID
The document must contain:
```text
status: approved
role: admin
```
Target user profile does not exist
The target user's Firestore document ID is not the same as their Firebase Authentication UID.
Role update did not persist
The update was blocked or another rule/profile process changed the value.
Important Firebase check
In Firebase Console:
Authentication → Users → copy your Admin UID
Then:
Firestore Database → users
Make sure there is a document whose ID is EXACTLY that UID.
Inside it:
```text
status = approved
role = admin
```
Firestore Rules
Publish the included `firestore.rules` in the same Firebase project used by `firebase-config.js`.
If you recently moved to a new Firebase account/project, rules from the old project do not carry over automatically.
Mobile
Role dropdown and Assign button are forced visible on small screens in this version.
Deployment
Extract ZIP.
Keep your current real Firebase values in `firebase-config.js`.
Replace GitHub files.
Commit to main.
Wait for Pages deployment.
Hard refresh.
Open User Monitor and read the new Admin diagnostic line.
