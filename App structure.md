# App structure definitions

We're about to define the structure of a web. The example will be a page called **RentaBoat** where we can reserve boats (kind of AirBnb for Boats).



## Front End



### User Stories

+ Signup - Login - Logout
+ 404 / 500
+ Add Boat
+ List Boats
+ Detail Boat
+ My profile
+ Create Booking
+ Accept Booking
+ Reject Booking



### Pages 

+ Login
+ Sign Up
+ 404 / 500
+ List-Boat
+ Add-boat
+ Boat-detail
+ My-profile
+ Create-booking



### Components

+ 8 * pages
+ Boat-list-component
+ boat-card-component
+ booking-card-component



### I/O

+ Boat-list-component INPUTS Single-Boat to Boat-Card-component
+ My-profile-page INPUTS single-booking to booking-card-component
+ Booking-card-component OUTPUTS handle REJECT in my-profile-page
+ Booking-card-component OUTPUTS handle ACCEPT in my-profile-page



### Services

#### Auth service

+ me()
+ Login (username, password)
+ Signup (username, password)
+ Logout ()
+ getUser()
+ setUser()

#### Boat service

+ getAll()
+ getOne(id)
+ CreateOne(data)

#### Booking service

+ CreateOne(data)
+ getUserBookings(userID)
+ acceptOne(bookingID)
+ rejectOne(bookingID)





## Back End

### Models

#### User

+ Username: String
+ Password: String

#### Boat

+ Name: String
+ Description: String
+ Owner: UserID

#### Reservations

+ Requester: UserID
+ Boat: BoatID
+ startDate: Date
+ endDate: Date



### Controllers

#### Auth

+ GET - /auth/me
+ POST - /auth/login - Body: username, password
+ POST /auth/signup - Body: username, password
+ POST /auth/logout

#### Booking 

+ POST /bookings - Body: reservation data
+ GET /bookings/my-bookings
+ PUT /bookings/accept - Body
+ PUT /bookings/reject - Body

#### Boat

+ GET /boats
+ GET /boats/:id
+ POST /boats - Body: data