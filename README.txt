TOURISM DATA WAREHOUSE PRACTICE DATASET
========================================

Purpose
-------
Synthetic practice dataset designed for a tourism Business Intelligence / Data Warehouse project:
- destinations, hotels, airlines, flights, activities, offers/packages
- customers, bookings, reviews, hotel price history
- currency conversion reference
- mixed source formats and intentional data quality issues for SSIS ETL practice

IMPORTANT
---------
All records are synthetic and generated for educational practice. They are NOT real customers,
real bookings, live prices, or actual availability. Do not present them as factual market data.

Files in raw_sources/
--------------------
destinations.csv             destination dimension source
hotels.csv                  hotel source; semicolon delimiter; prices have occasional EUR/€ text
flights.csv                 flight source; prices have occasional EUR text and some invalid negatives
customers.csv               customer source; pipe (|) delimiter; casing and missing values
airlines.csv                airline lookup source
activities.xlsx              activities source in Excel format
offers.csv                  combined package offers referencing hotels and flights
bookings.csv                booking transactions; has some missing fields, invalid counts, duplicates
reviews.csv                 hotel reviews; contains a few invalid ratings
hotel_price_history.csv      dated hotel price observations for trend analysis
currency_rates.csv           reference conversion rates to EUR (synthetic fixed rates)

Approximate volume
------------------
Destinations: 80+
Hotels: 750 + deliberate duplicate rows
Flights: 1,800
Customers: 1,200
Activities: 1,100
Offers: 900
Bookings: 9,000 + deliberate duplicate rows
Reviews: 6,000
Hotel price observations: 7,000
Airlines: 20

Suggested ETL
-------------
1. Load raw files into staging tables (keep raw columns/text where practical).
2. Standardize city/country/airline casing and trim whitespace.
3. Parse numeric prices, retain currency, convert to EUR using currency_rates.
4. Identify duplicates using source business keys.
5. Validate hotel stars (1-5), rating (1-5), prices (>0), traveler counts (>0), dates.
6. Use Lookup to map Destination_ID, Hotel_ID, Flight_ID, Customer_ID, Offer_ID.
7. Route invalid records to reject tables with a Reject_Reason rather than silently deleting.
8. Load dimensions before facts; use surrogate keys in the DW.
9. Validate row counts and referential integrity.

Suggested star schema
--------------------
Fact_Booking: Booking_ID (degenerate/business ID), Customer_Key, Hotel_Key, Flight_Key,
Offer_Key, Booking_Date_Key, Travel_Start_Date_Key, Travel_End_Date_Key,
Travelers_Count, Hotel_Nights, Total_Amount_EUR, Discount_EUR, booking status/channel.

Dimensions: Dim_Customer, Dim_Destination, Dim_Hotel, Dim_Flight, Dim_Airline,
Dim_Activity (or bridge for multi-activity offers), Dim_Offer, Dim_Date.
Additional facts: Fact_HotelPriceSnapshot, Fact_Review (or aggregate reviews by hotel/date).

Important modeling note
----------------------
Offers and bookings contain references to hotels/flights. For a rigorous model, validate that
the offer destination matches the referenced hotel's destination and that flight route ends at
the package destination. This dataset intentionally does not guarantee every random offer/booking
combination is semantically compatible; use SSIS Lookup/Conditional Split to detect or exclude
inconsistent combinations, or define an explicit rule for the project.


HETEROGENEOUS SOURCE MIX
-------------------------
This dataset now includes several different source formats:
- CSV files with comma, semicolon, and pipe delimiters
- Excel workbooks (.xlsx), including a workbook with multiple sheets
- Nested JSON partner/API export
- XML airport directory using both attributes and child elements
- SQL script extract containing CREATE TABLE and INSERT statements
- Semi-structured TXT review feed

Use documentation/source_inventory.csv to see each file's format and suggested ETL challenge.
The data is synthetic and intended for SSIS/Data Warehouse practice only.
