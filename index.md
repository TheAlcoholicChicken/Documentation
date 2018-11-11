# APIs

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
badge_app_token: String,
users: [{
    user_id: String // computed when user signs up
    core_app_id: String // filled out when user signs in with core app's credentials
    data: {
        ... // depends on app
    }
}]
}
```
