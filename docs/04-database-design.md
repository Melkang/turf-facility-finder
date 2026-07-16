# Database Design Decisions

Version: 1.1

## Purpose

This file explains why the database is organized the way it is. Use it when you need to remember the reasoning behind a table or relationship.

Turf Facility Finder helps turf vendors find animal-related facilities that may benefit from synthetic turf. The database stores information about those Facilities and the research used to evaluate them.

`05-database-schema.md` is the main file to trust for exact table names, field names, data types, and relationships.

**MVP** means the smallest useful first version of the project.

---

## Goals for the First Database Version

The database should:

- store each physical Facility location once
- keep basic Facility facts separate from calculated scores
- use one approved list of Facility Types
- keep older Opportunity Scores instead of overwriting them
- record where Evidence came from
- support searches by location
- support both imported data and manual research
- stay small enough to understand and maintain

---

## How the Data Is Organized

### One Physical Location Equals One Facility

The `facilities` table is the main table.

If a company has several locations, each location gets its own Facility record. This is important because each location can have a different Address, outdoor area, surface, Evidence, Photos, and Opportunity Score.

---

### Facts and Scores Are Kept Separate

Basic facts include:

- Facility name and status
- Facility Type
- Address and map coordinates
- Property details
- Photos
- Evidence and its Data Source

Calculated information includes:

- opportunity score
- High, Medium, or Low rating
- confidence score
- scoring method
- calculation date

Scores can change when new Evidence is found. Storing scores in `opportunity_scores` keeps those changes away from the basic Facility record and lets the project keep older scores.

---

### Property Stores the Fact; Evidence Stores the Explanation

The `properties` table stores searchable facts about the site.

Example:

```text
surface_type = Grass
```

The `evidence` table stores why that fact was entered.

Example:

```text
The business website shows a grass outdoor play yard.
```

Each Evidence record points to a Data Source, such as Google Maps, a business website, or a government directory. This makes it possible to check where the information came from later.

The Data Source stores the general provider. The Evidence record can also store the exact page:

```text
data_sources.base_url = https://happypaws.example
evidence.source_url = https://happypaws.example/services/dog-daycare
```

`evidence.source_url` is optional because a manual observation, offline record, deleted page, or API result may not have a useful URL.

---

### Old Records Are Kept When They Are Useful

A Facility should not be deleted only because it closes. Its `status` can be changed to Active, Inactive, or Closed. Keeping the record helps prevent the same closed business from being imported again.

Older Opportunity Scores are also kept. The score with the newest `calculated_at` date is the current score.

---

### Future Account Features Stay Out of the First Version

The first database version contains shared Facility research:

- Facilities
- Facility Types
- Addresses
- Properties
- Photos
- Evidence
- Data Sources
- Opportunity Scores

Vendor accounts, Saved Leads, Social Profiles, Contacts, notes, reminders, and sales activity will be designed later. Leaving them out keeps the first database focused and easier to build.

---

## Why the Data Uses Separate Tables

Separating related information into tables reduces repeated data and spelling differences. Database designers call this **normalization**.

For example, `facility_types` stores “Dog Daycare” once. Each matching Facility stores the ID for that Facility Type instead of storing another copy of the category name.

The main tables have one clear job:

- `facilities` stores the business location
- `facility_types` stores the approved categories
- `addresses` stores the street address and map coordinates
- `properties` stores facts about the physical site
- `photos` stores image links
- `evidence` stores research findings
- `data_sources` stores where research came from
- `opportunity_scores` stores calculated scores over time

---

## Relationship Overview

```text
Facility Type
└── Facilities
    ├── Address
    ├── Property
    ├── Photos
    ├── Evidence ── Data Source
    └── Opportunity Scores
```

Most records connect to one Facility.

---

## Decisions to Remember

### Decision 001: One business location gets one Facility record

Different locations can have different turf needs, so they must be stored separately.

### Decision 002: Opportunity Scores are not stored in Facilities

A score can change. Basic Facility information should not have to change with it.

### Decision 003: Property and Evidence have different jobs

Property stores the searchable fact. Evidence stores the explanation and source behind that fact.

### Decision 004: Latitude and longitude belong in Addresses

The coordinates describe where a Facility is located, so they are stored with its Address.

### Decision 005: Vendor workflow is a future feature

Saved Leads, accounts, notes, and CRM activity are not part of the first database version.

### Decision 006: MySQL creates the internal IDs

Each table uses a positive whole-number primary key such as `facility_id`.

Primary keys use:

```text
BIGINT UNSIGNED AUTO_INCREMENT
```

This means MySQL creates the next ID automatically when a record is added.

Foreign keys use:

```text
BIGINT UNSIGNED
```

A foreign key stores the ID of a related record. It does not create its own number.

For example, `facilities.facility_type_id` stores the ID of a record in `facility_types`.

IDs from outside services remain text. For example, `google_place_id` is Google's ID and is not the database's primary key.

UUIDs are not needed for the first version because all records will be created in one MySQL database. A separate public ID can be added later if public URLs need IDs that are difficult to guess.

---

## Possible Future Features

- vendor and user accounts
- Saved Leads and sales notes
- Social Profiles and Contacts
- map and distance-search tools
- AI-assisted scoring
- satellite image analysis
- automatic data imports
- website research tools
- dashboards
- CRM integrations

These ideas are not requirements for the first database version.

---

## Simple Rule to Follow

Store each fact once, record where it came from, and calculate scores separately.
