# Turf Facility Finder

## Database Schema

Version: 1.1
Status: Draft

---

## 1. Purpose

This is the main planning document for the Turf Facility Finder database. It lists the tables, the fields inside each table, and how the tables connect.

The project helps turf vendors find animal-related Facilities that may benefit from synthetic turf.

This document defines:

- database tables
- primary keys
- foreign keys
- relationships
- decisions made to avoid repeated data
- fields that may need indexes for faster searches
- ideas saved for later versions

This file describes what the database should contain. It is not the SQL code that creates the database.

The SQL scripts will be written after this draft is reviewed.

If two database documents disagree, use this file as the correct version. The Domain Model, Data Dictionary, Database Design Decisions, and ERD should all match it.

**MVP** means the smallest useful first version of the project.

---

## 2. Main Database Goals

The database should:

- store each Facility location once
- allow each Facility to have several pieces of Evidence
- keep useful older records instead of overwriting or deleting them
- keep basic Facility facts separate from research notes and calculated scores
- support future map and distance searches
- accept both imported data and manual research
- avoid storing the same information in several places

Keeping each fact in the table where it belongs is called **normalization**. This database aims for Third Normal Form (3NF), which is a common way to reduce repeated data and keep relationships clear.

---

## 3. Naming Standards

Use these naming rules so fields are predictable and easy to find.

### Tables

- use plural nouns: `facilities`, not `facility`
- use lowercase letters
- separate words with underscores: `facility_types`

Examples

facilities
facility_types
opportunity_scores
data_sources

### Primary Keys

Every table has a **primary key**, which is the unique ID for one record. Its name matches the table. For example, `facilities` uses `facility_id`.

Primary keys use `BIGINT UNSIGNED AUTO_INCREMENT`. In plain language, this is a positive whole number that MySQL creates automatically when a record is added.

### Foreign Keys

**Foreign keys** connect tables. A foreign key stores the primary key of a related record.

Foreign keys use `BIGINT UNSIGNED` so their type matches the related primary key. They do not use `AUTO_INCREMENT` because they point to an ID that already exists.

facility_id
facility_type_id
data_source_id

### Date Fields

Date-field names describe when something happened:

created_at
updated_at
last_verified_at

### Boolean Fields

Boolean fields store true or false. Their names should read clearly as yes-or-no questions:

synthetic_turf_present
fenced_area
parking_available
outdoor_space

## 4. Table Relationship Overview

Facility
│
├── Facility Type
│
├── Address
│
├── Property
│
├── Evidence
│
├── Photos
│
└── Opportunity Scores

Planned for later:

- Vendors
- Saved Leads
- Social Profiles

## 5. Table Definitions

---

`facilities`

### Purpose

Stores one animal-related business location that may be a turf sales opportunity.

---

### Relationships

- Belongs to one **facility_type**
- Has one **address**
- Has one **property** record
- Has many **photos**
- Has many **evidence** records
- Has many **opportunity_scores**

---

### Columns

| Column           | Type         | Required | Description               |
| ---------------- | ------------ | -------- | ------------------------- |
| facility_id      | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key |
| facility_name    | VARCHAR(255) | Yes      | Business name             |
| facility_type_id | BIGINT UNSIGNED | Yes | References facility_types |
| primary_website  | VARCHAR(500) | No       | Official website          |
| primary_phone    | VARCHAR(30)  | No       | Business phone number     |
| google_place_id  | VARCHAR(255) | No       | Google Maps Place ID      |
| status           | ENUM         | Yes      | Active, Inactive, Closed  |
| last_verified_at | DATETIME     | No       | Last verification date    |
| created_at       | DATETIME     | Yes      | Record created            |
| updated_at       | DATETIME     | Yes      | Record updated            |

---

### Notes

- One record represents one real-world facility.
- This is the central table in the database.

`facility_types`

### Purpose

Stores the available facility categories.

Examples include:

- Dog Daycare
- Dog Boarding
- Kennel
- Dog Training Center
- Canine Sports Facility
- Animal Shelter
- Animal Rescue
- Adoption Center
- Pet Resort
- Dog Park
- Mixed Animal Facility
- Other
- Unknown

---

### Relationships

- One facility type can have many facilities.

---

### Columns

| Column           | Type         | Required | Description          |
| ---------------- | ------------ | -------- | -------------------- |
| facility_type_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key |
| type_name        | VARCHAR(100) | Yes      | Facility category    |
| description      | TEXT         | No       | Optional description |
| created_at       | DATETIME     | Yes      | Record created       |
| updated_at       | DATETIME     | Yes      | Record updated       |

