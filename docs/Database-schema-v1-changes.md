# Database Changes for Version 1.1

This file is a short history of the decisions made while revising the first database draft.

## What Changed

- Made `05-database-schema.md` the main file to trust for table names, field names, and relationships.
- Updated the other database documents to use the same names as the schema.
- Removed `opportunity_rating` and other scoring fields from `facilities`.
- Moved geographic coordinates to `addresses`.
- Moved physical-site fields to `properties`.
- Allowed each Facility to have several Opportunity Scores so older scores can be kept.
- Made primary-key and foreign-key names consistent, including `data_source_id`.
- Changed internal IDs from UUID-style values to MySQL-generated `BIGINT UNSIGNED AUTO_INCREMENT` primary keys.
- Changed matching foreign keys to `BIGINT UNSIGNED`.
- Replaced examples such as `fac_8f32a` with simple numeric IDs such as `1042`.
- Moved Vendors, Contacts, Search history, Social Profiles, and Saved Leads out of the first database version.
- Updated the ERD so it shows every first-version table and leaves out future tables.
- Set the approved first-version Facility Types to Dog Daycare, Dog Boarding, Kennel, Dog Training Center, Canine Sports Facility, Animal Shelter, Animal Rescue, Adoption Center, Pet Resort, Dog Park, Mixed Animal Facility, Other, and Unknown.
- Defined `Other` as a known type outside the approved categories and `Unknown` as a type that has not been confirmed.
- Added the optional `evidence.source_url` field to store the exact page, review, image, or record supporting an Evidence item.
- Kept `data_sources.base_url` for the general source website and documented the difference between the two URL fields.
- Changed Address and Property relationships from required one-to-one to optional one-to-one. A Facility may have zero or one of each while research is incomplete.
- Required `UNIQUE (facility_id)` on `addresses` and `properties` so a Facility cannot have duplicate child records.
- Made `addresses.street` and `addresses.postal_code` optional for locations without a normal mailing address.
- Documented that `NULL` means “not confirmed” for optional Property Boolean fields, while `FALSE` means the feature was checked and is not present.
- Approved Facility Status values: Active, Temporarily Closed, Permanently Closed, and Unknown.
- Approved Surface Type values: Grass, Dirt, Mulch, Gravel, Concrete, Synthetic Turf, Mixed, Other, and Unknown.
- Approved Evidence Type values: Business Listing, Website, Review, Photo, Satellite Image, Street View, Social Media Post, News Article, Public Record, Manual Observation, and Other.
- Approved Opportunity Rating values: High, Medium, and Low. No score record means the Facility has not been scored yet.
- Renamed `data_sources.source_type` to `collection_method` because the values describe how information entered the database.
- Approved Collection Method values: API, Web Scrape, Manual Research, File Import, User Submitted, and Other.
- Replaced the free-text `photos.source` field with the `photos.data_source_id` foreign key.
- Added `created_at` and `updated_at` to `data_sources`.
- Changed `properties.estimated_play_area_sqft` to `INT UNSIGNED` and clarified that it may describe an indoor or outdoor animal activity area.
- Required `properties.surface_type` and set `Unknown` as its planned SQL default.
- Added value limits for coordinates, Opportunity Scores, and confidence scores.
- Added timestamp defaults, unique constraints, search indexes, and restrictive foreign-key rules for the future SQL.
- Documented how the server-rendered interface should display missing Address, Property, score, and contact values.
