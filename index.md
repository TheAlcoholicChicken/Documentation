# APIs

## Core App

### GET User Profile: /user_profile_link (this is unique per user)

Parameters:none required

user profile link structure: /user/{user_id}, where {user_id} is unique to each user and the /user/ part is requesting for user info of this {user_id}

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
    msg: "something",
    user_id: "something"
}
```

### POST Login: user/login

Parameters:

```
{
    user_email: "something",
    password: "something", would be hashed. Hashed method is yet to be determined
    token: "token" // must be an authorized token to proceed with request
}
```

Responses:

```
{
    msg: "something",
    user_id: "something"
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
    user_email: ”something”,
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

### Users Collection

```
{
    user_id: String // will be the same user_id from Mangement API,
    user_profile_link: String, // computed on sign-up, first_name + last_name (camelcase)
    data: {
        first_name: String,
        last_name: String,
        profile_picture_url: String // hosted on the core app's server
        description: String
    }
}
```

## Management System

### Users Collection
```

{
  user_id: String // computed when user signs up
  connected_external_apps: [app_ids]
  credentials: {
    email: String,
    password: String // will be hashed
  }
}
```
### External Apps Collection
```
{
    app_name: String,
    app_url: String,
    app_icon: String, // url to image
    token: String, // unique token used to make requests for the external app
}
```

### Authorized Tokens Collection
```
{ token: String }
```

## External App
### Users Collection
```
{
    user_id: String // computed when user signs up
    core_app_id: String // filled out when user signs in with core app's credentials
    data: {
        ... // depends on app
    }
}
```

# Tokens explained

There is 1 unique token for each external app, and the core app.

During onboarding of an external app, a token a generated for that app. That token must be used in each requests to ensure that the request is coming from an authorized source.

The direction a request goes in our architecture.
![request flow chart](https://github.com/TheAlcoholicChicken/Documentation/blob/master/assets/img/reqFlowChart.png?raw=true)

Management Server store all authorized tokens in a collection, so whenever a request comes in, the token associated to that request _MUST_ exist in the authorized tokens list, to be able to make a request to the management server.

When then management server makes a request to a particular external app, it must also include the respective token so the external app can verify it's getting a authorized request from the management server.

# Implemenation for Checking Tokens for each request (Node.js)

There are 2 things needed for thi implemenation. A config.js, and a authentication middleware.

### config.js
```
module.exports = {
    token: proccess.env.AUTH_TOKEN || 'randomlyGeneratedTokenForDevEnv' // auth token
};
```

### middlewares.js
```
let config = require('config.js');

let auth_token = (req, res, next) {
    // get token from req
    let token = req.body.token;
    
    if (!token) { 
        res.status(401).send({ error: "Token Missing" }); 
    }
    
    // checks token, this should use a more reobust method irl, but for the simplicity for this project, this is enough
    if (token === config.token) {
        next(); // proceed to actual request
    } else {
        res.status(401).send({ error: 'Invalid Token' });
    }
}

module.exports = { auth_token }
```

# Usage
### routes.js
```
  let mds = require('./middlewares.js')
  
  app.post('/user/get_badge_message', mds.auth_token, (req, res) => {
    
    // ofc perform some DB things here to get data
    
    // exmplae response
    res.status(200).json({
      userid: '123456',
      msg: 'Top 13th Player or Javascript 2% Top Player'
    });
    
    res.end();
  });
```

# Onboard your app
1. Send a post request to `https://management-system-api.herokuapp.com/admin/add_app_token`
2. Have the body be:
```
{
	"app_name": "App Name",
	"app_url": "App Url",
	"app_icon": "App Icon URl" // url to the image file, prefebly hosted on the app's server
}
```
3.  the response should be, it will include the token you have to use for any other requests