---

### Notes

Stores each approved Facility category once so category names stay consistent.

Use `Other` when the Facility Type is known but does not fit an approved category. Use `Unknown` when the Facility Type has not been confirmed.

---

`properties`

### Purpose

Stores facts about a Facility's physical site.

These facts help show whether synthetic turf may be useful at that location.

Examples include:

- estimated outdoor play area size
- current surface conditions
- fencing
- parking availability
- existing synthetic turf

---

### Relationships

- One facility has one property record.

---

### Columns

| Column                   | Type     | Required | Description                                                   |
| ------------------------ | -------- | -------- | ------------------------------------------------------------- |
| property_id              | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key                                                   |
| facility_id              | BIGINT UNSIGNED | Yes | References facilities                                         |
| estimated_play_area_sqft | INTEGER  | No       | Estimated outdoor activity area size                          |
| surface_type             | ENUM     | No       | Grass, Dirt, Mixed, Gravel, Synthetic Turf, Concrete, Unknown |
| synthetic_turf_present   | BOOLEAN  | No       | Indicates whether synthetic turf is currently installed       |
| fenced_area              | BOOLEAN  | No       | Indicates whether outdoor areas appear to be fenced           |
| parking_available        | BOOLEAN  | No       | Indicates whether customer parking appears available          |
| outdoor_space            | BOOLEAN  | Yes      | Indicates whether an outdoor area exists                      |
| created_at               | DATETIME | Yes      | Record created                                                |
| updated_at               | DATETIME | Yes      | Record last updated                                           |

---

### Notes

Property fields store things that can be observed or reasonably estimated about the site.

Some property values may be estimated from evidence sources such as:

- satellite imagery
- street view
- business websites
- facility photos

Store the fact in `properties`. Store where the fact came from and why it was entered in `evidence`.

`addresses`

### Purpose

Stores the physical location for each facility.

---

### Relationships

- One address belongs to one facility.

---

### Columns

| Column      | Type         | Required | Description           |
| ----------- | ------------ | -------- | --------------------- |
| address_id  | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key |
| facility_id | BIGINT UNSIGNED | Yes | References facilities |
| street      | VARCHAR(255) | Yes      | Street address        |
| city        | VARCHAR(100) | Yes      | City                  |
| state       | VARCHAR(100) | Yes      | State                 |
| postal_code | VARCHAR(20)  | Yes      | ZIP or Postal Code    |
| country     | VARCHAR(100) | Yes      | Country               |
| latitude    | DECIMAL(9,6) | Yes      | Latitude coordinate   |
| longitude   | DECIMAL(9,6) | Yes      | Longitude coordinate  |
| created_at  | DATETIME     | Yes      | Record created        |
| updated_at  | DATETIME     | Yes      | Record updated        |

---

### Notes

The Address is separate from the main Facility record so location fields stay grouped together and can support map searches later.

---

`photos`

### Purpose

Stores photos associated with a facility.

---

### Relationships

- Many photos belong to one facility.

---

### Columns

| Column      | Type         | Required | Description                   |
| ----------- | ------------ | -------- | ----------------------------- |
| photo_id    | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key |
| facility_id | BIGINT UNSIGNED | Yes | References facilities |
| photo_url   | VARCHAR(500) | Yes      | Image URL                     |
| source      | VARCHAR(100) | No       | Google, Website, Social Media |
| caption     | VARCHAR(255) | No       | Optional description          |
| created_at  | DATETIME     | Yes      | Record created                |

---

### Notes

Photos can help show outdoor conditions and support a Facility's score.

---

`evidence`

### Purpose

Stores research findings that help explain a Facility's score.

---

### Relationships

- Many evidence records belong to one facility.
- Many evidence records reference one data source.

---

### Columns

| Column           | Type         | Required | Description                  |
| ---------------- | ------------ | -------- | ---------------------------- |
| evidence_id      | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key |
| facility_id      | BIGINT UNSIGNED | Yes | References facilities |
| data_source_id   | BIGINT UNSIGNED | Yes | References data_sources |
| evidence_type    | ENUM         | Yes      | Review, Image, Website, etc. |
| description      | TEXT         | Yes      | Evidence summary             |
| confidence_score | DECIMAL(5,2) | Yes      | Confidence rating            |
| collected_at     | DATETIME     | Yes      | Date collected               |

---

### Notes

Evidence makes it possible to look back and see why a Facility received its rating.

---

`data_sources`

### Purpose

Stores where imported information or Evidence came from.

---

### Relationships

