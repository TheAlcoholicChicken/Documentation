# APIs

## Core App

### GET User Profile: /user_profile_link (this is unique per user)

Parameters:none required

Responses: Dynamically rendered Page

## Management System

### POST Create Account: user/create_account

Parameters:

```
{
    user_email: ”something”,
    password: "something", would be hashed. Hashed method is yet to be determined
    token: "token" // must be an authorized token to proceed with request
}
```

Responses:

```
{
    success: boolean
}
```

### POST Login: user/login

Parameters:

```
{
    user_email: ”something”,
    password: "something", would be hashed. Hashed method is yet to be determined
    token: "token" // must be an authorized token to proceed with request
```

Responses:

```
{
    success: boolean
}
```

### POST Get Badges: user/get_badges

Parameters:

```
{
    user_id: ”something”,
    token: "token" // must be an authorized token to proceed with request
}
```

Responses:

```
{
    badges: [{
        app_name: String,
        app_url: String,
        app_icon: String, // url to image
        badge_text: String
    }]
}
```

## External Applications (HangmanApp, QuizApp, TankGameApp, etc)

### POST Ranking: /user/get_badge_message

Parameters:

```
{
    user_id: ”something”,
    token: "token" // token must match app's token to proceed with request
}
```

Responses:

```
{
    user_id: “something”,
    badge_text: “Top 13th Player or Javascript 2% Top Player”
}
```

# Database Structure

Anything surrounded in square brakets implies that it will be array of whatever is in the square brackets.

## Core App

```
{
 token: String // used to send authorized reqs to the mangement system
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

## Management System

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
            token: String, // unique token used to make requests for the external app
        }
    },
    authorized_tokens: [String] // requests with this any token from this list can only make requests
}
```

## External App

```
{
token: String, // token assigned by the management system
users: [{
    user_id: String // computed when user signs up
    core_app_id: String // filled out when user signs in with core app's credentials
    data: {
        ... // depends on app
    }
}]
}
```

# Tokens explained

There is 1 unique token for each external app, and the core app.

During onboarding of an external app, a token a generated for that app. That token must be used in each requests to ensure that the request is coming from an authorized source.

The direction a request goes in our architecture.
![request flow chart](https://github.com/TheAlcoholicChicken/Documentation/blob/master/assets/img/reqFlowChart.png?raw=true)

Management Server store all authorized tokens in a list, so whenever a request comes in, the token associated to that request _MUST_ exist in the authorized tokens list, to be able to make a request to the management server.

When then management server makes a request to a particular external app, it must also include the respective token so the external app can verify it's getting a authorized request from the management server.
