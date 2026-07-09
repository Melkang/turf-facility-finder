# Turf Facility Finder
## Data Dictionary

Version: 1.0

---

# Purpose

The Data Dictionary defines every data field used by Turf Facility Finder.

Each field includes:

- Name
- Description
- Data Type
- Required?
- Example
- Notes

This document serves as the source of truth before creating the database schema.

---

# Entity: Facility

Represents a single physical business location that may be a sales opportunity.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| facility_id | UUID | Yes | `fac_8f32a...` | Unique identifier for the facility |
| business_name | VARCHAR(150) | Yes | Happy Paws Dog Daycare | Official business name |
| facility_type | ENUM | Yes | Dog Daycare | Primary facility classification |
| business_status | ENUM | Yes | Active | Operational status |
| website_url | VARCHAR(255) | No | https://happypaws.com | Official website |
| phone_number | VARCHAR(25) | No | (555) 555-1234 | Public phone number |
| email | VARCHAR(150) | No | info@happypaws.com | Public email |
| description | TEXT | No | Indoor and outdoor dog daycare... | Business description |
| created_at | DATETIME | Yes | 2026-07-09 | Record created |
| updated_at | DATETIME | Yes | 2026-07-12 | Last updated |

---

# Entity: Location

Stores the geographic location of a facility.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| location_id | UUID | Yes | loc_12345 | Unique identifier |
| facility_id | UUID | Yes | fac_8f32a | Related facility |
| street_address | VARCHAR(255) | Yes | 123 Main St | Street address |
| city | VARCHAR(100) | Yes | Lexington | City |
| state | CHAR(2) | Yes | KY | State abbreviation |
| postal_code | VARCHAR(15) | Yes | 40502 | ZIP code |
| county | VARCHAR(100) | No | Fayette | County |
| latitude | DECIMAL(10,7) | Yes | 38.040584 | Latitude |
| longitude | DECIMAL(10,7) | Yes | -84.503716 | Longitude |

---

# Entity: Property

Describes the physical property.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| property_id | UUID | Yes | prop_201 | Unique identifier |
| facility_id | UUID | Yes | fac_8f32a | Related facility |
| estimated_property_size_sqft | INTEGER | No | 35000 | Estimated property size |
| estimated_play_area_sqft | INTEGER | No | 12000 | Outdoor animal area |
| fenced_area | BOOLEAN | No | TRUE | Fence detected |
| parking_available | BOOLEAN | No | TRUE | Parking visible |
| outdoor_space | BOOLEAN | Yes | TRUE | Outdoor area exists |
| current_surface | ENUM | No | Grass | Primary surface |
| synthetic_turf_present | BOOLEAN | No | FALSE | Existing turf detected |

---

# Entity: Opportunity Assessment

Represents how promising the facility is.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| assessment_id | UUID | Yes | assess_1 | Unique identifier |
| facility_id | UUID | Yes | fac_8f32a | Related facility |
| opportunity_score | ENUM | Yes | High | Lead ranking |
| confidence_score | INTEGER | Yes | 92 | 0–100 confidence |
| assessment_date | DATETIME | Yes | 2026-07-09 | Last calculation |
| assessment_method | ENUM | Yes | Rules Engine | How score was generated |
| notes | TEXT | No | Large worn grass area | Explanation |

---

# Entity: Evidence

Evidence supporting the opportunity score.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| evidence_id | UUID | Yes | ev_1001 | Unique identifier |
| facility_id | UUID | Yes | fac_8f32a | Related facility |
| evidence_type | ENUM | Yes | Review | Source type |
| source_name | VARCHAR(100) | Yes | Google Reviews | Source |
| evidence_summary | TEXT | Yes | Reviews mention muddy yard | Summary |
| source_url | VARCHAR(255) | No | https://... | Original source |
| confidence | INTEGER | Yes | 85 | Reliability score |
| collected_at | DATETIME | Yes | 2026-07-09 | Collection date |

---

# Entity: Contact

Public business contacts.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| contact_id | UUID | Yes | contact_1 | Unique identifier |
| facility_id | UUID | Yes | fac_8f32a | Related facility |
| contact_name | VARCHAR(150) | No | Sarah Smith | Public contact |
| contact_title | VARCHAR(100) | No | Owner | Position |
| phone | VARCHAR(25) | No | (555) 123-4567 | Phone |
| email | VARCHAR(150) | No | owner@business.com | Email |

---

# Entity: Photo

References publicly available imagery.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| photo_id | UUID | Yes | photo_123 | Unique identifier |
| facility_id | UUID | Yes | fac_8f32a | Related facility |
| photo_type | ENUM | Yes | Satellite | Image source |
| image_url | VARCHAR(255) | Yes | https://... | Image location |
| caption | VARCHAR(255) | No | Rear play yard | Description |
| collected_at | DATETIME | Yes | 2026-07-09 | Collection date |

---

# Entity: Data Source

Tracks where information originated.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| source_id | UUID | Yes | src_12 | Unique identifier |
| facility_id | UUID | Yes | fac_8f32a | Related facility |
| source_type | ENUM | Yes | Google Business | Source category |
| source_url | VARCHAR(255) | No | https://... | Original URL |
| last_checked | DATETIME | Yes | 2026-07-09 | Last verified |

---

# Entity: Vendor

Represents a Turf Facility Finder customer.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| vendor_id | UUID | Yes | vendor_55 | Unique identifier |
| company_name | VARCHAR(150) | Yes | ABC Turf | Vendor business |
| contact_name | VARCHAR(150) | Yes | John Doe | Primary contact |
| email | VARCHAR(150) | Yes | john@abcturf.com | Login email |
| phone | VARCHAR(25) | No | (555) 123-1234 | Contact phone |

---

# Entity: Saved Lead

Facilities saved by vendors.

| Field | Type | Required | Example | Description |
|---------|------|----------|---------|-------------|
| saved_lead_id | UUID | Yes | save_101 | Unique identifier |
| vendor_id | UUID | Yes | vendor_55 | Vendor |
| facility_id | UUID | Yes | fac_8f32a | Saved facility |
| saved_at | DATETIME | Yes | 2026-07-09 | Date saved |
| status | ENUM | Yes | New | Lead stage |
| notes | TEXT | No | Call next week | Vendor notes |

---

# Common Enumerations

## Facility Type

- Dog Daycare
- Dog Boarding
- Kennel
- Dog Training Center
- Canine Sports Facility
- Animal Shelter
- Animal Rescue
- Adoption Center
- Pet Resort
- Mixed Animal Facility
- Dog Park

---

## Business Status

- Active
- Permanently Closed
- Temporarily Closed
- Unknown

---

## Surface Type

- Grass
- Dirt
- Mixed
- Gravel
- Synthetic Turf
- Concrete
- Unknown

---

## Opportunity Score

- High
- Medium
- Low
- Unknown

---

## Evidence Type

- Website
- Google Business
- Google Review
- Facebook
- Instagram
- Satellite
- Street View
- News
- Public Records
- AI Observation

---

## Saved Lead Status

- New
- Contacted
- Qualified
- Proposal Sent
- Won
- Lost
- Archived