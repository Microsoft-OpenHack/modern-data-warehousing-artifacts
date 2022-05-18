# Table Definitions

- [DimActors](#DimActors)
- [DimCategories](#DimCategories)
- [DimCustomers](#DimCustomers)
- [DimDate](#DimDate)
- [DimLocations](#DimLocations)
- [DimMovieActors](#DimMovieActors)
- [DimMovies](#DimMovies)
- [DimRatings](#DimRatings)
- [DimTime](#DimTime)
- [FactRentals](#FactRentals)
- [FactSales](#FactSales)
- [FactStreaming](#FactStreaming)

## DimActors

Column Name | Data Type | Nulls | Rules
---|---|---|---
ActorSK | 32-bit Integer | No | Sequential Integer Key
ActorID | GUID | No | Unique Identifier from soruce system
ActorName | String | No | Name of actor (81 character max)
ActorGender | String | No | Single-character representation of actor's gender

### Data Generation/Population Rules

- Data should be the combination of actor lists from all source systems
- Duplicates should be removed and accounted for before they are loaded into the warehouse

[Back to top](#Table-Definitions)

## DimCategories

Column Name | Data Type | Nulls | Rules
---|---|---|---
MovieCategorySK | 8-bit Integer | No | Sequential Integer Key
MovieCategoryDescription | String | No | Description of Movie Category

### Data Generation/Population Rules

- Data should be the combination of movie categories from all source systems
- Duplicates should be removed and accounted for before they are loaded into the warehouse

[Back to top](#Table-Definitions)

## DimCustomers

Column Name | Data Type | Nulls | Rules
---|---|---|---
CustomerSK | 32-bit Integer | No | Sequential integer key
CustomerID | GUID | No | Unique identifier from source system
LastName | String | No | Customer last name (50 characters max)
FirstName | String | No | Customer first name (50 characters max)
AddressLine1 | String | No | Customer Address - Line 1 (50 characters max)
AddressLine2 | String | Yes | Customer Address - Line 2 (50 characters max)
City | String | No | Customer city (30 characters max)
State | String | No | Customer state code (exactly 2 characters)
ZipCode | String | No | Customer zip code (exactly 5 digits)
PhoneNumber | String | No | Customer phone number (exactly 10 digits)
RecordStartDate | Date | No | Date that this version of the customer recrod became active
RecordEndDate | Date | Yes | Date that this version of the customer record became inactive
ActiveFlag | Boolean | No | Flag set to true for the active record for a given customer

### Data Generation/Population Rules

- This is a type-2 dimension - changes to records should cause previous record to become inactive
- Data should be the combination of customer records from all source systems
- Duplicates should be removed and accounted for before they are loaded into the warehouse
- If your database platform supports referential integrity and keys, you may use the following rules:
  - Primary Key - `CustomerSK`
  - Unique Index - `CustomerID`

[Back to top](#Table-Definitions)

## DimDate

Column Name | Data Type | Nulls | Rules
---|---|---|---
DateSK | 32-bit Integer | No | Sequential Integer Key
DateValue | Date | No | Date Value (No Time)
DateYear | 16-bit Integer | No | Year part of DateValue
DateMonth | 8-bit integer | No | Month part of DateValue
DateDay | 8-bit integer | No | Day part of DateValue
DateDayOfWeek | 8-bit integer | No | Day of week for DateValue (Sunday = 1)
DateDayOfYear | 16-bit integer | No | Day of year for DateValue
DateWeekOfYear | 8-bit integer | No | Week of year for DateValue

### Data Generation/Population Rules

- First date record should be for `2017-01-01`
- For initial (bulk) data load, include one `DimDate` record for each date being loaded
- For daily (incremental) data load, add a `DimDate` record for the day being loaded

[Back to top](#Table-Definitions)

## DimLocations

Column Name | Data Type | Nulls | Rules
---|---|---|---
LocationSK | 16-bit Integer | No | Sequential Integer Key
LocationName | String | No | Name of location (50 character max)
Streaming | Boolean | No | True if Location supports streaming
Rentals | Boolean | No | True if Location supports rentals
Sales | Boolean | No | True if location supports sales

### Data Generation/Population Rules

- Create one record per location that provides source data
- This table should be initially loaded with one record for each data source location
  - As locations are added, add a record to this table for the new location

[Back to top](#Table-Definitions)

## DimMovieActors

Column Name | Data Type | Nulls | Rules
---|---|---|---
MovieID | GUID | No | ID of movie
ActorID | GUID | No | ID of actor

### Data Generation/Population Rules

- This is a many-to-many mapping table of moives to actors
- Data should be the combination of movie ratings from all source systems
- Duplicates should be removed and accounted for before they are loaded into the warehouse

[Back to top](#Table-Definitions)

## DimMovies

Column Name | Data Type | Nulls | Rules
---|---|---|---
MovieSK | 32-bit Integer | No | Sequential Integer Key
MovieID | GUID | No | Unique Identifier from soruce system
MovieTitle | String | No | Title of movie (255 character max)
MovieCategorySK | 8-bit Integer | No | Key pointing movie's category (from DimCategories table)
MovieRatingSK | 8-bit Integer | No | Key pointing to movie's rating (from DimRatings table)
MovieRunTimeMin | 16-bit Integer | No | Run time of movie in minutes

### Data Generation/Population Rules

- Data should be the combination of movie lists from all source systems
- Duplicates should be removed and accounted for before they are loaded into the warehouse

[Back to top](#Table-Definitions)

## DimRatings

Column Name | Data Type | Nulls | Rules
---|---|---|---
MovieRatingSK | 8-bit Integer | No | Sequential Integer Key
MovieRatingDescription | String | No | Description of Movie Rating (maximum 5 characters)

### Data Generation/Population Rules

- Data should be the combination of movie ratings from all source systems
- Duplicates should be removed and accounted for before they are loaded into the warehouse

[Back to top](#Table-Definitions)

## DimTime

Column Name | Data Type | Nulls | Rules
---|---|---|---
TimeSK | 32-bit Integer | No | Sequential Integer Key
TImeValue | Time | No | Time Value (No Date; 1 Second Granularity)
TimeHour | 8-bit Integer | No | Hour part of TimeValue
TimeMinute | 8-bit Integer | No | Minute part of TimeValue
TimeSecond | 8-bit Integer | No | Second part of TimeValue
TimeMinuteOfDay | 16-bit Integer | No | Minute of day (Midnight = 0)
TimeSecondOfDay | 32-bit Integer | No | Second of day (Midnight = 0)

### Data Generation/Population Rules

- Create one record per second of day (86,400 total records)
- This is a one-time load table - data should not be added or changed

[Back to top](#Table-Definitions)

## FactRentals

Column Name | Data Type | Nulls | Rules
---|---|---|---
RentalSK | 32-bit Integer | No | Sequential integer key
TransactionID | GUID | No | Transaction ID from source system
CustomerSK | 32-bit Integer | No | Surrogate key from the Customers dimension for the customer
LocationSK | 16-bit Integer | No | Surrogate key from the Locations dimension for the location of the source data
MovieSK | 32-bit Integer | No | Surrogate key from the Movies dimension for the movie
RentalDateSK | 32-bit Integer | No | Surrogate key from the Date dimension for the date the movie was rented
ReturnDateSK | 32-bit Integer | Yes | Surrogate key from the Date dimension for the date the movie was returned
RentalDuration | 8-bit Integer | Yes | Number of days from when a movie was rented until it was returned
RentalCost | Currency | No | Cost for the base rental
LateFee | Currency | Yes | Late charges for the rental
TotalCost | Currency | Yes | RentalCost + LateFee
RewindFlag | Boolean | Yes | Was the movie rewound?

### Data Generation/Population Rules

- This is a fact table representing physical movie rentals
- Data should be the combination of customer records from all source systems that deal with movie rentals

[Back to top](#Table-Definitions)

## FactSales

Column Name | Data Type | Nulls | Rules
---|---|---|---
SalesSK | 32-bit Integer | No | Sequential integer key
OrderID | GUID | No | Order ID from source system
LineNumber | 8-bit Integer | No | Order Line Number from source system
OrderDateSK | 32-bit Integer | No | Surrogate key from the Date dimension for the date the order was placed
ShipDateSK | 32-bit Integer | Yes | Surrogate key from the Date dimension for the date the order was shipped
CustomerSK | 32-bit Integer | No | Surrogate key from the Customers dimension for the customer
LocationSK | 16-bit Integer | No | Surrogate key from the Locations dimension for the location of the source data
MovieSK | 32-bit Integer | No | Surrogate key from the Movies dimension for the movie
DaysToShip | 8-bit Integer | Yes | Number of days from when an order was placed to when the order shipped
Quantity | 8-bit Integer | No | Quantity of items purchased for this line item
UnitCost | Currency | No | Price of a single quantity of the item purchased for this line item
ExtendedCost | Currency | No | Quantity * UnitCost

### Data Generation/Population Rules

- This is a fact table representing physical movie sales
- Data should be the combination of customer records from all source systems that deal with movie sales

[Back to top](#Table-Definitions)

## FactStreaming

Column Name | Data Type | Nulls | Rules
---|---|---|---
StreamingSK | 32-bit Integer | No | Sequential integer key
TransactionID | GUID | No | Transaction ID from source system
CustomerSK | 32-bit Integer | No | Surrogate key from the Customers dimension for the customer
MovieSK | 32-bit Integer | No | Surrogate key from the Movies dimension for the movie
StreamStartDateSK | 32-bit Integer | No | Surrogate key from the Date dimension for the date the stream was started
StreamStartTimeSK | 32-bit Integer | No | Surrogate key from the Time dimension for the time the stream was started
StreamEndDateSK | 32-bit Integer | Yes | Surrogate key from the Date dimension for the date the stream was ended
StreamEndTimeSK | 32-bit Integer | Yes | Surrogate key from the Time dimension for the time the stream was ended
StreamDurationSec | 32-bit Integer | Yes | Duration (in seconds) of the streaming session
StreamDurationMin | Decimal | Yes | Duration (in minutes + fraction of minutes) of the streaming session (4 decimal place precision)

### Data Generation/Population Rules

- This is a fact table representing movie streaming sessions
- Data should be the combination of customer records from all source systems that deal with movie streaming

[Back to top](#Table-Definitions)
