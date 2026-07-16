# Turf Facility Finder

## Domain Model

Version: 1.1

---

## Purpose

This file is a simple map of the main types of information in Turf Facility Finder and how they connect.

In database terms, these types of information are called **entities**. Most entities will become database tables later.

`05-database-schema.md` is the main file to trust if two database documents disagree. **MVP** means the smallest useful first version of the project.

---

## MVP Domain

```text
Facility Type
└── Facility
    ├── Address
    ├── Property
    ├── Photos
    ├── Evidence
    │   └── Data Source
    └── Opportunity Scores
```

`Facility` is the main entity. The other entities describe a Facility, group it by type, show where its information came from, or store its score.

---

## Facility

A Facility represents one physical business location that may be a synthetic turf sales opportunity.

Examples:

- Happy Paws Dog Daycare
- Bark Park Boarding
- Canine Adventure Center

If one company has several locations, each location gets its own Facility record. Each location can have a different address, outdoor area, surface, and opportunity score.

A Facility:

- belongs to one Facility Type
- has one Address
- has one Property record
- has many Photos
- has many Evidence records
- has many Opportunity Scores over time

---

## Facility Type

A Facility Type is a category used to group similar Facilities. Storing the category once helps avoid variations such as “Dog daycare,” “Dog Day Care,” and “Daycare for Dogs.”

Examples:

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

`Other` is used when the type is known but does not fit an approved category. `Unknown` is used when the type has not been confirmed.

One Facility Type may classify many Facilities.

---

## Address

An Address stores where a Facility is located. It also stores the latitude and longitude needed for map and distance searches.

It includes:

- street
- city
- state
- postal code
- country
- latitude
- longitude

Each Facility has one Address in the MVP.

---

## Property

A Property stores facts about the physical site that may affect whether turf would be useful.

It includes:

- estimated outdoor play-area size
- surface type
- whether synthetic turf is already present
- whether the area is fenced
- whether parking is available
- whether outdoor space exists

Each Facility has one Property record in the first version. Put the fact in Property—for example, `surface_type = Grass`. Put where that fact came from in Evidence—for example, “The business website shows a grass play yard.”

---

## Photo

A Photo stores the web address of a publicly available image connected to a Facility. The database stores the image link, not necessarily the image file itself.

Examples include:

- facility photos
- satellite imagery
- Street View imagery
- business website images

A Facility may have many Photos.

---

## Evidence

Evidence stores a short explanation of what was found and where it was found. It helps explain why a Facility received a particular score.

Examples include:

- reviews mentioning a muddy yard
- a website describing an outdoor play area
- imagery showing worn grass
- public records describing a property expansion

Each Evidence record belongs to one Facility and points to one Data Source. One Facility can have many pieces of Evidence.

---

## Data Source

A Data Source records where information came from.

Examples include:

- Google Maps
- a business website
- OpenStreetMap
- a government directory
- a manual research import

Many Evidence records can point to the same Data Source. For example, several pieces of Evidence may come from Google Maps.

---

## Opportunity Score

An Opportunity Score stores the calculated sales potential of a Facility on a specific date.

It includes:

- numeric opportunity score
- rating of High, Medium, or Low
- confidence score
- scoring method
- calculation date

A Facility can have several scores over time. Keeping the older scores makes it possible to see how the Facility changed. The newest score is the current score.

---

## Future Domain

The following ideas are not part of the first database version. They can be designed later:

- Vendor and user accounts
- Saved Leads
- Social Profiles
- Contacts
- Search history
- Visits
- Proposals
- Sales Activity
- CRM synchronization
- Inspections
- Maintenance and installation history
- AI recommendations
- Territories and sales representatives
- Opportunity trends and market analysis

Leaving these out for now keeps the first database smaller and easier to build.
