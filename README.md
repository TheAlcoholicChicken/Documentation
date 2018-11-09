# APIs

## External Applications (HangmanApp, QuizApp, TankGameApp, etc)

### POST Score: /user/get_score

Parameters:
{ userid: ”something” }
Responses:
{ userid: “something”, msg: “Score is 100”, value: “100” }

### POST Ranking: /user/get_badge_message

Parameters:
{ userid: ”something” }
Responses:
{ userid: “something”, msg: “Top 13th Player or Javascript 2% Top Player” }
