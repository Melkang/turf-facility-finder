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
- `INT UNSIGNED` stores zero or a positive whole number.
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
| status | ENUM | Yes | Active | Active, Temporarily Closed, Permanently Closed, or Unknown |
| last_verified_at | DATETIME | No | 2026-07-12 14:30:00 | Most recent verification date |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |
| updated_at | DATETIME | Yes | 2026-07-12 14:30:00 | Most recent record update |

Facility status meanings:

- `Active`: the Facility is currently operating
- `Temporarily Closed`: the Facility is expected to reopen
- `Permanently Closed`: the Facility is no longer operating
- `Unknown`: the operating status has not been confirmed

When `google_place_id` is present, it must be unique so the same Google Maps location cannot be attached to two Facility records.

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

Each `type_name` must be unique so an approved Facility Type is stored only once.

---

## Entity: Property (`properties`)

Stores facts about the Facility's physical site, such as its surface and animal activity or play area.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| property_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `501` | Database-generated identifier for the Property record |
| facility_id | BIGINT UNSIGNED | Yes | `1042` | ID of the related Facility |
| estimated_play_area_sqft | INT UNSIGNED | No | 12000 | Estimated animal activity or play-area size in square feet |
| surface_type | ENUM | Yes | Grass | Grass, Dirt, Mulch, Gravel, Concrete, Synthetic Turf, Mixed, Other, or Unknown |
| synthetic_turf_present | BOOLEAN | No | FALSE | Whether synthetic turf is currently installed |
| fenced_area | BOOLEAN | No | TRUE | Whether an outdoor area appears fenced |
| parking_available | BOOLEAN | No | TRUE | Whether customer parking appears available |
| outdoor_space | BOOLEAN | Yes | TRUE | Whether an outdoor area exists |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |
| updated_at | DATETIME | Yes | 2026-07-12 14:30:00 | Most recent record update |

A Facility may have no Property record while the site is still being researched. If a Property record exists, it must belong to one Facility, and its `facility_id` must be unique.

`outdoor_space` is required when a Property record is created. The other Boolean fields are optional: `TRUE` means the feature is present, `FALSE` means it is not present, and `NULL` means it has not been confirmed.

Surface Type meanings:

- `Mixed`: two or more approved surface types are present; Evidence should explain the combination
- `Other`: the surface is known but does not fit an approved value
- `Unknown`: the surface has not been confirmed

Surface Type is required when a Property record exists. The future SQL uses `Unknown` as its default value. The play-area size may be `NULL` when it has not been estimated.

---

## Entity: Address (`addresses`)

Stores the physical location and coordinates of a Facility.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| address_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `880` | Database-generated identifier for the Address |
| facility_id | BIGINT UNSIGNED | Yes | `1042` | ID of the related Facility |
| street | VARCHAR(255) | No | 123 Main St | Street address, when one is available |
| city | VARCHAR(100) | Yes | Lexington | City |
| state | VARCHAR(100) | Yes | Kentucky | State or region |
| postal_code | VARCHAR(20) | No | 40502 | ZIP or postal code, when known |
| country | VARCHAR(100) | Yes | United States | Country |
| latitude | DECIMAL(9,6) | Yes | 38.040584 | Latitude coordinate |
| longitude | DECIMAL(9,6) | Yes | -84.503716 | Longitude coordinate |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |
| updated_at | DATETIME | Yes | 2026-07-12 14:30:00 | Most recent record update |

A Facility may have no Address while its location is being researched. If an Address exists, it must belong to one Facility, and its `facility_id` must be unique.

City, state, country, latitude, and longitude are required before an Address record is added. Street and postal code are optional because some locations do not have a normal mailing address.

---

## Entity: Photo (`photos`)

Stores references to publicly available imagery associated with a Facility.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| photo_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `3201` | Database-generated identifier for the Photo |
| facility_id | BIGINT UNSIGNED | Yes | `1042` | ID of the related Facility |
| photo_url | VARCHAR(500) | Yes | <https://images.example/play-yard.jpg> | Image location |
| data_source_id | BIGINT UNSIGNED | Yes | `2` | ID of the related Data Source |
| caption | VARCHAR(255) | No | Rear outdoor play yard | Optional image description |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |

Each Photo points to one Data Source. The Data Source stores the provider and how the image information was collected.

---

## Entity: Evidence (`evidence`)

Stores what was found during research and helps explain a Facility's score.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| evidence_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `7401` | Database-generated identifier for the Evidence record |
| facility_id | BIGINT UNSIGNED | Yes | `1042` | ID of the related Facility |
| data_source_id | BIGINT UNSIGNED | Yes | `2` | ID of the related Data Source |
| evidence_type | ENUM | Yes | Review | Business Listing, Website, Review, Photo, Satellite Image, Street View, Social Media Post, News Article, Public Record, Manual Observation, or Other |
| description | TEXT | Yes | Reviews mention a muddy yard | Evidence summary |
| source_url | VARCHAR(1000) | No | <https://happypaws.example/services/dog-daycare> | Exact page, review, image, or record supporting the Evidence |
| confidence_score | DECIMAL(5,2) | Yes | 85.00 | Confidence rating |
| collected_at | DATETIME | Yes | 2026-07-09 10:00:00 | Collection date |

`source_url` points to the exact item used as Evidence. It is optional because not every source has a useful web address.

Evidence Type describes what kind of supporting item was found. Use `Other` only when the item is known but does not fit an approved Evidence Type. Do not create an Evidence record until its type is known.

---

## Entity: Data Source (`data_sources`)

Stores where imported information, Photos, or Evidence came from.

| Field | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data_source_id | BIGINT UNSIGNED AUTO_INCREMENT | Yes | `2` | Database-generated identifier for the Data Source |
| source_name | VARCHAR(100) | Yes | Google Maps | Source name |
| collection_method | ENUM | Yes | API | API, Web Scrape, Manual Research, File Import, User Submitted, or Other |
| base_url | VARCHAR(500) | No | <https://maps.google.com> | General website for the Data Source |
| last_imported_at | DATETIME | No | 2026-07-09 10:00:00 | Most recent import date |
| created_at | DATETIME | Yes | 2026-07-09 10:00:00 | Record creation date |
| updated_at | DATETIME | Yes | 2026-07-12 14:30:00 | Most recent record update |

Collection Method describes how the information entered the database:

- `API`: received through a service's API
- `Web Scrape`: collected automatically from a webpage
- `Manual Research`: entered by a person researching a Facility
- `File Import`: loaded from a CSV, spreadsheet, or other dataset
- `User Submitted`: provided through a future user-facing form
- `Other`: entered through another known method

The combination of `source_name` and `collection_method` must be unique. This allows the same provider to be recorded with different Collection Methods without creating duplicate rows for the same method.

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

If a Facility has not been scored, it has no Opportunity Score record. The rating list therefore contains only High, Medium, and Low.

Latitude, longitude, Opportunity Scores, and confidence scores have limits:

- latitude is from `-90` through `90`
- longitude is from `-180` through `180`
- Opportunity Scores are from `0` through `100`
- confidence scores are from `0` through `100`

---

## Future Entities

The following features are not part of the first database version, so their fields are not final:

- Vendors and user accounts
- Saved Leads
- Social Profiles
- Contacts
- Sales notes and follow-up activity

Their tables and fields will be designed when work begins on those features.
