---
title: Database Sructure
permalink: /db_structure/
---

# Database Sructure
Anything surounderd in square brakets implies that it will be array of whatever is in the square brackets.

## Core App
```
{
 users: [{
    user_id: String // will be the same user_id from Mangement API
    data: {
        profile_picture_url: String // hosted on the core app's server
        ... // other things required, nothing related to the external apps
    }
 }] 
}
```

## Mangement API
```
{
    users: [{
      user_id: String // computed when user signs up
      connected_external_apps: [String] // strings in this array will be the app_id of external apps
      credentials: {
        email: String,
        password: String // will be hashed
      }
    }]
}
```

## External App
```
{
app_id: String // manually created during onboarding. Must be Unique for each external app.
users: [{
    user_id: String // computed when user signs up
    core_app_id: String // filled out when user signs in with core app's credentials
    data: {
        ... // app determined
    }
}]
}
```
