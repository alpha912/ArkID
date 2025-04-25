# ArkID Integration Using Keycloak

**Version:** 1.1.0  
**Last Updated:** April 25, 2025 
**Status:** Draft

## **Introduction**

ArkID aims to be a unique identifier system for the ResearchArk platform, capable of generating standardized ISO-27729 identifiers for the complete research innovation chain—ideas, researchers, projects, organizations, and products. Unlike existing systems like ORCID, ArkID solves the specific problem of managing complex relationships between different research entities across the entire innovation lifecycle, ensuring traceability and unified identification for all stakeholders in the research ecosystem. 

ArkID provides identifiers not only for individual researchers but also for ideas, projects, organizations, and research outputs. This enhances traceability across different research entities and ensures a holistic integration of all key players in the research ecosystem. ArkID will be a secure, memorable identifier that provides easy identification and authentication across the entire ResearchArk ecosystem. It will also allow all entity types to be identified uniquely, accessible via a user-friendly URL format, such as `https://arkid.researchark.eu/{arkid}`.

Keycloak will be used as the Identity Provider (IdP) to manage authentication and assign ArkIDs to users. Keycloak was chosen over other identity management systems because of its robust support for modern authentication protocols (such as OpenID Connect and OAuth 2.0), ease of integration, scalability, and extensive customization options, which make it well-suited to meet the complex needs of the ResearchArk platform. However, additional functionalities, such as assigning ArkIDs to ideas, projects, organizations, and products, will require custom workflows to fully integrate the broader ResearchArk ecosystem.

ArkID is designed to simplify how different actors within the ResearchArk community interact by providing a centralized authentication and identification solution. Whether the entity is an idea, user, project, organization, or product, ArkID enables streamlined identification and enhances the efficiency of managing research-related credentials. The use of Keycloak as a foundation for ArkID means we can leverage industry-standard identity and access management protocols, ensuring reliability and scalability for future expansion.

### **ArkID Requirements**

1. **Unique, Human-Readable ArkIDs:** IDs need to be unique, memorable, and easy to comply with ISO standards. UUIDs are not preferred due to their length and complexity, which may hinder ease of use and communication.
2. **Entity Support:** ArkID must be capable of supporting identifiers for:
   - Ideas (initial research concepts)
   - Users/Researchers (login/auth)
   - Projects (funded research initiatives)
   - Organizations (research institutions and partners)
   - Products/Outputs (research results, prototypes, publications)
3. **User Ownership & Permissions:** Users with an ArkID should be able to create ArkIDs for ideas, projects, organizations, or products if they can verify their authorization. This ownership capability ensures that only verified individuals are able to create and manage the ArkIDs.
4. **URL Resolution:** The ArkID must be publicly resolvable, e.g., `https://arkid.researchark.eu/{arkid}`, which will link to either a profile or metadata page.
5. **Data Privacy and Security:** Users should be able to control what information is publicly accessible, ensuring their privacy preferences are respected across all entities in the ResearchArk ecosystem.
6. **Verifiable Credentials:** The system must support W3C verifiable credentials standards to ensure tamper-evident provenance chains.
7. **Temporal Relationship Tracking:** ArkID must track relationships between entities over time, including start and end dates of associations.
8. **Digital Sovereignty:** The infrastructure must be EU-hosted and governed to ensure data sovereignty.

## **ArkID Schema Design**

### **1. ArkID Format**

To ensure ArkIDs are compliant with the **ISO 27729 (ISNI)** standard, each ArkID contains a **16-digit numerical value** that remains unique for each entity type, along with an **alphanumeric prefix** to identify the type of entity. The combination of a prefix and a numeric value enhances usability by making it easier to identify the type of entity at a glance, which improves readability and reduces the likelihood of confusion compared to using only a numerical value.

Format examples:
- **Idea ArkID:** `https://arkid.researchark.eu/ARKI-0000000112233445`
- **User ArkID:** `https://arkid.researchark.eu/ARKU-0000000278392736`
- **Project ArkID:** `https://arkid.researchark.eu/ARKP-0000000394321980`
- **Organization ArkID:** `https://arkid.researchark.eu/ARKO-0000000512938476`
- **Product ArkID:** `https://arkid.researchark.eu/ARKD-0000000667788990`

### **2. Entity Schemas**

Each entity type follows a strictly defined JSON schema that ensures data consistency and validation:

