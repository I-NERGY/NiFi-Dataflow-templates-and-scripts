## API DataFlow
In this example we will cover the API usage in NiFi where we first need to login and get the bearer token which is used afterwards for fetching the actual data.

![image](https://user-images.githubusercontent.com/90190347/189651969-3d166d14-0359-45ad-8ce8-be6089706cf9.png)

## How does it work?

### Logging in and getting the bearer token
Supose we have a remote URL looking like ```htts://test.com/login``` where we post login information in this format ```grant_type=password&userName=user&password=password``` that gives us the bearer token in this format ```json
{"access_token":"TOKEN_HERE"}```
