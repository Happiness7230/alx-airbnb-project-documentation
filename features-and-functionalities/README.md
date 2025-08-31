How the Schema Supports Backend Features
1. User Management & Authentication
Users Table: This is the central table for user data. The role column, defined by the user_role enum, clearly distinguishes between guests, hosts, and admins, satisfying the need for Role-Based Access Control (RBAC).

Authentication: The password_hash column is for secure password storage. It's nullable to accommodate OAuth logins (e.g., Google, Facebook), where a password isn't required.

Profile Management: Fields like full_name, email, and profile_photo_url allow users to manage their profiles.

2. Property Management
Properties Table: A host (linked via host_id) can create a listing with all necessary details like title, description, location, price, and guest capacity.

Photos: The one-to-many relationship between Properties and Property_Photos allows hosts to upload multiple images for a single listing, with URLs pointing to a cloud storage service like AWS S3.

Amenities & Filtering: The many-to-many relationship using the Property_Amenities join table allows for a flexible and efficient search system. Users can filter properties by amenities like "Wi-Fi" or "Pool".

3. Booking System
Bookings Table: This table connects a guest_id to a property_id for specific check_in_date and check_out_date.

Preventing Double Bookings: The backend logic can query this table for overlapping dates for a specific property_id before confirming a new booking, ensuring a property cannot be booked twice for the same period.

Booking Status: The status column (pending, confirmed, cancelled, completed) allows for easy tracking and management of the entire booking lifecycle.

4. Payments
Payments Table: It has a one-to-one relationship with the Bookings table (booking_id is unique). This design ensures every booking has a corresponding payment record.

Secure Integration: The gateway_charge_id stores the unique transaction identifier from a payment gateway like Stripe or PayPal, linking our system to the external service for reconciliation and refunds.

Currency Support: The currency field enables the system to handle multi-currency transactions.

5. Reviews and Ratings
Reviews Table: By linking a review directly to a booking_id, the system ensures that only guests who have completed a booking can leave a review, preventing abuse.

Host Responses: The host_response field allows hosts to engage with guest feedback directly.

6. Notifications
Notifications Table: This simple table can power both in-app and email notifications. The backend can create a new row here whenever a key event occurs (e.g., booking confirmation, payment success), and a separate service can process these records to send alerts.

7. Technical & Non-Functional Requirements
Scalability: The schema is normalized, reducing data redundancy and improving performance. Using uuid for primary keys is excellent for distributed systems.

Performance: Indexes are defined on foreign keys and frequently queried columns (e.g., Properties.city, Properties.price_per_night) to speed up search and data retrieval operations.

Data Integrity: The use of foreign key constraints (ref), not null fields, and unique constraints ensures that the data in the database remains consistent and reliable.