#### **Idea Schema (v1.0.0)**
Required fields:
- `arkidIdea`: Unique identifier (ARKI-prefix) following ISO 27729
- `creationDate`: ISO 8601 timestamp
- `title`: Title of the research idea
- `createdBy`: ArkID of the user who created the idea

Optional fields:
- Description
- Keywords
- Related concepts
- Status (Draft/Published/Archived)
- Verification status

#### **User Schema (v1.0.0)**
Required fields:
- `arkidUser`: Unique identifier (ARKU-prefix) following ISO 27729
- `creationDate`: ISO 8601 timestamp
- `name`: Object containing firstName and lastName

Optional fields:
- Associated ideas with roles and dates
- Associated projects with roles and dates
- Managed projects
- Associated organizations with roles and dates
- Managed organizations
- Associated products with roles and dates
- ORCID ID (format: ^\d{4}-\d{4}-\d{4}-\d{3}[\dX]$)
- User bio
- Website URL
- Verification status (Verified/Pending/Rejected)

#### **Project Schema (v1.0.0)**
Required fields:
- `arkidProject`: Unique identifier (ARKP-prefix)
- `projectDetails`: Object containing:
  - `projectAcronym`
  - `fullProjectTitle`
- `originatingIdea`: ArkID of the idea that led to this project (if applicable)

Optional fields:
- Project duration (ISO 8601 dates)
- Budget (ISO 4217 currency)
- Associated people (with roles and dates)
- Associated organizations (with roles and dates)
- Associated products/outputs (with dates)
- Publications (DOIs, ISBNs, arXiv IDs)
- Funding information

#### **Organization Schema (v1.0.0)**
Required fields:
- `arkidOrg`: Unique identifier (ARKO-prefix)
- `organizationDetails`: Object containing:
  - `organizationName`
  - `organizationAcronym`
  - `organizationType`
- `organizationLocation`: Object containing:
  - `city`
  - `country` (ISO 3166-1 alpha-2)

Optional fields:
- ROR ID
- DOI Org ID
- PIC ID (9-digit format)
- Website URL
- Associated projects
- Associated people
- Associated products

#### **Product Schema (v1.0.0)**
Required fields:
- `arkidProduct`: Unique identifier (ARKD-prefix)
- `productDetails`: Object containing:
  - `productName`
  - `productType` (Publication/Software/Hardware/Patent/Dataset/Other)
- `creationDate`: ISO 8601 timestamp
- `originatingProject`: ArkID of the project that produced this output (if applicable)

Optional fields:
- Description
- Associated people (creators, contributors)
- Associated organizations
- External identifiers (DOI, ISBN, Patent number)
- Version information
- License information
- Publication status

### **3. How the 16-Digit Number is Generated**

The 16-digit number in ArkID is generated based on the **International Standard Name Identifier (ISNI)** guidelines. Here is how the generation process works:

1. **Unique Identifier Generation**: The 16-digit identifier is generated using a combination of:
   - **Sequential Numbers**: Ensures that each new ArkID is unique.
   - **Entity Metadata**: Attributes like entity creation date or type may be used as part of the generation to increase uniqueness.

2. **Checksum Calculation**: ISNI requires a checksum for validity.
   - The last digit of the 16-digit identifier is a **checksum** calculated using the previous digits to validate the identifier's integrity.
   - This checksum helps detect errors in data entry or transmission, offering an additional layer of verification.

3. **Global Uniqueness**: ISNI-compliance ensures that the ArkID remains unique across the globe, making it suitable for integration with other systems and platforms.

## **Keycloak Integration**

### **1. Custom Attributes for ArkID**

All custom attributes follow the JSON schemas defined in version 1.1.0:

#### Idea Attributes:
```json
{
  "arkidIdea": "ARKI-0000000112233445",
  "creationDate": "2024-10-20T09:30:00Z",
  "title": "Hybrid Wind Energy Storage System",
  "createdBy": "ARKU-0000000278392736",
  "status": "Published"
}
```

#### User Profile Attributes:
```json
{
  "arkidUser": "ARKU-0000000278392736",
  "creationDate": "2024-10-24T12:00:00Z",
  "name": {
    "prefix": "Dr.",
    "firstName": "John",
    "middleName": null,
    "lastName": "Doe",
    "suffix": "Ph.D."
  },
  "verificationStatus": "Verified",
  "orcidId": "0000-0002-1825-0097"
}
```

