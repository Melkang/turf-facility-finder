# Turf Facility Finder

## Domain Model

Version: 1.0

---

## Purpose

The domain model defines every major object used by Turf Facility Finder.

These objects become:

- database tables
- API resources
- JavaScript models
- search indexes

---

## Core Domain

Vendor
│
├── Saved Lead
│
├── Search
│
└── Facility
├── Location
├── Property
├── Opportunity Score
├── Evidence
├── Contact
├── Photos
└── Data Sources

---

## Primary Entity

Facility

A Facility represents one physical business location.

Examples:

- Happy Paws Dog Daycare
- Bark Park Boarding
- Canine Adventure Center

A facility is the main object throughout the application.

Everything else belongs to it.

---

## Facility Relationships

Facility

has one

Location

has one

Property

has many

Evidence Items

has many

Photos

has many

Contacts

belongs to many

Data Sources

can belong to many

Saved Lists

---

## Vendor

Represents the customer using Turf Facility Finder.

A vendor can:

- search
- save leads
- export leads
- manage territories (future)

---

## Saved Lead

Represents a facility bookmarked by a vendor.

Stores:

- saved date
- notes
- status
- follow-up history

---

## Search

Represents a search request.

Contains:

- location
- radius
- filters
- sorting

---

## Location

Describes where a facility exists.

Includes:

- address
- city
- county
- state
- ZIP
- latitude
- longitude

---

## Property

Describes the physical characteristics.

Examples:

- estimated acreage
- outdoor space
- fencing
- parking
- visible grass
- visible dirt
- synthetic turf present

---

## Opportunity Score

Represents the estimated sales potential.

Possible values:

High

Medium

Low

Unknown

The score is calculated from evidence with reasoning and confidence level.

---

## Evidence

Evidence explains why a facility received its score.

Examples include:

Website

Customer Reviews

Google Maps

Satellite imagery

Street View

Facebook

Instagram

Photos

News articles

---

## Contact

Stores publicly available business contacts.

Examples:

Owner

Manager

Email

Phone

Website

---

## Photos

Stores references to publicly available imagery.

May include:

Facility photos

Street View

Satellite

Website images

Social media

---

## Data Source

Every piece of information has a source.

Examples:

Google Business

Business Website

County GIS

Facebook

Instagram

Yelp

Review Sites

OpenStreetMap

Government Data

---

## Future Entities

Visit

Proposal

Sales Activity

CRM Sync

Inspection

Maintenance History

Installation History

AI Recommendation

Territory

Sales Representative

Opportunity Trend

Market Analysis
