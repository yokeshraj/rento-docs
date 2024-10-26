# Rento Documentations

## Table of Contents
- [Database Schema](#database-schema)
- [Tech Stack](#tech-stack)

## Database Schema
### Table Details: Users and User_Details
#### **1. Users (Master Table)**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| user_id           | INT (Primary Key) | Unique identifier for each user     |
| role           | VARCHAR(50) | Role of the user (e.g., admin, driver, regular user)     |
| first_name        | VARCHAR(100)      | First name of the user              |
| last_name         | VARCHAR(100)      | Last name of the user               |
| email             | VARCHAR(150)      | Email address of the user           |
| country_code  | VARCHAR(2)      | Add to enable easier querying of users by country|
| phone_number      | VARCHAR(20)       | Contact number of the user          |
| password_hash     | VARCHAR(255)      | Hashed password for authentication  |
| is_verified       | BOOLEAN           | Indicates if the user is verified   |
| created_at        | DATETIME          | Timestamp for when the user was created |
| updated_at        | DATETIME          | Timestamp for when the user was last updated |
| is_deleted        | BOOLEAN           | Soft delete flag for logical deletion |

**Relationships**:
- **User_Details** has a one-to-one relationship with **Users** based on `user_id`.
- **Authentication_Tokens**, **Two_Factor_Auth**, **Orders**, **User_Geolocation**, and **Preferences** are related to **Users** through `user_id`.

---

#### **2. User_Details (Sub Table)**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| user_id           | INT (Foreign Key) | Foreign key referencing **Users.user_id** |
| address           | VARCHAR(255)      | User's street address               |
| city              | VARCHAR(100)      | City of the user's address          |
| state             | VARCHAR(100)      | State of the user's address         |
| zip_code          | VARCHAR(20)       | Zip code of the user's address      |
| country           | VARCHAR(100)      | Country of the user's address       |

**Relationships**:
- **User_Details** references **Users** via `user_id`.

---

#### **3. Preferences (Sub Table)**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| preference_id     | INT (Primary Key) | Unique identifier for each preference |
| user_id           | INT (Foreign Key) | Reference to **Users.user_id**      |
| ride_preference    | VARCHAR(100)      | User's ride preferences (e.g., car type) |
| favorite_locations | TEXT              | User's favorite locations in JSON format |

**Relationships**:
- **Preferences** references **Users** via `user_id`.

---

### Driver Management

#### **4. Drivers (Master Table)**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| driver_id         | INT (Primary Key) | Unique identifier for each driver   |
| user_id           | INT (Foreign Key) | Reference to **Users.user_id**      |
| license_number    | VARCHAR(50)       | Driver's license number             |
| vehicle_id        | INT (Foreign Key) | Reference to **Vehicles.vehicle_id**|
| is_active         | BOOLEAN           | Indicates if the driver is active   |
| created_at        | DATETIME          | Timestamp for driver creation       |
| updated_at        | DATETIME          | Timestamp for last update           |
| is_deleted        | BOOLEAN           | Soft delete flag for logical deletion|

**Relationships**:
- One-to-one relationship with **Vehicles**.
- **Driver_Geolocation** references **Drivers** for current location tracking.
- **Driver_Ratings** tracks ratings for each driver over time.

---

#### **5. Driver_Documentation (Master Table)**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| document_id       | INT (Primary Key) | Unique identifier for each document |
| driver_id         | INT (Foreign Key) | Reference to **Drivers.driver_id**  |
| document_type     | VARCHAR(100)      | Type of document (e.g., license, insurance) |
| document_url      | VARCHAR(255)      | URL or path to the document         |
| document_expiry_date  | DATETIME     | Add to track when a document needs renewal |
| created_at        | DATETIME          | Timestamp for document upload       |

---

#### **6. Vehicles (Master Table)**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| vehicle_id        | INT (Primary Key) | Unique identifier for each vehicle  |
| driver_id         | INT (Foreign Key) | Reference to **Drivers.driver_id**  |
| vehicle_type      | VARCHAR(50)       | Type of vehicle (e.g., car, bike)   |
| vehicle_model     | VARCHAR(100)      | Vehicle model                       |
| license_plate     | VARCHAR(50)       | License plate number                |
| created_at        | DATETIME          | Timestamp for vehicle creation      |
| is_deleted        | BOOLEAN           | Soft delete flag for logical deletion|

**Relationships**:
- **Vehicles** references **Drivers** for ownership.

---

#### **7. Driver_Ratings (Sub Table)**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| rating_id         | INT (Primary Key) | Unique identifier for each rating    |
| driver_id         | INT (Foreign Key) | Reference to **Drivers.driver_id**   |
| rating            | INT               | Rating given (1-5)                  |
| created_at        | DATETIME          | Timestamp for rating submission      |

**Relationships**:
- **Driver_Ratings** references **Drivers** to track ratings.

---

### Service Management

#### **8. Services (Master Table)**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| service_id        | INT (Primary Key) | Unique identifier for each service  |
| service_name      | VARCHAR(100)      | Name of the service                 |
| description       | TEXT              | Description of the service          |
| base_price        | DECIMAL(10, 2)    | Base price of the service           |
| duration_minutes  | INT               | Estimated duration in minutes       |
| created_at        | DATETIME          | Timestamp for service creation      |
| is_deleted        | BOOLEAN           | Soft delete flag for logical deletion|

---

### Order Management

#### **9. Orders (Master Table)**

| **Column Name**      | **Data Type**     | **Description**                                           |
|----------------------|-------------------|-----------------------------------------------------------|
| order_id             | INT (Primary Key) | Unique identifier for each order                          |
| user_id              | INT (Foreign Key) | Reference to **Users.user_id**                            |
| driver_id            | INT (Foreign Key) | Reference to **Drivers.driver_id**                        |
| total_price          | DECIMAL(10, 2)    | Total cost for the order (can be retrieved from **Price_Calculation**) |
| order_status         | VARCHAR(50)       | Status of the order (e.g., pending, in progress, complete) |
| service_id           | INT (Foreign Key) | Reference to **Services.service_id**                      |
| cancellation_reason   | VARCHAR(255)      | Reason for order cancellation (if applicable)            |
| created_at           | DATETIME          | Timestamp for order creation                              |
| updated_at           | DATETIME          | Timestamp for last update                                 |
| is_deleted           | BOOLEAN           | Soft delete flag for logical deletion                    |

**Relationships**:
- **Orders** references **Price_Calculation** to get the total price details.
- **Order_Geolocation** tracks the geolocation data for pickup and drop-off points.

---

### Geolocation Tables (Separated for Users, Drivers, and Orders)

#### **10. User_Geolocation**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| geolocation_id    | INT (Primary Key) | Unique identifier for geolocation entry |
| user_id           | INT (Foreign Key) | Reference to **Users.user_id**      |
| latitude          | DECIMAL(10, 8)    | Latitude of user's current location |
| longitude         | DECIMAL(11, 8)    | Longitude of user's current location|
| timestamp         | DATETIME          | Time of geolocation record          |

---

#### **11. Driver_Geolocation**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| geolocation_id    | INT (Primary Key) | Unique identifier for geolocation entry |
| driver_id         | INT (Foreign Key) | Reference to **Drivers.driver_id**  |
| latitude          | DECIMAL(10, 8)    | Latitude of driver's current location |
| longitude         | DECIMAL(11, 8)    | Longitude of driver's current location|
| timestamp         | DATETIME          | Time of geolocation record          |

---

#### **12. Order_Geolocation**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| geolocation_id    | INT (Primary Key) | Unique identifier for geolocation entry |
| order_id          | INT (Foreign Key) | Reference to **Orders.order_id**    |
| pickup_latitude   | DECIMAL(10, 8)    | Latitude of pickup location         |
| pickup_longitude  | DECIMAL(11, 8)    | Longitude of pickup location        |
| dropoff_latitude  | DECIMAL(10, 8)    | Latitude of drop-off location       |
| dropoff_longitude | DECIMAL(11, 8)    | Longitude of drop-off location      |
| timestamp         | DATETIME          | Time of geolocation record          |

---

### Authentication and Security

#### **13. Authentication_Tokens**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| token_id          | INT (Primary Key) | Unique identifier for each token    |
| user_id           | INT (Foreign Key) | Reference to **Users.user_id**      |
| token             | VARCHAR(255)      | JWT or session token                |
| expires_at        | DATETIME          | Expiration date of the token        |
| created_at        | DATETIME          | Timestamp for when the token was created |
| updated_at        | DATETIME          | Timestamp for last update           |

---

#### **14. Two_Factor_Auth**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| auth_id           | INT (Primary Key) | Unique identifier for each auth record |
| user_id           | INT (Foreign Key) | Reference to **Users.user_id**      |
| phone_number      | VARCHAR(20)       | Phone number for two-factor authentication |
| is_enabled        | BOOLEAN           | Indicates if 2FA is enabled         |
| created_at        | DATETIME          | Timestamp for when the record was created |
| updated_at        | DATETIME          | Timestamp for last update           |

---

#### **15. Password_Reset_Tokens**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| reset_token_id    | INT (Primary Key) | Unique identifier for each token    |
| user_id           | INT (Foreign Key) | Reference to **Users.user_id**      |
| token             | VARCHAR(255)      | Token for password reset             |
| expires_at        | DATETIME          | Expiration date of the reset token  |
| created_at        | DATETIME          | Timestamp for when the token was created |

---

### Payment and Transaction Management

#### **16. Payment_Methods**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| payment_method_id  | INT (Primary Key) | Unique identifier for payment method |
| user_id           | INT (Foreign Key) | Reference to **Users.user_id**      |
| card_type         | VARCHAR(50)       | Type of card (e.g., Visa, MasterCard) |
| card_number       | VARCHAR(20)       | Encrypted card number               |
| expiration_date   | DATE              | Expiration date of the card        |
| is_default        | BOOLEAN           | Indicates if this is the default payment method |
| created_at        | DATETIME          | Timestamp for when the record was created |
| updated_at        | DATETIME          | Timestamp for last update           |

---
#### **17. Payment_Transaction**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| transaction_id    | INT (Primary Key) | Unique identifier for each transaction |
| order_id          | INT (Foreign Key) | Reference to **Orders.order_id**    |
| payment_method_id  | INT (Foreign Key) | Reference to **Payment_Methods.payment_method_id** |
| amount            | DECIMAL(10, 2)    | Amount paid                         |
| transaction_date  | DATETIME          | Date and time of the transaction    |
| status            | VARCHAR(50)       | Status of the transaction (e.g., completed, failed) |

---

### Logging System

#### **18. Admin_Actions_Log**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| action_id         | INT (Primary Key) | Unique identifier for each action   |
| admin_id          | INT (Foreign Key) | Reference to the admin user         |
| action_type       | VARCHAR(100)      | Type of action performed (e.g., create, update, delete) |
| target_table      | VARCHAR(100)      | Table where the action occurred     |
| target_id         | INT               | Identifier of the target record affected |
| details           | TEXT              | Description of the action taken     |
| created_at        | DATETIME          | Timestamp for when the action was logged |

**Relationships**:
- References **Admins** via `admin_id` (assuming an `Admins` table exists for admin user management).

---

#### **19. User_Activity_Log**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| user_activity_id  | INT (Primary Key) | Unique identifier for each user activity log entry |
| user_id           | INT (Foreign Key) | Reference to **Users.user_id**      |
| activity_type     | VARCHAR(100)      | Type of user activity (e.g., login, logout, order creation) |
| target_table      | VARCHAR(100)      | Table where the activity occurred   |
| target_id         | INT               | Identifier of the affected record    |
| details           | TEXT              | Description of the user activity     |
| created_at        | DATETIME          | Timestamp for when the activity was logged |

**Relationships**:
- References **Users** via `user_id`.

---

#### **20. Global_Activity_Log**

| **Column Name**   | **Data Type**     | **Description**                     |
|-------------------|-------------------|-------------------------------------|
| global_activity_id | INT (Primary Key) | Unique identifier for each global activity log entry |
| activity_type     | VARCHAR(100)      | Type of global activity (e.g., system startup, error logs) |
| details           | TEXT              | Description of the global activity   |
| created_at        | DATETIME          | Timestamp for when the activity was logged |

**Purpose**:
- This table logs significant system-level events that affect all users, such as system errors, performance metrics, or maintenance activities.

---

### Notification System

#### **21. Notifications**

| **Column Name**     | **Data Type**     | **Description**                             |
|---------------------|-------------------|---------------------------------------------|
| notification_id     | INT (Primary Key) | Unique identifier for each notification     |
| user_id             | INT (Foreign Key) | Reference to **Users.user_id**              |
| notification_type   | VARCHAR(100)      | Type of notification (e.g., ride updates, promotions, system alerts) |
| title               | VARCHAR(255)      | Short title for the notification            |
| message             | TEXT              | Detailed message for the notification       |
| is_read             | BOOLEAN           | Indicates if the notification has been read |
| created_at          | DATETIME          | Timestamp for when the notification was created |
| updated_at          | DATETIME          | Timestamp for when the notification was last updated |
| is_deleted          | BOOLEAN           | Soft delete flag for logical deletion       |

**Relationships**:
- References **Users** via `user_id`, linking notifications to specific users.

---

#### **22. User_Notification_Preferences**

| **Column Name**     | **Data Type**     | **Description**                             |
|---------------------|-------------------|---------------------------------------------|
| preference_id       | INT (Primary Key) | Unique identifier for notification preferences |
| user_id             | INT (Foreign Key) | Reference to **Users.user_id**              |
| email_notifications  | BOOLEAN           | Indicates if the user wants to receive email notifications |
| sms_notifications    | BOOLEAN           | Indicates if the user wants to receive SMS notifications |
| push_notifications    | BOOLEAN           | Indicates if the user wants to receive app push notifications |
| created_at          | DATETIME          | Timestamp for when the preferences were set |
| updated_at          | DATETIME          | Timestamp for when the preferences were last updated |

**Relationships**:
- References **Users** via `user_id`, allowing users to manage their notification preferences.

---

#### **23. Notification_Log**

| **Column Name**     | **Data Type**     | **Description**                             |
|---------------------|-------------------|---------------------------------------------|
| log_id              | INT (Primary Key) | Unique identifier for each notification log entry |
| notification_id     | INT (Foreign Key) | Reference to **Notifications.notification_id** |
| sent_at             | DATETIME          | Timestamp for when the notification was sent |
| status              | VARCHAR(50)       | Status of the notification (e.g., sent, failed) |
| error_message       | TEXT              | Error message if the notification failed to send |

**Relationships**:
- References **Notifications** via `notification_id`, tracking the status of sent notifications.

---

### Price Calculation

#### **24. Price_Calculation (Master Table)**

| **Column Name**      | **Data Type**     | **Description**                                |
|----------------------|-------------------|------------------------------------------------|
| calculation_id       | INT (Primary Key) | Unique identifier for each price calculation    |
| order_id             | INT (Foreign Key) | Reference to **Orders.order_id**               |
| base_price           | DECIMAL(10, 2)    | Base price of the service                       |
| distance_fee         | DECIMAL(10, 2)    | Additional fee based on distance                |
| time_fee             | DECIMAL(10, 2)    | Additional fee based on time                    |
| additional_fees      | DECIMAL(10, 2)    | Any additional fees (e.g., tolls, surcharges)  |
| discounts            | DECIMAL(10, 2)    | Total discounts applied to the order            |
| total_price          | DECIMAL(10, 2)    | Final total price after applying fees and discounts |
| created_at           | DATETIME          | Timestamp for price calculation creation         |
| updated_at           | DATETIME          | Timestamp for last update                       |

**Relationships**:
- **Price_Calculation** references **Orders** via `order_id`, linking pricing details directly to specific orders.

---

### Pricing Calculation Breakdown

The **Price_Calculation** table allows you to compute the total price for an order based on several components. Here's how the total price is calculated:

##### 1. **Base Price**: This is the starting price for the service selected (from the **Services** table).

##### 2. **Distance Fee**: This fee is calculated based on the distance traveled for the order. It can vary by service type and distance covered.

##### 3.  **Time Fee**: This fee accounts for the time taken to complete the order. It may include waiting time and any delays.

##### 4. **Additional Fees**: Any extra charges that may apply, such as tolls or service surcharges.

##### 5.  **Discounts**: Any promotional discounts or user-specific discounts that are applied to reduce the overall price.

##### 6.  **Total Price**: The total price for the order is computed as follows: Total Price = Base Price + Distance Fee + Time Fee + Additional Fees - Discounts

### **25. Subscription Table**

| Column Name                | Data Type                | Description                                                               |
|----------------------------|--------------------------|---------------------------------------------------------------------------|
| subscription_id             | INT (Primary Key)        | Unique identifier for each subscription                                   |
| user_id                     | INT (Foreign Key)        | Reference to Users.user_id                                                |
| plan_name                   | VARCHAR(50)              | Name of the subscription plan                                             |
| total_rides                 | INT                      | Total number of rides used during the subscription                        |
| remaining_rides             | INT                      | Remaining rides left in the subscription                                  |
| subscription_type           | VARCHAR(50)              | Type of subscription (e.g., '30 days', '90 days')                         |
| start_date                  | DATETIME                 | The date when the subscription starts                                     |
| end_date                    | DATETIME                 | The date when the subscription ends                                       |
| pickup_location             | VARCHAR(255)             | Address for the pickup location                                           |
| pickup_latitude             | DECIMAL(10, 8)           | Latitude of the pickup location                                           |
| pickup_longitude            | DECIMAL(11, 8)           | Longitude of the pickup location                                          |
| drop_location               | VARCHAR(255)             | Address for the drop-off location                                         |
| drop_latitude               | DECIMAL(10, 8)           | Latitude of the drop-off location                                         |
| drop_longitude              | DECIMAL(11, 8)           | Longitude of the drop-off location                                        |
| validity_days               | INT                      | Number of days the subscription is valid                                  |
| amount                      | DECIMAL(10, 2)           | Total subscription cost based on distance (adjusted daily)                |
| created_at                  | DATETIME                 | Timestamp for when the subscription was created                           |
| updated_at                  | DATETIME                 | Timestamp for when the subscription was last updated                      |
| cancellation_reason          | TEXT                     | Reason for cancellation, if applicable                                    |
| cancellation_date           | DATETIME                 | Date when the subscription was canceled                                   |
| is_active                   | BOOLEAN                  | Indicates if the subscription is currently active                         |
| is_deleted                  | BOOLEAN                  | Soft delete flag for logical deletion                                     |
| payment_method              | VARCHAR(50)              | Method of payment used for the subscription                               |
| transaction_id              | VARCHAR(255)             | Unique identifier for the payment transaction                             |
| last_renewal_date           | DATETIME                 | When the subscription was last renewed                                    |
| notification_sent           | BOOLEAN                  | Indicates if a notification has been sent                                 |
| status                      | ENUM('active', 'expired', 'canceled') | Current status of the subscription                           |
| discount_code               | VARCHAR(50)              | Promotional code applied to the subscription                              |
| discount_amount             | DECIMAL(10, 2)           | Discount amount applied to the subscription                               |
| previous_subscription_type  | VARCHAR(50)              | Type of previous subscription (if renewed)                                |
| feedback                    | TEXT                     | User feedback on their subscription experience                            |

---

### **26. Daily Deduction Table**

| Column Name                | Data Type                | Description                                                               |
|----------------------------|--------------------------|---------------------------------------------------------------------------|
| daily_deduction_id          | INT (Primary Key)        | Unique identifier for each daily deduction record                         |
| subscription_id             | INT (Foreign Key)        | Reference to Subscription.subscription_id                                 |
| deduction_date              | DATETIME                 | The date for which the deduction is made                                  |
| daily_amount                | DECIMAL(10, 2)           | Amount deducted for the day                                               |
| total_amount_remaining       | DECIMAL(10, 2)           | Total amount remaining after deduction                                    |
| status                      | ENUM('deducted', 'extra_payment', 'refund') | Status of the transaction (deduction, extra payment, or refund) |
| notes                       | TEXT                     | Any additional notes or reasons for the deduction                         |

---

## Tech Stack
## Backend Tech Stack

| **Category**            | **Technology**              | **Description**                                |
|-------------------------|-----------------------------|------------------------------------------------|
| **Framework**           | Nest.js                  | A web framework for Node.js for building REST APIs. |
| **Programming Language**| TypeScript       | Core language for backend logic.               |
| **Database**            | PostgreSQL                  | Relational database with scalability.          |
| **ORM/Query Builder**   | TypeORM           | Helps to interact with PostgreSQL easily.      |
| **Authentication**      | JWT            | JSON Web Tokens for stateless authentication. |
| **Real-time Updates**   | Socket.io                   | To handle real-time driver and rider updates.  |
| **Payments**            | Razorpay or Paytm API       | Payment gateway popular in India.              |
| **Geolocation**         | Google Maps API/Mapbox      | Geolocation services and maps.                 |
| **Caching**             | Redis                       | For faster access to frequently requested data (e.g., active trips). |
| **Cloud Hosting**       | AWS, DigitalOcean, or Google Cloud (India Region) | Server hosting for Indian customers.           |
| **Server Management**   | Nginx                       | Load balancing and reverse proxy server.       |
| **API Documentation**   | Swagger                     | For documenting APIs.                          |
| **Testing**             | Jest, Mocha                 | Unit and integration testing frameworks.       |
| **Notification**        | FCM (Firebase Cloud Messaging) | For push notifications to drivers and riders. |

## Frontend Tech Stack - Web Application

| **Category**            | **Technology**              | **Description**                                |
|-------------------------|-----------------------------|------------------------------------------------|
| **Framework**           | React.js                    | JavaScript library for building web UIs.       |
| **State Management**    | Redux or Context API        | For managing global state.                     |
| **Routing**             | React Router                | For navigation within the web app.             |
| **Styling**             | Tailwind CSS or Material UI | CSS framework for responsive and stylish UI.   |
| **API Integration**     | Axios or Fetch API          | For HTTP requests to the backend.              |
| **Real-time Updates**   | Socket.io-client            | For real-time data updates.                    |
| **Maps Integration**    | Google Maps API/Mapbox      | Displaying geolocation data on a map.          |
| **Form Management**     | Formik + Yup                | For managing and validating forms.             |
| **Testing**             | Jest, React Testing Library | For unit and integration testing.              |
| **Hosting**             | AWS S3, Vercel, Netlify     | Hosting the web front end.                     |

## Mobile Application Tech Stack - Mobile App (React Native)

| **Category**            | **Technology**              | **Description**                                |
|-------------------------|-----------------------------|------------------------------------------------|
| **Framework**           | React Native                | Cross-platform mobile framework for Android and iOS. |
| **State Management**    | Redux or Context API        | For managing global state.                     |
| **Navigation**          | React Navigation            | Library for screen-to-screen navigation.       |
| **Real-time Updates**   | Socket.io-client            | For real-time data updates between driver and rider. |
| **Forms and Validation**| Formik + Yup                | For form management and validation.            |
| **Maps Integration**    | React Native Maps           | Map view component for geolocation features.   |
| **Notifications**       | FCM (Firebase Cloud Messaging) | For push notifications to drivers and riders.  |
| **Payments**            | Razorpay React Native SDK   | Payment gateway integration for Indian customers. |
| **Device Features**     | React Native Geolocation, Permissions | Access to GPS and other device capabilities.   |
| **Testing**             | Jest, Detox                 | For testing components and integration.        |
| **Distribution**        | Google Play Store, Apple App Store | Distribution channels for Android and iOS.     |

## Database and Storage Tech Stack

| **Category**            | **Technology**              | **Description**                                |
|-------------------------|-----------------------------|------------------------------------------------|
| **Main Database**       | PostgreSQL                  | Used for storing structured data like user profiles, trips, etc. |
| **Geospatial Data**     | PostGIS                     | Extension for PostgreSQL for geospatial data support. |
| **Caching**             | Redis                       | For storing frequently accessed data like ongoing rides. |
| **File Storage**        | AWS S3, Google Cloud Storage | For storing files like profile pictures, documents.  |
| **Backup Database**     | Amazon RDS, DigitalOcean DBaaS | Managed database services for automatic backups and recovery. |

## Server Configurations

| **Category**            | **Technology**              | **Description**                                |
|-------------------------|-----------------------------|------------------------------------------------|
| **Cloud Service Provider** | AWS India Region (Mumbai), DigitalOcean (Bangalore), Google Cloud (Mumbai) | Servers hosted in Indian data centers to comply with local regulations. |
| **CDN**                 | Cloudflare or AWS CloudFront | For faster content delivery across India.      |
| **Compliance**          | ISO/IEC 27001, GDPR, Local Compliance | Ensure compliance with Indian data security laws. |
| **Payment Gateway**     | Razorpay, Paytm, Cashfree   | Payment providers that comply with RBI regulations in India. |
| **SMS Service**         | Twilio, MSG91               | SMS services for OTP and notifications tailored for India. |
