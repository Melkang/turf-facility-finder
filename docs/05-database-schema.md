# Turf Facility Finder

## Database Schema

Version: 1.0
Status: Draft

---

## 1. Purpose

This document describes the logical database schema used by Turf Facility Finder.

The application is designed as a lead generation platform for synthetic turf vendors by identifying animal-related facilities that may benefit from synthetic turf installation, replacement, or maintenance.

This document defines:

- database tables
- primary keys
- foreign keys
- relationships
- normalization decisions
- indexing strategy
- future scalability considerations

This is a logical schema rather than a database-specific implementation.

Physical SQL scripts will be created later after the schema has been reviewed.

---

## 2. Database Design Philosophy

The database is designed around several goals.

• Store each facility only once.

• Allow multiple evidence sources to support each facility.

• Preserve historical information instead of deleting records.

• Separate searchable business information from supporting metadata.

• Support future GIS (map) functionality.

• Support automated data imports from APIs and web scraping.

• Support manual review and editing.

• Minimize duplicated information.

The design follows Third Normal Form (3NF) wherever practical.

---

## 3. Naming Standards

The database follows consistent naming conventions to improve readability, maintainability, and collaboration.

### Tables

- Use plural nouns
- snake_case
- lowercase

Examples

facilities
facility_types
social_profiles
saved_leads

### Primary Keys

Every table uses

id

as the primary key.

### Foreign Keys

Foreign keys reference the parent table name.

facility_id
facility_type_id
address_id
source_id

### Date Fields

created_at
updated_at
last_verified_at
deleted_at

### Boolean Fields

is_active
is_verified
is_closed
is_manual_override

## 4. Entity Relationship Overview

Facility
│
├── Facility Types
│
├── Addresses
│
├── Websites
│
├── Social Profiles
│
├── Photos
│
├── Opportunity Scores
│
├── Evidence
│
├── Data Sources
│
└── Saved Leads

## 5. Table Definitions

---

`facilities`

### Purpose

Stores the primary record for every animal-related facility that may represent a synthetic turf sales opportunity.

---

### Relationships

- Belongs to one **facility_type**
- Has one **address**
- Has many **social_profiles**
- Has many **photos**
- Has many **evidence** records
- Has one **opportunity_score**
- Can appear in many **saved_leads**

---

### Columns

| Column                   | Type         | Required | Description                 |
| ------------------------ | ------------ | -------- | --------------------------- |
| id                       | UUID         | Yes      | Primary Key                 |
| facility_name            | VARCHAR(255) | Yes      | Business name               |
| facility_type_id         | UUID         | Yes      | References facility_types   |
| primary_address_id       | UUID         | Yes      | References addresses        |
| primary_website          | VARCHAR(500) | No       | Official website            |
| primary_phone            | VARCHAR(30)  | No       | Business phone number       |
| google_place_id          | VARCHAR(255) | No       | Google Maps Place ID        |
| latitude                 | DECIMAL(9,6) | Yes      | Latitude coordinate         |
| longitude                | DECIMAL(9,6) | Yes      | Longitude coordinate        |
| estimated_play_area_sqft | INTEGER      | No       | Estimated outdoor play area |
| surface_type             | ENUM         | No       | Grass, Turf, Mixed, Unknown |
| opportunity_rating       | ENUM         | Yes      | High, Medium, Low           |
| evidence_score           | DECIMAL(5,2) | Yes      | Confidence score            |
| status                   | ENUM         | Yes      | Active, Inactive, Closed    |
| last_verified_at         | DATETIME     | No       | Last verification date      |
| created_at               | DATETIME     | Yes      | Record created              |
| updated_at               | DATETIME     | Yes      | Record updated              |

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
- Dog Park
- Animal Shelter
- Veterinary Clinic
- Training Facility

---

### Relationships

- One facility type can have many facilities.

---

### Columns

| Column      | Type         | Required | Description          |
| ----------- | ------------ | -------- | -------------------- |
| id          | UUID         | Yes      | Primary Key          |
| type_name   | VARCHAR(100) | Yes      | Facility category    |
| description | TEXT         | No       | Optional description |
| created_at  | DATETIME     | Yes      | Record created       |
| updated_at  | DATETIME     | Yes      | Record updated       |

---

### Notes

Stores standardized facility categories to reduce duplicate data.

---

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
| id          | UUID         | Yes      | Primary Key           |
| facility_id | UUID         | Yes      | References facilities |
| street      | VARCHAR(255) | Yes      | Street address        |
| city        | VARCHAR(100) | Yes      | City                  |
| state       | VARCHAR(100) | Yes      | State                 |
| postal_code | VARCHAR(20)  | Yes      | ZIP or Postal Code    |
| country     | VARCHAR(100) | Yes      | Country               |
| created_at  | DATETIME     | Yes      | Record created        |
| updated_at  | DATETIME     | Yes      | Record updated        |

---

### Notes

Keeping addresses in their own table allows future support for multiple locations if needed.

---

`social_profiles`

### Purpose

Stores links to a facility's social media accounts.

---

### Relationships

