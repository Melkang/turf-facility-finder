# Turf Facility Finder

Turf Facility Finder is a lead-discovery project for synthetic turf vendors.

The application is intended to help vendors find animal-related facilities that may benefit from synthetic turf installation, replacement, expansion, or maintenance.

Facilities can eventually be searched by information such as location, surface type, facility type, estimated play-area size, and opportunity rating.

## Project Status

The project is currently moving from a static HTML and CSS prototype into the database and server-development stage.

Completed so far:

- Semantic HTML page structure
- Responsive facility-result cards
- Accessible search form controls
- Accessible facility badges and card information
- Static search-results prototype
- Static saved-leads and print prototype
- Approved first-version facility types
- Database domain model
- Database data dictionary
- Database design decisions
- First-version database schema
- Entity relationship diagram
- MySQL internal ID strategy

Currently being planned:

- MySQL table creation
- Sample database records
- Server-rendered search results
- Database filtering and sorting
- Reusable HTML templates

Not yet implemented:

- Live database connection
- Working search filters
- Working result sorting
- Dynamic saved leads
- User accounts
- Live opportunity scoring
- Automatic data collection

## Development Approach

The project follows a progressive enhancement approach.

The main search experience should work with standard HTML forms and server-rendered pages. JavaScript may be added later when it provides a useful enhancement, but the main application should not depend on JavaScript for basic searching and browsing.

The code should remain:

- readable
- accessible
- maintainable
- beginner-friendly
- reusable
- organized by responsibility

## Current Technologies

Currently implemented:

- HTML5
- CSS3
- Git
- GitHub

Proposed for the next development stage:

- Node.js
- Express
- EJS templates
- MySQL 8.4
- Small amounts of JavaScript for optional enhancements

The server technology has not been added to the repository yet.

## Project Structure

```text
turf-facility-finder/
├── assets/
│   ├── fonts/
│   └── images/
├── docs/
│   ├── 01-product-vision.md
│   ├── 02-domain-model.md
│   ├── 03-data-dictionary.md
│   ├── 04-database-design.md
│   ├── 05-database-schema.md
│   ├── 06-database-entity-relationship-diagram.md
│   └── Database-schema-v1-changes.md
├── index.html
├── saved-leads.html
├── styles.css
└── README.md
```

## Deferred Interface Work

The following interface features will be added as the server-rendered application is built:

- Render a simple empty-results message when a database search returns no facilities.
- Render an understandable server-error message if facility data cannot be loaded.
- Use one reusable EJS partial for facility cards.
- Add server-rendered pagination only when the database contains enough results to require it.
- Use `rel="prev"` and `rel="next"` on working pagination links.
- Add `aria-atomic="true"` when result counts are updated dynamically.
- Use `aria-pressed` when saved-lead buttons gain working toggle behavior.
- Add remove-from-saved controls when the saved-leads data workflow is implemented.
