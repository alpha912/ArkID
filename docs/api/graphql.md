# ArkID GraphQL API

> **Note**: This GraphQL API documentation is a placeholder and will be implemented during Phase 3 of the ArkID project timeline (Months 10-16).

## GraphQL Endpoint

- **Production**: `https://arkid.researchark.eu/graphql`
- **Staging**: `https://staging.arkid.researchark.eu/graphql`
- **Development**: `https://dev.arkid.researchark.eu/graphql`

## Authentication

All GraphQL requests require authentication using OAuth 2.0/OpenID Connect. Authentication will be handled via bearer tokens:

```
Authorization: Bearer {access_token}
```

## Schema Overview

The ArkID GraphQL schema provides a flexible way to query and mutate data across the entire research innovation chain.

### Core Types

#### Idea Type
```graphql
type Idea {
  arkidIdea: ID!
  creationDate: DateTime!
  title: String!
  description: String
  createdBy: User!
  keywords: [String!]
  status: IdeaStatus
  verificationStatus: VerificationStatus
  associatedProjects: [Project!]
  associatedProducts: [Product!]
  domain: [String!]
  license: String
}
```

#### User Type
```graphql
type User {
  arkidUser: ID!
  creationDate: DateTime!
  name: Name!
  orcidId: String
  userBio: String
  verificationStatus: VerificationStatus
  associatedIdeas: [Idea!]
  associatedProjects: [Project!]
  managedProjects: [Project!]
  associatedOrganizations: [Organization!]
  managedOrganizations: [Organization!]
  associatedProducts: [Product!]
  websiteUrl: String
}
```

#### Project Type
```graphql
type Project {
  arkidProject: ID!
  projectDetails: ProjectDetails!
  originatingIdea: Idea
  associatedPeople: [User!]
  associatedOrganizations: [Organization!]
  publications: [Publication!]
}
```

#### Organization Type
```graphql
type Organization {
  arkidOrg: ID!
  organizationDetails: OrganizationDetails!
  organizationLocation: Location!
  rorId: String
  doiOrgId: String
  picId: String
  website: String
}
```

#### Product Type
```graphql
type Product {
  arkidProduct: ID!
  productDetails: ProductDetails!
  creationDate: DateTime!
  description: String
  originatingProject: Project
  originatingIdea: Idea
  associatedPeople: [User!]
  associatedOrganizations: [Organization!]
  externalIdentifiers: ExternalIdentifiers
  versionInformation: VersionInfo
  licenseInformation: License
  publicationStatus: PublicationStatus
  keywords: [String!]
}
```

## Example Queries

### Fetch an Idea with Related Entities
```graphql
query {
  idea(id: "ARKI-0000000112233445") {
    arkidIdea
    title
    description
    createdBy {
      arkidUser
      name {
        firstName
        lastName
      }
    }
    associatedProjects {
      arkidProject
      projectDetails {
        projectAcronym
        fullProjectTitle
      }
    }
  }
}
```

### Fetch User with All Relationships
```graphql
query {
  user(id: "ARKU-0000000278392736") {
    arkidUser
    name {
      firstName
      lastName
    }
    associatedIdeas {
      arkidIdea
      title
    }
    associatedProjects {
      arkidProject
      projectDetails {
        projectAcronym
      }
    }
    associatedOrganizations {
      arkidOrg
      organizationDetails {
        organizationName
      }
    }
    associatedProducts {
      arkidProduct
      productDetails {
        productName
      }
    }
  }
}
```

## Status
Implementation pending. The first version of this GraphQL reference will be available with the second pilot deployment in Months 10-12. 