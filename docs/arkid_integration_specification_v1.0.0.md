# ArkID Integration Using Keycloak

## **Introduction**

ArkID aims to be a unique identifier system for the ResearchArk platform, similar to ORCID, capable of generating IDs for users, projects, and organizations. Unlike existing systems like ORCID, ArkID solves the specific problem of managing complex relationships between different research entities, such as users, projects, and organizations, ensuring traceability and unified identification for all stakeholders in the research ecosystem. Unlike ORCID, ArkID provides identifiers not only for individual researchers but also for projects and organizations. This enhances traceability across different research entities and ensures a holistic integration of all key players in the research ecosystem. ArkID will be a secure, memorable identifier that provides easy identification and authentication across the entire ResearchArk ecosystem. It will also allow users, projects, and organizations to be identified uniquely, accessible via a user-friendly URL format, such as `https://arkid.researchark.eu/{arkid}`.

Keycloak will be used as the Identity Provider (IdP) to manage authentication and assign ArkIDs to users. Keycloak was chosen over other identity management systems because of its robust support for modern authentication protocols (such as OpenID Connect and OAuth 2.0), ease of integration, scalability, and extensive customization options, which make it well-suited to meet the complex needs of the ResearchArk platform. However, additional functionalities, such as assigning ArkIDs to projects and organizations, will require custom workflows to fully integrate the broader ResearchArk ecosystem.

ArkID is designed to simplify how different actors within the ResearchArk community interact by providing a centralized authentication and identification solution. Whether the entity is a user, project, or organization, ArkID enables streamlined identification and enhances the efficiency of managing research-related credentials. The use of Keycloak as a foundation for ArkID means we can leverage industry-standard identity and access management protocols, ensuring reliability and scalability for future expansion.

### **ArkID Requirements**

1. **Unique, Human-Readable ArkIDs:** IDs need to be unique, memorable, and easy to comply with ISO standards. UUIDs are not preferred due to their length and complexity, which may hinder ease of use and communication.
2. **Entity Support:** ArkID must be capable of supporting identifiers for:
   - Users (login/auth)
   - Projects
   - Organizations
3. **User Ownership & Permissions:** Users with an ArkID should be able to create ArkIDs for projects or organizations if they can verify their authorization. This ownership capability ensures that only verified individuals are able to create and manage the ArkIDs.
4. **URL Resolution:** The ArkID must be publicly resolvable, e.g., `https://arkid.researchark.eu/{arkid}`, which will link to either a profile or metadata page.
5. **Data Privacy and Security:** Users should be able to control what information is publicly accessible, ensuring their privacy preferences are respected across all entities in the ResearchArk ecosystem.

## **ArkID Schema Design**

### **1. ArkID Format**

To ensure ArkIDs are compliant with the **ISO 27729 (ISNI)** standard, each ArkID contains a **16-digit numerical value** that remains unique for each entity type, along with an **alphanumeric prefix** to identify the type of entity. The combination of a prefix and a numeric value enhances usability by making it easier to identify the type of entity at a glance, which improves readability and reduces the likelihood of confusion compared to using only a numerical value.

Format examples:
- **User ArkID:** `https://arkid.researchark.eu/ARKU-0000000278392736`
- **Project ArkID:** `https://arkid.researchark.eu/ARKP-0000000394321980`
- **Organization ArkID:** `https://arkid.researchark.eu/ARKO-0000000512938476`

### **2. Entity Schemas**

Each entity type follows a strictly defined JSON schema that ensures data consistency and validation:

#### **User Schema (v1.0.0)**
Required fields:
- `arkidUser`: Unique identifier (ARKU-prefix) following ISO 27729
- `creationDate`: ISO 8601 timestamp
- `name`: Object containing firstName and lastName

Optional fields:
- Associated projects with roles and dates
- Managed projects
- Associated organizations with roles and dates
- Managed organizations
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

Optional fields:
- Project duration (ISO 8601 dates)
- Budget (ISO 4217 currency)
- Associated people (with roles)
- Associated organizations (with roles)
- Publications (DOIs, ISBNs, arXiv IDs)

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

All custom attributes follow the JSON schemas defined in version 1.0.0:

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
  }
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

### **2. Extending Keycloak for Projects and Organizations**

Keycloak primarily manages users. However, we extend its capabilities for projects and organizations in the following ways:

#### Representing Projects and Organizations:
- Use **Groups** in Keycloak to represent projects and organizations
- Store **custom attributes** to assign ArkIDs to each group
- Leverage built-in group hierarchy and access control

#### ArkID Creation Workflow:
- Custom REST API for ArkID creation requests
- Authentication and authorization checks
- Validation against schema requirements

### **3. Verification for ArkID Creation**

Users must pass verification before being allowed to create ArkIDs for projects or organizations. The verification process includes:

#### Verification Status Types:
- `Verified`: User has been approved to create project and organization ArkIDs
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

### **3. API Access**

The public resolver provides a REST API for programmatic access:

```
GET https://arkid.researchark.eu/api/v1/{arkid}
Accept: application/json
```

Response format follows the respective schema for each entity type (user, project, organization).

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

### **3. Security Implementation**

- All API endpoints must use HTTPS
- Authentication using OAuth 2.0 / OpenID Connect
- Rate limiting applied to public API endpoints
- Input validation on all endpoints
- Regular security audits
- GDPR compliance for EU data protection

### **4. Performance Considerations**

- Caching strategy for public resolver
- Database indexing on ArkID fields
- Load balancing for high availability
- Response time monitoring
- Resource usage optimization

## **Appendix: Schema Documentation**

The complete JSON schemas for all entity types are available in the following files:

1. User Schema: [/docs/arkid/arkid_user_schema_v1.0.0.json](arkid_user_schema_v1.0.0.json)
2. Project Schema: [/docs/arkid/arkid_project_schema_v1.0.0.json](arkid_project_schema_v1.0.0.json)
3. Organization Schema: [/docs/arkid/arkid_organization_schema_v1.0.0.json](arkid_organization_schema_v1.0.0.json)

### Schema Relationships

The three schemas are interrelated through their respective ArkID references:

1. User Schema references:
   - Project ArkIDs in associatedProjects
   - Organization ArkIDs in associatedOrganizations

2. Project Schema references:
   - User ArkIDs in associatedPeople
   - Organization ArkIDs in associatedOrganizations

3. Organization Schema references:
   - No direct references to other ArkIDs, but is referenced by both User and Project schemas

### Schema Validation

To validate entity data against these schemas:

1. Use a JSON Schema validator that supports draft-2020-12
2. Ensure all required fields are present
3. Validate format patterns for identifiers
4. Check relationship consistency across entities
5. Verify enumerated values match schema options