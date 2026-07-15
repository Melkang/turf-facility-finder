# Database Design (Architecture Decision Record)

## Purpose

The Turf Facility Finder database is designed to support a lead generation platform for synthetic turf vendors.

The primary goal of the database is to identify, organize, analyze, and surface potential sales opportunities for businesses that maintain outdoor animal activity areas where synthetic turf may provide operational, hygienic, or maintenance benefits.

Rather than functioning as a traditional customer relationship management (CRM) system, the database serves as an opportunity discovery engine. Vendors interact with analyzed opportunities while the underlying facility data remains protected as the system's source of truth.

---

### Design Goals

The database was designed around the following principles:

• Maintain a single source of truth for imported facility information.

• Separate factual data from calculated opportunity analysis.

• Separate shared platform data from vendor-specific data.

• Normalize repetitive information to improve consistency.

• Preserve historical records instead of deleting them.

• Support multiple external data sources.

• Allow future expansion without significant schema redesign.

• Keep the design understandable for both technical and non-technical stakeholders.

---

## Core Design Principles

### Source of Truth

Facility information originates from trusted external sources.

Examples include:

• Google Places
• OpenStreetMap
• Business websites
• Government business directories
• Animal organization directories

Imported information should not be directly editable by vendors.

---

### Read-Only Master Dataset

The Facilities table represents the platform's master dataset.

Users can:

• Save facilities
• Add notes
• Track follow-ups

Users cannot directly modify imported facility records.

Corrections should be stored separately for administrative review.

---

### Facts vs Analysis

The database separates observable facts from calculated opportunity scores.

Examples of factual information include:

• Facility name
• Address
• Website
• Facility type
• Geographic location

Examples of analytical information include:

• Opportunity score
• Turf suitability
• Estimated installation potential
• Confidence score
• Recommended sales priority

Separating these concerns allows scoring algorithms to improve without modifying historical facility data.

---

### Shared Data vs Vendor Data

Platform data is shared among all vendors.

Examples include:

• Facilities
• Evidence
• Facility Types

Vendor-specific data includes:

• Saved leads
• Personal notes
• Follow-up reminders
• Sales status

This prevents one vendor's activity from affecting another vendor's workspace.

---

### Soft Deletes

Facilities should never be permanently removed.

Instead, records receive status values such as:

• Active
• Temporarily Closed
• Permanently Closed
• Unknown

Historical records provide reporting value and prevent duplicate imports.

---

### Data Quality

Every imported record should include metadata describing:

• Original source

• Last verification date

• Import timestamp

• Confidence level

These fields improve trust in the dataset and simplify maintenance.

---

## Normalization Strategy

The database follows Third Normal Form (3NF) where practical.

Large repeating values are stored once and referenced through foreign keys.

Examples include:

Facility Types

instead of repeatedly storing

Dog Daycare
Dog Park
Boarding Facility
Veterinary Clinic

throughout thousands of records.

This reduces duplication, improves consistency, and simplifies future updates.

---

## High-Level Entity Relationships

FacilityTypes

↓

Facilities

├── Evidence

├── SocialProfiles

├── OpportunityScores

├── SavedLeads

└── LeadNotes

Facilities serve as the central entity within the application.

Most business information ultimately relates back to a single facility.

---

## Future Scalability

The architecture anticipates future features including:

• AI-assisted opportunity scoring

• Satellite imagery analysis

• Multiple import pipelines

• Automated website crawling

• User accounts

• Analytics dashboards

The database is intentionally designed to accommodate these features without requiring major structural changes.

---

## Database Philosophy

The Turf Facility Finder database is designed around one guiding principle:

Store reliable facts once.

Build intelligent analysis on top of those facts.

Allow vendors to personalize their workflow without compromising the integrity of the shared dataset.

### Key Architecture Decisions

### Decision 001

One business location equals one Facility record.

Reason:
The application identifies opportunities at the physical location level rather than the company level. A business with multiple locations may have different outdoor conditions, opportunity scores, and supporting evidence at each location.

---

### Decision 002

Opportunity Scores are stored separately from Facilities.

Reason:
Opportunity scores are analytical values that change over time, while facility information represents factual imported data.

---

### Decision 003

Vendor data is separated from shared platform data.

Reason:
Each vendor maintains independent saved leads, notes, and sales activities while sharing the same underlying facility dataset.