#### Project Custom Attributes:
```json
{
  "arkidProject": "ARKP-0000000394321980",
  "projectDetails": {
    "projectAcronym": "HybridWind",
    "fullProjectTitle": "Hybrid Wind Energy Systems",
    "projectDuration": {
      "startDate": "2024-01-01",
      "endDate": "2026-12-31"
    }
  },
  "originatingIdea": "ARKI-0000000112233445"
}
```

#### Organization Custom Attributes:
```json
{
  "arkidOrg": "ARKO-0000000512938476",
  "organizationDetails": {
    "organizationName": "Research Institute of Technology",
    "organizationAcronym": "RIT",
    "organizationType": "Research Institution"
  },
  "organizationLocation": {
    "city": "Amsterdam",
    "country": "NL"
  }
}
```

#### Product Custom Attributes:
```json
{
  "arkidProduct": "ARKD-0000000667788990",
  "productDetails": {
    "productName": "Enhanced Wind Turbine Blade Design",
    "productType": "Patent"
  },
  "creationDate": "2025-05-15T14:25:00Z",
  "originatingProject": "ARKP-0000000394321980"
}
```

### **2. Extending Keycloak for All Entity Types**

Keycloak primarily manages users. However, we extend its capabilities for ideas, projects, organizations, and products in the following ways:

#### Representing All Entity Types:
- Use **Groups** in Keycloak to represent projects and organizations
- Use **Custom Resources** for ideas and products
- Store **custom attributes** to assign ArkIDs to each entity type
- Leverage built-in group hierarchy and access control
- Implement custom SPI modules to handle the specific requirements of each entity type

#### ArkID Creation Workflow:
- Custom REST API for ArkID creation requests
- Authentication and authorization checks
- Validation against schema requirements
- Temporal.io workflows for complex approval processes

#### Relationship Management:
- Graph database extensions for tracking relationships between entities
- Temporal tracking of relationship start and end dates
- Role-based associations between entities

### **3. Verification for ArkID Creation**

Users must pass verification before being allowed to create ArkIDs for ideas, projects, organizations, or products. The verification process includes:

#### Verification Status Types:
- `Verified`: User has been approved to create ArkIDs for other entity types
- `Pending`: User has submitted verification request but approval is pending
- `Rejected`: User's verification request has been denied

#### Verification Process:
1. User submits verification request with supporting documentation
2. System administrators review the request
3. Status is updated to either "Verified" or "Rejected"
4. User is notified of the decision
5. Verified users can proceed with ArkID creation

#### Verification Requirements:
- Must have a complete user profile
- Must have valid institutional affiliation
- Must provide professional credentials
- Must agree to terms of use and data management policies

## **Public Resolving Service for ArkIDs**

### **1. URL Structure and Accessibility**

All ArkIDs are accessible via the URL: `https://arkid.researchark.eu/{arkid}`. This URL resolves to a profile or metadata page providing essential information based on entity type and privacy settings.

### **2. Metadata Access and Privacy**

#### Idea Metadata:
- **Public by Default:**
  - Title
  - Creation date
  - Keywords
  - Status
- **Optional Public Fields:**
  - Description
  - Related concepts
  - Creator information (limited)

#### User Metadata:
- **Public by Default:**
  - Display name
  - Verification status
  - Public projects
  - Current organization affiliations
- **Optional Public Fields:**
  - ORCID ID
  - Email contact form
  - Biography
  - Website URL
  - Past affiliations

#### Project Metadata:
- **Public by Default:**
  - Project acronym
  - Full project title
  - Project brief
  - Start and end dates
  - Public team members
- **Optional Public Fields:**
  - Budget information
  - Publications
  - Detailed objectives
  - Project updates

#### Organization Metadata:
- **Public by Default:**
  - Organization name
  - Organization type
  - Location (city, country)
  - ROR ID
- **Optional Public Fields:**
  - Member directory
  - Projects listing
  - Contact information
  - Additional identifiers (DOI, PIC)

#### Product Metadata:
- **Public by Default:**
  - Product name
  - Product type
  - Creation date
  - License information (if applicable)
- **Optional Public Fields:**
  - Description
  - Associated people
  - Version history
  - Patent/DOI information

### **3. API Access**

The public resolver provides multiple API options for programmatic access:

#### RESTful API:
```
GET https://arkid.researchark.eu/api/v1/{arkid}
Accept: application/json
```

#### GraphQL API:
```
POST https://arkid.researchark.eu/graphql
Content-Type: application/json
{
  "query": "query($id: ID!) { entity(id: $id) { ... on User { name } ... on Project { title } } }",
  "variables": { "id": "ARKP-0000000394321980" }
}
```

