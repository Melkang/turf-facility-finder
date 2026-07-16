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
