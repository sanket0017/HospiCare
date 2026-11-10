HOSPICARE — FIREBASE CONNECTED DEMONSTRATION
=============================================

OVERVIEW
--------
HospiCare is a student demonstration platform for hospital discovery, doctor discovery, patient appointment booking, token generation, live queue monitoring and role-based dashboards.

RUN
---
1. Extract the ZIP.
2. Open the HospiCare folder in VS Code.
3. Start Live Server from index.html. Do not open pages using file://.
4. Browse the public home, hospitals and doctors pages without signing in.
5. Sign in using the four Firebase Auth accounts created in the HospiCare Firebase project.

FIREBASE ARCHITECTURE
---------------------
Authentication: Firebase Authentication (Email/Password)
Database: Cloud Firestore
Project: hospicare-603cf
Region: asia-south1

Collections used:
- users: one profile document per Firebase Auth UID
- hospitals: hospital directory
- doctors: doctor directory
- doctorAvailability: doctor-controlled weekly availability
- appointments: patient bookings
- queues: doctor/date token counters and current serving token
- slotLocks: unique doctor/date/time reservations to prevent double booking

ROLE PROFILES
-------------
Patient profiles use role='patient'.
Doctor profiles use role='doctor' plus doctorId and hospitalId.
Hospital manager profiles use role='hospital_manager' plus hospitalId.
Staff roles must be assigned by a trusted administrator. Public registration creates patient accounts only.

LIVE BOOKING FLOW
-----------------
1. Patient signs in with Firebase Authentication.
2. Patient chooses a hospital, doctor, date and published time slot.
3. The website reads doctorAvailability and existing slotLocks from Firestore.
4. Booking is written in a Firestore transaction. The transaction checks the availability and slot lock again, creates the appointment and slot lock, and advances the doctor/date queue token.
5. Doctor and manager dashboards listen to Firestore appointment changes.
6. Doctor updates the current serving token in queues/{doctorId}_{YYYY-MM-DD}.
7. The patient queue page listens to that queue document and updates across devices.

ONE-TIME FIREBASE SETUP
-----------------------
See FIREBASE_CROSS_DEVICE_SETUP.txt. Publish firestore.rules.txt only after testing it in the Firebase Rules Playground or Emulator.

DIRECTORY IDS
-------------
The KK Multispeciality Hospital demo uses hospital_001 and doctor_001. Booking links carry these canonical Firestore document IDs; the website does not infer IDs from display names.

LIMITATIONS
-----------
- Payment processing is not connected; revenue remains ₹0.
- Availability is a weekly recurring schedule; holiday and leave management are not included.
- The doctor-specific QR route for Dr. Shrikant Kurhade opens the canonical Firebase booking flow.
- Firebase rules and the end-to-end flow still require testing in the live Firebase project on two separate devices before being presented as fully verified.
- This is a student demonstration project, not a production-ready clinical records system. Do not enter real sensitive medical information.
- Firebase Web API keys are client-side identifiers, not private secrets. Restrict authorized domains in Google Cloud and configure Firebase Authentication authorized domains before deployment.
- FastAPI/PostgreSQL materials, if present, are future work and are not connected to the current website workflow.