Response formats follow the respective schema for each entity type (idea, user, project, organization, product).

## **Implementation Details**

### **1. Schema Versioning**

- All implementations must follow schema v1.0.0 specifications
- Schema files are versioned using semantic versioning
- Breaking changes require a new major version
- Backward compatibility maintained within minor versions

### **2. Data Validation**

- All string patterns must match exactly as specified in schemas
- Dates must follow ISO 8601 format
- Country codes must follow ISO 3166-1 alpha-2
- Currency codes must follow ISO 4217
- Identifiers must follow their respective format patterns:
  - ORCID: ^\d{4}-\d{4}-\d{4}-\d{3}[\dX]$
  - DOI: ^10.\d{4,9}/[-._;()/A-Z0-9]+$
  - ROR: ^https?://ror.org/[a-z0-9]{9}$
  - PIC: ^\d{9}$

### **3. Technology Stack**

- **Core Identity Infrastructure:**
  - Keycloak with custom SPI modules
  - PostgreSQL with graph extensions (pg_graphql or ltree)
  - Temporal.io for workflow management
  - Apache Camel for integration

- **Performance and Caching:**
  - Redis for high-performance caching
  - Elasticsearch for search capabilities

- **Containerization and Orchestration:**
  - Docker for containerization
  - Kubernetes-compatible design

- **Security Infrastructure:**
  - OAuth 2.0/OpenID Connect (via Keycloak)
  - API gateway with rate limiting
  - Audit logging for compliance

- **Data Standards:**
  - JSON Schema validators
  - ISO 27729/ISNI format libraries
  - JSON-LD context providers

### **4. Security Implementation**

- All API endpoints must use HTTPS
- Authentication using OAuth 2.0 / OpenID Connect
- Rate limiting applied to public API endpoints
- Input validation on all endpoints
- Regular security audits
- GDPR compliance for EU data protection
- Verifiable credentials for tamper-evident data

### **5. External System Integration**

- ORCID integration for researcher identification
- DOI system integration for publications
- ROR integration for organization data
- ETL pipelines for data import/export
- W3C Verifiable Credentials support

### **6. Performance Considerations**

- Caching strategy for public resolver
- Database indexing on ArkID fields
- Load balancing for high availability
- Response time monitoring
- Resource usage optimization

## **Governance and Sovereignty**

- EU-based hosting for all infrastructure components
- German e.V. foundation as legal steward (planned)
- Community governance model
- Open-source licensing (Apache 2.0)
- Transparent decision-making processes
- Regular security and compliance audits

## **Appendix: Schema Documentation**

The complete JSON schemas for all entity types are available in the following files:

1. Idea Schema: [/docs/arkid_idea_schema.json](arkid_idea_schema.json)
2. User Schema: [/docs/arkid_user_schema.json](arkid_user_schema.json)
3. Project Schema: [/docs/arkid_project_schema.json](arkid_project_schema.json)
4. Organization Schema: [/docs/arkid_organization_schema.json](arkid_organization_schema.json)
5. Product Schema: [/docs/arkid_product_schema.json](arkid_product_schema.json)

### Schema Relationships

The five schemas are interrelated through their respective ArkID references, creating a complete graph of the research innovation chain:

1. Idea Schema references:
   - User ArkIDs for creators

2. User Schema references:
   - Idea ArkIDs in associated ideas
   - Project ArkIDs in associated projects
   - Organization ArkIDs in associated organizations
   - Product ArkIDs in associated products

3. Project Schema references:
   - Idea ArkIDs for originating concepts
   - User ArkIDs in associated people
   - Organization ArkIDs in associated organizations
   - Product ArkIDs in outputs

4. Organization Schema references:
   - User ArkIDs for members
   - Project ArkIDs for research initiatives
   - Product ArkIDs for outputs

5. Product Schema references:
   - Idea ArkIDs for originating concepts
   - Project ArkIDs for originating projects
   - User ArkIDs for creators/contributors
   - Organization ArkIDs for affiliated organizations

This interconnected schema design ensures complete traceability from initial research ideas through to final products, creating a comprehensive record of the entire research and innovation lifecycle.

### Schema Validation

To validate entity data against these schemas:

1. Use a JSON Schema validator that supports draft-2020-12
2. Ensure all required fields are present
3. Validate format patterns for identifiers
4. Check relationship consistency across entities
5. Verify enumerated values match schema options