# ArkID

## Overview
ArkID is an open-source Keycloak-based system that issues standardized ISO-27729 identifiers for the complete research innovation chain—ideas, researchers, projects, organizations, and products. It creates an open, sovereign, "invisible" identifier infrastructure for Europe's research and innovation ecosystem that enables traceability and verification across previously disconnected domains.

## Features
- **Unified Identity Platform**: Provides standardized identification numbers for the entire innovation chain (ARKI for ideas, ARKU for researchers, ARKP for projects, ARKO for organizations, ARKD for products/outputs)
- **Interlinked Relationship Graph**: Forms a connected graph that traces the complete lifecycle from initial concept to market product
- **EU-Sovereign Infrastructure**: Built on open-source components with EU-based hosting and governance
- **Standardized API Access**: Machine-readable access to entity data through REST endpoints and GraphQL APIs
- **Verifiable Credentials**: Supports tamper-proof verification of research entity relationships

## Technical Details

### ArkID Types and Relationships
ArkID provides five standardized identifier types that interconnect the research innovation chain:

- **ARKI**: Identifiers for initial research ideas and concepts
- **ARKU**: Identifiers for users/researchers (complements ORCID)
- **ARKP**: Identifiers for funded projects and initiatives
- **ARKO**: Identifiers for organizations and institutions
- **ARKD**: Identifiers for products, outputs, and deliverables

Each identifier follows the ISO 27729 (ISNI) standard format with a 16-digit number prefixed by its type code. The identifiers form a connected graph that enables traceability from concept to final output:

- Ideas (ARKI) are created by Users (ARKU)
- Projects (ARKP) implement Ideas (ARKI) and involve Users (ARKU)
- Organizations (ARKO) host Projects (ARKP) and employ Users (ARKU)
- Products (ARKD) result from Projects (ARKP) and are created by Users (ARKU)

### Architecture
- **Core:** Keycloak with custom SPI modules
- **Database:** PostgreSQL with graph extensions
- **API:** RESTful and GraphQL endpoints
- **Security:** OAuth 2.0/OpenID Connect, verifiable credentials

## Implementation Timeline

ArkID development is planned in four main phases over 18 months:

### Phase 1: Core Identity Infrastructure (Months 1-6)
- Custom Keycloak SPI modules for research entities
- PostgreSQL database with graph extensions
- Comprehensive CI pipeline with security checks
- Software Bill of Materials (SBOM) generation
- Documented threat models and security patterns

### Phase 2: Identity Integration (Months 5-12)
- Secure token mappers and permission models
- Administrative interfaces with security by default
- External system integration (ORCID, DOI, ROR)
- ETL pipelines for identifier import/mapping
- W3C verifiable credentials implementation

### Phase 3: Performance & Developer Experience (Months 10-16)
- Redis caching for fast identifier resolution
- Elasticsearch for complex entity searches
- Optimized database queries for relationship traversal
- Comprehensive documentation and integration guides
- SDKs and reference implementations

### Phase 4: Governance & Sustainability (Months 14-18)
- German e.V. foundation establishment
- Maintainer steering committee formation
- Formalized security processes
- Published code of conduct and governance policies
- Contributor pathways and mentorship programs

Key deliverables include:
- Months 1-2: Public α-release v0.3 (Keycloak SPI, ISO-27729 resolver)
- Months 3-6: ORCID, DOI, ROR import pipelines + security gates
- Months 7-9: First pilot deployment
- Months 10-12: Second pilot, Redis caching, GraphQL API
- Months 13-15: External security audit, e.V. registration
- Months 16-18: Second audit, contributor handbook, LTS release v1.0

## Installation
```bash
# Installation instructions will be provided once development begins
```

> **Note**: ArkID is currently in the planning and early development phase. Installation and usage instructions will be updated as the project progresses.

## Usage
```bash
# Usage examples will be provided once development begins
```

## Documentation
For more detailed documentation, please see the following:

- [Specifications](./docs/specifications)
- [JSON Schemas](./docs/schemas)
- [API Reference](./docs/api) (coming soon)
- [User Guides](./docs/guides) (coming soon)

> The documentation will be expanded as development proceeds. The project is currently seeking funding through the Sovereign Tech Fund.

## License
This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Authors
- Alphin Tom
- Andreas Corusa 