- One data source may provide many evidence records.

---

### Columns

| Column           | Type         | Required | Description                                  |
| ---------------- | ------------ | -------- | -------------------------------------------- |
| data_source_id   | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key |
| source_name      | VARCHAR(100) | Yes      | Google Maps, Website, Yelp                   |
| source_type      | ENUM         | Yes      | API, Scraper, Manual, Import, User Submitted |
| base_url         | VARCHAR(500) | No       | Source website                               |
| last_imported_at | DATETIME     | No       | Last import date                             |

---

### Notes

This makes it easier to check the original source later.

---

`opportunity_scores`

### Purpose

Stores a calculated sales-opportunity score for a Facility on a specific date.

---

### Relationships

- Many opportunity score records belong to one facility.
- A facility may have multiple historical opportunity scores.

---

### Columns

| Column            | Type         | Required | Description                    |
| ----------------- | ------------ | -------- | ------------------------------ |
| score_id          | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key |
| facility_id       | BIGINT UNSIGNED | Yes | References facilities |
| opportunity_score | DECIMAL(5,2) | Yes      | Overall score                  |
| rating            | ENUM         | Yes      | High, Medium, Low              |
| confidence_score  | DECIMAL(5,2) | Yes      | Confidence score               |
| scoring_method    | VARCHAR(100) | Yes      | Method used to calculate score |
| calculated_at     | DATETIME     | Yes      | Date calculated                |

---

### Notes

New scores can be added when new Evidence is found. Older scores stay in the table.

---

## Future Entities

The following tables are ideas for later versions. Their fields may change and should not be built as part of the first database version.

`social_profiles`

### Purpose

Stores links to a facility's social media accounts.

---

### Relationships

- Many social profiles belong to one facility.

---

### Columns

| Column            | Type         | Required | Description               |
| ----------------- | ------------ | -------- | ------------------------- |
| social_profile_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key |
| facility_id       | BIGINT UNSIGNED | Yes | References facilities |
| platform          | VARCHAR(50)  | Yes      | Facebook, Instagram, etc. |
| profile_url       | VARCHAR(500) | Yes      | Profile URL               |
| created_at        | DATETIME     | Yes      | Record created            |
| updated_at        | DATETIME     | Yes      | Record updated            |

---

### Notes

A facility may have zero, one, or many social media profiles.

---

`saved_leads`

### Purpose

Stores facilities that a vendor has saved for future follow-up.

---

### Relationships

- Many saved leads reference one facility.

---

### Columns

| Column       | Type     | Required | Description           |
| ------------ | -------- | -------- | --------------------- |
| saved_lead_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | Primary Key |
| facility_id  | BIGINT UNSIGNED | Yes | References facilities |
| saved_at     | DATETIME | Yes      | Date saved            |
| notes        | TEXT     | No       | Optional vendor notes |

---

### Notes

This future table would store a simple saved list. User accounts can be connected to it when that feature is designed.

---

## 6. How the Tables Connect

This section explains the table connections in plain language.

### Facility → Facility Type

Many Facilities can use the same Facility Type.

Example:

Dogtopia → Dog Daycare

Camp Bow Wow → Dog Daycare

Both Facility records store the ID of the same “Dog Daycare” Facility Type record.

---

### Facility → Address

Each facility has one primary address.

---

### Facility → Property

Each Facility has one Property record describing its physical site.

---

### Facility → Photos

A facility may have multiple photos.

---

### Facility → Evidence

Each Facility can have many pieces of Evidence from different sources.

---

### Data Source → Evidence

Many Evidence records can point to the same Data Source.

---

### Facility → Opportunity Score

Each Facility can have several Opportunity Scores over time. The record with the newest `calculated_at` date is the current score.

## 7. Fields That May Need Indexes

An **index** helps MySQL find records faster, similar to an index in a book. Indexes use extra storage, so they should be added to fields that are searched, sorted, or joined often.

These fields are likely index candidates when the SQL is written:

### facilities

facility_name

google_place_id

status

facility_type_id

---

### addresses

city

state

postal_code

latitude

longitude

---

### evidence

facility_id

data_source_id

---

## 8. Ideas for Later Versions

These features are not part of the first database version:

### User Accounts

Allow vendors to log in.

---

### CRM Integration

Export leads to HubSpot, Salesforce, or other CRMs.

---

### Sales Notes

Allow vendors to save follow-up notes.

---

### GIS Mapping

Support maps, distance filters, and location-based searches.

---

### AI Opportunity Scoring

Automatically estimate opportunity using images, reviews, and website content.
