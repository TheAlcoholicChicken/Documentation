# APIs
## Core App

### GET User Profile: /user_profile_link (this is unique per user)
Parameters:none required

Responses: Dynamically rendered Page

## Mangement System
### POST Create Account: user/create_account
Parameters:
```
{ 
    user_email: ”something”, 
    token: "token" // token here must be same as 'badge_app_token' to proceed with request
}
```

Responses:
```
{ 
    user_id: “something”, 
    badge_text: “Top 13th Player or Javascript 2% Top Player” 
}
```

### POST Login: user/login
### POST Get Badges: user/get_badges

## External Applications (HangmanApp, QuizApp, TankGameApp, etc)

### POST Ranking: /user/get_badge_message

Parameters:
```
{ 
    user_id: ”something”, 
    token: "token" // token here must be same as 'badge_app_token' to proceed with request
}
```

Responses:
```
{ 
    user_id: “something”, 
    badge_text: “Top 13th Player or Javascript 2% Top Player” 
}
```

# Database Sructure
Anything surounderd in square brakets implies that it will be array of whatever is in the square brackets.

## Core App
```
{
 users: [{
    user_id: String // will be the same user_id from Mangement API,
    user_profile_link: String, // computed on sign-up, first_name + last_name (camelcase)
    data: {
        first_name: String,
        last_name: String,
        profile_picture_url: String // hosted on the core app's server
        description: String
    }
 }],
 user_links: [String] // list of strings, to avoid duplicate URLs
}
```

## Mangement System
```
{
    users: [{
      user_id: String // computed when user signs up
      connected_external_apps: [app_ids] 
      credentials: {
        email: String,
        password: String // will be hashed
      }
    }],
    external_apps : {
        'app_id' : {
            app_name: String,
            app_url: String,
            app_icon: String, // url to image
            token: String, // unique token decided on onboarding
        }
    }
}
```

## External App
```
{
app_id: String // manually created during onboarding. Must be Unique for each external app
core_app_token: String,
users: [{
    user_id: String // computed when user signs up
    core_app_id: String // filled out when user signs in with core app's credentials
    data: {
        ... // depends on app
    }
}]
}
```
