# Turf Facility Finder

## Data Dictionary

Version: 1.1

---

## Purpose

Use this file to look up what each database field means, what type of value it stores, and whether it is required.

`05-database-schema.md` is the main file to trust if two database documents disagree. This dictionary uses the same names and data types as that schema.

## How to Read the Tables

- **Field** is the column name used in the database.
- **Type** controls what kind of value the field can store.
- **Required** tells you whether every record must have a value.
- **Example** shows what a value might look like.
- **Description** explains why the field exists.

Common data types:

- `BIGINT UNSIGNED AUTO_INCREMENT` is a positive whole-number ID created automatically by MySQL.
- `BIGINT UNSIGNED` is a positive whole-number ID that points to a record in another table.
- `VARCHAR(255)` stores short text, with the maximum length shown in parentheses.
- `TEXT` stores longer text.
- `INTEGER` stores a whole number.
- `DECIMAL(5,2)` stores a number with two decimal places, such as `87.50`.
- `BOOLEAN` stores true or false.
- `DATETIME` stores a date and time.
- `ENUM` limits a field to a list of allowed choices.

---

## Entity: Facility (`facilities`)

Represents one physical business location that may be a synthetic turf sales opportunity.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| facility_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `1042` | Database-generated identifier for the Facility |
| facility_name | VARCHAR(255) | Yes | Happy Paws Dog Daycare | Business name |
| facility_type_id | BIGINT UNSIGNED | Yes | `1` | ID of the related record in `facility_types` |
| primary_website | VARCHAR(500) | No | <https://happypaws.example> | Official website |
| primary_phone | VARCHAR(30) | No | (555) 555-1234 | Public business phone number |
| google_place_id | VARCHAR(255) | No | `ChIJ...` | Google Maps Place ID |
| status | ENUM | Yes | Active | Active, Inactive, or Closed |
| last_verified_at | DATETIME | No | 2026-07-12 14:30:00 | Most recent verification date |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |
| updated_at | DATETIME | Yes | 2026-07-12 14:30:00 | Most recent record update |

---

## Entity: Facility Type (`facility_types`)

Stores the approved list of Facility categories. This prevents the same category from being entered with different spellings.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| facility_type_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `1` | Database-generated identifier for the Facility Type |
| type_name | VARCHAR(100) | Yes | Dog Daycare | Standard category name |
| description | TEXT | No | Daytime care facility for dogs | Optional category explanation |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |
| updated_at | DATETIME | Yes | 2026-07-12 14:30:00 | Most recent record update |

Approved values for the first version:

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

Use `Other` when the type is known but does not fit an approved category. Use `Unknown` when the type has not been confirmed.

---

## Entity: Property (`properties`)

Stores facts about the Facility's physical site, such as its surface and outdoor play area.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| property_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `501` | Database-generated identifier for the Property record |
| facility_id | BIGINT UNSIGNED | Yes | `1042` | ID of the related Facility |
| estimated_play_area_sqft | INTEGER | No | 12000 | Estimated outdoor activity-area size |
| surface_type | ENUM | No | Grass | Grass, Dirt, Mixed, Gravel, Synthetic Turf, Concrete, or Unknown |
| synthetic_turf_present | BOOLEAN | No | FALSE | Whether synthetic turf is currently installed |
| fenced_area | BOOLEAN | No | TRUE | Whether an outdoor area appears fenced |
| parking_available | BOOLEAN | No | TRUE | Whether customer parking appears available |
| outdoor_space | BOOLEAN | Yes | TRUE | Whether an outdoor area exists |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |
| updated_at | DATETIME | Yes | 2026-07-12 14:30:00 | Most recent record update |

---

## Entity: Address (`addresses`)

Stores the physical location and coordinates of a Facility.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| address_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `880` | Database-generated identifier for the Address |
| facility_id | BIGINT UNSIGNED | Yes | `1042` | ID of the related Facility |
| street | VARCHAR(255) | Yes | 123 Main St | Street address |
| city | VARCHAR(100) | Yes | Lexington | City |
| state | VARCHAR(100) | Yes | Kentucky | State or region |
| postal_code | VARCHAR(20) | Yes | 40502 | ZIP or postal code |
| country | VARCHAR(100) | Yes | United States | Country |
| latitude | DECIMAL(9,6) | Yes | 38.040584 | Latitude coordinate |
| longitude | DECIMAL(9,6) | Yes | -84.503716 | Longitude coordinate |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |
| updated_at | DATETIME | Yes | 2026-07-12 14:30:00 | Most recent record update |

---

## Entity: Photo (`photos`)

Stores references to publicly available imagery associated with a Facility.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| photo_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `3201` | Database-generated identifier for the Photo |
| facility_id | BIGINT UNSIGNED | Yes | `1042` | ID of the related Facility |
| photo_url | VARCHAR(500) | Yes | <https://images.example/play-yard.jpg> | Image location |
| source | VARCHAR(100) | No | Business Website | Human-readable image source |
| caption | VARCHAR(255) | No | Rear outdoor play yard | Optional image description |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |

---

## Entity: Evidence (`evidence`)

Stores what was found during research and helps explain a Facility's score.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| evidence_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `7401` | Database-generated identifier for the Evidence record |
| facility_id | BIGINT UNSIGNED | Yes | `1042` | ID of the related Facility |
| data_source_id | BIGINT UNSIGNED | Yes | `2` | ID of the related Data Source |
| evidence_type | ENUM | Yes | Review | Evidence category such as Review, Image, or Website |
| description | TEXT | Yes | Reviews mention a muddy yard | Evidence summary |
| source_url | VARCHAR(1000) | No | <https://happypaws.example/services/dog-daycare> | Exact page, review, image, or record supporting the Evidence |
| confidence_score | DECIMAL(5,2) | Yes | 85.00 | Confidence rating |
| collected_at | DATETIME | Yes | 2026-07-09 10:00:00 | Collection date |

`source_url` points to the exact item used as Evidence. It is optional because not every source has a useful web address.

---

## Entity: Data Source (`data_sources`)

Stores where imported information or Evidence came from.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data_source_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `2` | Database-generated identifier for the Data Source |
| source_name | VARCHAR(100) | Yes | Google Maps | Source name |
| source_type | ENUM | Yes | API | API, Scraper, Manual, Import, or User Submitted |
| base_url | VARCHAR(500) | No | <https://maps.google.com> | General website for the Data Source |
| last_imported_at | DATETIME | No | 2026-07-09 10:00:00 | Most recent import date |

---

## Entity: Opportunity Score (`opportunity_scores`)

Stores a Facility's calculated sales-opportunity score on a specific date.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| score_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `9101` | Database-generated identifier for the Opportunity Score |
| facility_id | BIGINT UNSIGNED | Yes | `1042` | ID of the related Facility |
| opportunity_score | DECIMAL(5,2) | Yes | 87.50 | Overall numeric score |
| rating | ENUM | Yes | High | High, Medium, or Low |
| confidence_score | DECIMAL(5,2) | Yes | 92.00 | Confidence in the calculation |
| scoring_method | VARCHAR(100) | Yes | Rules Engine v1 | Method used to calculate the score |
| calculated_at | DATETIME | Yes | 2026-07-09 10:00:00 | Calculation date |

A Facility may have multiple score records. The most recently calculated record is its current score.

---

## Future Entities

The following features are not part of the first database version, so their fields are not final:

- Vendors and user accounts
- Saved Leads
- Social Profiles
- Contacts
- Sales notes and follow-up activity

Their tables and fields will be designed when work begins on those features.
