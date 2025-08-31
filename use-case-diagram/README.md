

This database schema outlines a comprehensive backend structure for an Airbnb-style application using PostgreSQL. It is logically organized into four primary functional areas:

User Management: The Users table is the core of this section, designed to store information for all types of users (guests, hosts, and admins), distinguished by a role field. It supports standard email/password registration as well as OAuth integration by allowing the password_hash to be nullable.

Property Management: This is the most detailed section, centered around the Properties table, which holds all the core information for a listing. It links to a Users table to assign a host. To support rich listings, it also includes:

A Property_Photos table for multiple images per property.

A flexible many-to-many relationship between Properties and Amenities via the Property_Amenities join table, allowing for robust search and filtering.

Booking & Payment: This section manages the entire transaction lifecycle.

The Bookings table connects a guest to a property for a specific date range and tracks the status of the booking (e.g., pending, confirmed, completed).

The Payments table has a unique, one-to-one relationship with a booking, recording the financial details and storing a reference ID from an external payment gateway.

Reviews & Notifications: This area handles user engagement and communication.

The Reviews table is uniquely linked to a booking_id, cleverly ensuring that only users who have completed a booking can leave a review. It also includes a field for a host to respond.

The Notifications table provides a simple, flexible system for sending alerts to any user about important events.