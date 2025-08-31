Backend Requirement Specifications for Airbnb Clone
This document provides detailed technical and functional specifications for key backend features of the Airbnb Clone project, based on the provided DBML schema.

1. Feature: User Authentication System
This feature covers the complete user lifecycle, including registration, login, session management, and authorization.

1.1. Functional Requirements:

FR1.1: User Registration (Email/Password):

The system must allow new users to register by providing a full_name, email, and password.

The email provided must be unique across all users in the Users table.

Passwords must be securely hashed (e.g., using bcrypt) before being stored in the password_hash column.

Upon successful registration, a new record will be created in the Users table with the default role of 'guest'.

FR1.2: User Registration (OAuth 2.0):

The system must support registration and login via third-party OAuth providers (e.g., Google, Facebook).

If a user signs up with OAuth, a new record is created in the Users table using their name and email from the provider. The password_hash field will be NULL.

FR1.3: User Login:

Registered users must be able to log in using their email and password.

The system will validate the provided password against the stored password_hash.

Upon successful login, the system will generate a JSON Web Token (JWT) containing the user_id and role. This token will be sent to the client to manage the session.

FR1.4: Role-Based Access Control (RBAC):

The system must enforce access control based on the role (guest, host, admin) defined in the user_role enum.

Guests: Can search, book, and review properties.

Hosts: Can perform all guest actions plus create, edit, and manage their own property listings.

Admins: Have full access to manage all users, properties, and bookings.

1.2. Non-Functional Requirements:

NFR1.1 (Security): All API endpoints handling sensitive user data must be protected and require a valid JWT.

NFR1.2 (Performance): Login and registration processes should complete in under 500ms under normal load.

2. Feature: Property Management
This feature covers the functionalities for hosts to create, manage, and display their property listings.

2.1. Functional Requirements:

FR2.1: Create Property Listing:

An authenticated user with the role of 'host' must be able to create a new property listing.

The creation process must require the following fields: title, description, address, city, country, price_per_night, and max_guests.

A new record will be created in the Properties table, linked to the host via host_id.

FR2.2: Manage Property Photos:

Hosts must be able to upload multiple images for each property.

Each uploaded image URL will be stored as a new record in the Property_Photos table, linked by property_id.

Hosts must be able to delete photos from their listings.

FR2.3: Manage Amenities:

Hosts must be able to associate their properties with a list of available amenities (e.g., Wi-Fi, Pool).

This is managed by creating entries in the Property_Amenities join table, linking a property_id to an amenity_id.

FR2.4: Edit/Delete Listing:

Hosts must only be able to edit or delete their own property listings. The system will verify that the host_id in the Properties table matches the authenticated user's ID.

2.2. Non-Functional Requirements:

NFR2.1 (Storage): Property images should not be stored in the database. Their URLs should point to a cloud storage solution (e.g., AWS S3, Cloudinary).

NFR2.2 (Performance): Database queries for searching properties should be optimized using the indexes on city, country, and price_per_night.

3. Feature: Booking System
This feature covers the end-to-end process of a guest booking a property, including validation, payment, and status management.

3.1. Functional Requirements:

FR3.1: Booking Creation:

An authenticated guest must be able to submit a booking request for a property by providing check_in_date, check_out_date, and num_guests.

The system must validate that num_guests does not exceed the max_guests for the property.

FR3.2: Double-Booking Prevention:

Before confirming a booking, the system must query the Bookings table to ensure there are no existing confirmed bookings for the same property_id that overlap with the requested dates.

FR3.3: Payment Integration:

Upon successful validation, the system will create a Booking record with a 'pending' status and initiate a payment process.

The system will interface with a third-party payment gateway (e.g., Stripe) to handle the transaction.

A successful transaction will be recorded in the Payments table, linked by the booking_id.

FR3.4: Booking Status Management:

If payment is successful, the booking status in the Bookings table must be updated from 'pending' to 'confirmed'.

If payment fails, the status must be updated to 'cancelled'.

The system should provide functionality for guests or hosts to cancel a booking (updating the status to 'cancelled') and for the system to automatically mark a booking as 'completed' after the check_out_date.

FR3.5: Notifications:

Upon a status change to 'confirmed' or 'cancelled', the system must create a notification for both the guest and the host in the Notifications table.

3.2. Non-Functional Requirements:

NFR3.1 (Reliability): The booking process must be transactional. If the payment fails, the booking must not be confirmed.

NFR3.2 (Security): The system must not store raw payment information (e.g., credit card numbers). It should only store the gateway_charge_id provided by the payment processor.