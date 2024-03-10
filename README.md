# system-design-template
System design template to help with creation/brainstorming of system designs

# Template
---
# SYSTEM DESIGN NAME (PRODUCT/PROJECT NAME)

Scoping (MVP)
<INSERT BRIEF OVERVIEW IN BULLET POINTS WHAT REQUIREMENTS>
examples:

- User can create an account and authenticate 
- User can upload PNG or JPEG photo 

## Technical design

### Flow 
<INSERT FLOW FOR USER EXPERIENCE IF THERE IS ONE>
example:
	
User uploads photo -> photo is placed in to blob storage -> sends event to queue system with image metadata -> worker picks up event -> worker sends geolocation to data store 

<INSERT DIAGRAM HERE I LIKE TO USE EXCALIDRAW>
	
## Data models

### Data store
<INSERT YOUR DATA MODELS>
examples:
	
Tables: users (User entity), images (ImageData entity)

```plantuml-svg
!theme minty

entity ImageData {
	id PK Uuid
	user_id FK Uuid
	coordinates geography(POINT)
	blob TEXT
}

entity User {
	id PK Uuid
	first_name TEXT
	last_name TEXT
	username TEXT
	email TEXT
}
```

### Message queue

```plantuml-svg
!theme minty

entity NewImageUploadEvent {
	id int
	
	/* the location where the image was imported to in blob storage */
	source String
	
	/ * the id of the user the uploaded the image */
	user_id int
}
```

# API
<INSERT YOUR API ROUTES IF THERE ARE ANY>
examples:
	
`POST /api/v1/:user_id/images`

DTO 
```plantuml-svg
!theme minty

struct ImagesDto {
	id int
	source String
	user_id int
}
```

Response 
`201 Created`
`400 Bad request`
`401 Unauthorized`


`GET /api/v1/user/:user_id/images`

Response 
`200 OK`
`400 Bad request` generic service error
`401 Unauthorized` no jwt or invalid jwt
`403 Forbidden`  attempting to access user_id that is not in jwt

Response Body

```plantuml-svg
!theme minty

struct Images {
	images Vec<Image>
}

struct Image {
	latitude String
	longitude String
	source String
}
```