- Many social profiles belong to one facility.

---

### Columns

| Column      | Type         | Required | Description               |
| ----------- | ------------ | -------- | ------------------------- |
| id          | UUID         | Yes      | Primary Key               |
| facility_id | UUID         | Yes      | References facilities     |
| platform    | VARCHAR(50)  | Yes      | Facebook, Instagram, etc. |
| profile_url | VARCHAR(500) | Yes      | Profile URL               |
| created_at  | DATETIME     | Yes      | Record created            |
| updated_at  | DATETIME     | Yes      | Record updated            |

---

### Notes

A facility may have zero, one, or many social media profiles.

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
| id          | UUID         | Yes      | Primary Key                   |
| facility_id | UUID         | Yes      | References facilities         |
| photo_url   | VARCHAR(500) | Yes      | Image URL                     |
| source      | VARCHAR(100) | No       | Google, Website, Social Media |
| caption     | VARCHAR(255) | No       | Optional description          |
| created_at  | DATETIME     | Yes      | Record created                |

---

### Notes

Multiple photos help estimate outdoor conditions and support opportunity scoring.

---

`evidence`

### Purpose

Stores supporting evidence used to evaluate a sales opportunity.

---

### Relationships

- Many evidence records belong to one facility.
- Many evidence records reference one data source.

---

### Columns

| Column           | Type         | Required | Description                  |
| ---------------- | ------------ | -------- | ---------------------------- |
| id               | UUID         | Yes      | Primary Key                  |
| facility_id      | UUID         | Yes      | References facilities        |
| source_id        | UUID         | Yes      | References data_sources      |
| evidence_type    | VARCHAR(100) | Yes      | Review, Image, Website, etc. |
| description      | TEXT         | Yes      | Evidence summary             |
| confidence_score | DECIMAL(5,2) | Yes      | Confidence rating            |
| collected_at     | DATETIME     | Yes      | Date collected               |

---

### Notes

Evidence provides transparency for why a facility received its opportunity rating.

---

`data_sources`

### Purpose

Stores the origin of imported facility data.

---

### Relationships

- One data source may provide many evidence records.

---

### Columns

| Column           | Type         | Required | Description                |
| ---------------- | ------------ | -------- | -------------------------- |
| id               | UUID         | Yes      | Primary Key                |
| source_name      | VARCHAR(100) | Yes      | Google Maps, Website, Yelp |
| source_type      | VARCHAR(50)  | Yes      | API, Scraper, Manual       |
| base_url         | VARCHAR(500) | No       | Source website             |
| last_imported_at | DATETIME     | No       | Last import date           |

---

### Notes

Allows tracking of where every piece of information originated.

---

`opportunity_scores`

### Purpose

Stores the calculated opportunity score for each facility.

---

### Relationships

- One opportunity score belongs to one facility.

---

### Columns

| Column        | Type         | Required | Description           |
| ------------- | ------------ | -------- | --------------------- |
| id            | UUID         | Yes      | Primary Key           |
| facility_id   | UUID         | Yes      | References facilities |
| score         | DECIMAL(5,2) | Yes      | Overall score         |
| rating        | ENUM         | Yes      | High, Medium, Low     |
| calculated_at | DATETIME     | Yes      | Date calculated       |

---

### Notes

Scores may be recalculated as new evidence becomes available.

---

`saved_leads`

### Purpose

Stores facilities that a vendor has saved for future follow-up.

---

### Relationships

- Many saved leads reference one facility.

---

### Columns

| Column      | Type     | Required | Description           |
| ----------- | -------- | -------- | --------------------- |
| id          | UUID     | Yes      | Primary Key           |
| facility_id | UUID     | Yes      | References facilities |
| saved_at    | DATETIME | Yes      | Date saved            |
| notes       | TEXT     | No       | Optional vendor notes |

---

### Notes

Initially supports a simple saved list. Future versions may associate saved leads with individual user accounts.

---

## 6. Relationship Rules

The database follows these relationship rules.

### Facility → Facility Type

Many facilities may belong to one facility type.

Example:

Dogtopia → Dog Daycare

Camp Bow Wow → Dog Daycare

Both reference the same Facility Type record.

---

### Facility → Address

Each facility has one primary address.

---

### Facility → Social Profiles

A facility may have zero or many social media profiles.

Examples:

Facebook

Instagram

LinkedIn

TikTok

---

### Facility → Photos

A facility may have multiple photos.

---

### Facility → Evidence

Each facility may have many pieces of evidence collected from different sources.

---

### Facility → Opportunity Score

Each facility has one current opportunity score.

## 7. Indexing Strategies

Indexes improve search performance.

The following columns should be indexed.

### facilities

facility_name

google_place_id

latitude

longitude

status

facility_type_id

---

### addresses

city

state

postal_code

---

### evidence

facility_id

source_id

---

### social_profiles

facility_id

platform

---

## 8. Future Expansion Thoughts

The current schema supports the MVP.

Future versions may include:

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

Support map overlays and spatial searches.

---

### AI Opportunity Scoring

Automatically estimate opportunity using images, reviews, and website content.
