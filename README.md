# system-design-template
System design template to help with creation/brainstorming of system designs

# Template
---

Scoping (MVP)
- User can create an account and authenticate 
- User can upload PNG or JPEG photo 
- If photo has geolocation metadata it will get pinned on an interactive map
- If photo does not have geolocation metadata user will get a notification and image will not be imported to system
Scoping (Phase 2)
- User can select location on map if no geolocation metadata

## Technical design

### Flow 
User uploads photo -> photo is placed in to blob storage -> sends event to queue system with image metadata -> worker picks up event -> worker sends geolocation to data store 

![[geotagz|800]]
## Data models

### Data store
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
