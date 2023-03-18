

![bootcamp-logo](https://user-images.githubusercontent.com/113374279/224373745-c389f866-e46f-440c-a518-841c0f0ad94a.png)


# Week 3 — Decentralized Authentication

It is week 4 💃🏽💃🏽💃🏽💃 techically, week 3. Despite feeling fatigued, I'm showing resilience and perseverance 💪💪💪.

I usually start with my own little research on the topic of the week. For this week i found an article written by Andrew [freecodecamp](https://www.freecodecamp.org/news/how-to-run-your-own-decentralized-authentication-service-using-authn/). Thanks for all you do, you are trully a hero.

Instructor for the week: 

Andrew Brown

### Resources:

[Instructional-video 1](https://www.youtube.com/live/9obl7rVgzJw?feature=share) (Live stream)

[Cognito Custom Pages](https://youtu.be/T4X4yIzejTc)

[Exploring JWTS](https://youtu.be/nJjbI4BbasU)

[JWT server side Verify](https://youtu.be/d079jccoG-M)


We are using Amazon Cognito for our Decentralized Authentication.

### Implementing Decentralized Authentication using Amazon Cognito

- Sign in to AWS account

- Search for Cognito

![cognito-home](https://user-images.githubusercontent.com/113374279/224384810-0f6ba21e-dcc9-48ee-ad4e-547436b4d995.png)

- Click on the three horizontal lines on the top left corner of the page(Andrew calls them hamburgers 🤣)

![hamburger](https://user-images.githubusercontent.com/113374279/224385532-295080c6-a435-4504-9bf1-f1ce038051d0.png)

- Click on `User Pools`

![user-pool](https://user-images.githubusercontent.com/113374279/224386285-b2c9c545-9a34-4252-a9f0-b8f765f9b70f.png)

A  `Cognito user pool` is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito. Your users can also sign in through social identity providers like Google, Facebook, Amazon, or Apple, this is the case for `Federated Identity providers` 

- Edit `configure sign-in experience`

![user-name](https://user-images.githubusercontent.com/113374279/224389873-c94a3bd0-4a34-4a25-a009-4396d12253f0.png)

-  Edit `Configure security requirements`

![password-policy](https://user-images.githubusercontent.com/113374279/224467526-6710f6a7-bc2b-40e4-851e-fb5aa9497216.png)


![mfa](https://user-images.githubusercontent.com/113374279/224467562-35ddfb25-4aa5-46b4-892a-20f185c7d61f.png)

- Click on next

- `Configure sign-up experience`

![configure-signup](https://user-images.githubusercontent.com/113374279/224468282-4facc421-710c-4e62-8e4c-dfa91e90efba.png)


![atttribute-veri](https://user-images.githubusercontent.com/113374279/224468433-feb710a2-cbbd-41ab-bec4-0f42763f173f.png)


![required-attributes](https://user-images.githubusercontent.com/113374279/224468447-ffb3ab16-3368-47fa-8743-cacfd0748843.png)

- Click on `next`

- Edit `Configure Message delivery`

![configure-message](https://user-images.githubusercontent.com/113374279/224476292-d5ce3436-c81f-4b92-9507-6769aaa0b7d3.png)

- Click on next

- Edit `Integrate your app`


![integrate-app](https://user-images.githubusercontent.com/113374279/224596941-5d354bb3-0d4f-4ce9-93a9-f28346b9b3c0.png)


![initial-app](https://user-images.githubusercontent.com/113374279/224596974-543df972-7de9-49f9-be5d-739e33126a97.png)

- Click on `next`

-   `Review and Create`

After reviewing click on `create user pool`

![sucessful](https://user-images.githubusercontent.com/113374279/224598278-556dc050-3a00-4b4e-b026-7523e12b8c98.png)


### Install AWS Amplify

- Change directory to the frontend and run: 

`npm i aws-amplify --save` . The `--save` adds it to the [package.json](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/frontend-react-js/package.json) file.

Check  `package.json` file to be sure it was added to the package.

![amplify-package](https://user-images.githubusercontent.com/113374279/225452221-1e0e94ea-8e32-425c-9964-5463e6b64c2f.png)

### Configure Amplify

  - Add `import { Amplify } from 'aws-amplify';`  to  [App.js](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/frontend-react-js/src/App.js) file


![ampliy-appjs](https://user-images.githubusercontent.com/113374279/225455578-1442f195-b1fc-4b69-a58f-45eca8840c94.png)


- Add the following after the import statement


```
Amplify.configure({
  "AWS_PROJECT_REGION": process.env.REACT_APP_AWS_PROJECT_REGION,
  "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
  "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
  "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
  "oauth": {},
  Auth: {
    // We are not using an Identity Pool
    // identityPoolId: process.env.REACT_APP_IDENTITY_POOL_ID, // REQUIRED - Amazon Cognito Identity Pool ID
    region: process.env.REACT_APP_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
    userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
    userPoolWebClientId: process.env.REACT_APP_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
  }
});

```

![appjs](https://user-images.githubusercontent.com/113374279/225455745-8ee4b69b-93d8-4980-b0a9-88ba52f9f158.png)


- Edit the [docker-compose.yml](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/docker-compose.yml) file adding the following values:

```
REACT_APP_AWS_PROJECT_REGION: "${AWS_DEFAULT_REGION}"
REACT_APP_AWS_COGNITO_REGION: "${AWS_DEFAULT_REGION}"
REACT_APP_AWS_USER_POOLS_ID:  "us-east-1_FQorSgYUt"
REACT_APP_CLIENT_ID: "6emm8hogul9h9jgrrdlsj2t0k4"

```

- User pool id and client id can be found on the user pool section on AWS

![user-poolid](https://user-images.githubusercontent.com/113374279/225461345-d3d6d9f8-8513-47d5-b445-11d17a311bbe.png)

- To get Client id,click into the user pool, click on `app integration`

![app-integration](https://user-images.githubusercontent.com/113374279/225461627-1c02242c-df96-4d64-a0a2-61fa79e8a20b.png)


- Scroll down to `App clients and analytics`


![client-id](https://user-images.githubusercontent.com/113374279/225461554-b33a4c05-6916-40d8-b2d9-f5bc31304e39.png)


- ids are not sensitive hence no need to protect them


![docker-compose](https://user-images.githubusercontent.com/113374279/225458578-2d30c6cd-e3b4-4ffe-9460-f77bafce9846.png)


### Conditionally show components on the metrics of being logged in or out 

- Start by editing [HomeFeedPage.js](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/frontend-react-js/src/pages/HomeFeedPage.js)

- Add the import statement `import { Auth } from 'aws-amplify';` into the `HomeFeedPage.js` file.

- Delete the following code 

![cookiescheckauth-homefeed](https://user-images.githubusercontent.com/113374279/225464761-8b72b215-8cc8-4c5c-84bf-72e82e0e66b3.png)

- Add this:

```
// check if we are authenticated
const checkAuth = async () => {
  Auth.currentAuthenticatedUser({
    // Optional, By default is false. 
    // If set to true, this call will send a 
    // request to Cognito to get the latest user data
    bypassCache: false 
  })
  .then((user) => {
    console.log('user',user);
    return Auth.currentAuthenticatedUser()
  }).then((cognito_user) => {
      setUser({
        display_name: cognito_user.attributes.name,
        handle: cognito_user.attributes.preferred_username
      })
  })
  .catch((err) => console.log(err));
};

```

![homefeed-correct](https://user-images.githubusercontent.com/113374279/225466095-780cfbd9-c113-4f2d-a44d-494e5c452954.png)


- Edit/Update `ProfileInfo.js`

- Add import statement `import { Auth } from 'aws-amplify';`

- Remove the old code

![cookies-profileinfo](https://user-images.githubusercontent.com/113374279/225466977-ca0516c8-ff3f-4237-b439-7b8b480fba00.png)

- Add this one

![newcode-profilejs](https://user-images.githubusercontent.com/113374279/225466861-0e0b3b49-a7c5-4ff2-8acd-872e0006d13d.png)

- `docker compose up`

![viola!](https://user-images.githubusercontent.com/113374279/225468190-2eaecb36-dd47-4f6a-bad1-beba9d3c5cfe.png)


### Signin Page:

-  In [SigninPage.js](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/frontend-react-js/src/pages/SigninPage.js) replace the import cookie line with 

`import { Auth } from 'aws-amplify';`

![newcode-signinpagejs](https://user-images.githubusercontent.com/113374279/225574616-02cffdef-657f-4c5b-8b70-62bf5fb02258.png)


- Replace the onsubmit code with the one below


```
const onsubmit = async (event) => {
  setErrors('')
  event.preventDefault();
  try {
    Auth.signIn(email, password)
      .then(user => {
        localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
        window.location.href = "/"
      })
      .catch(err => { console.log('Error!', err) });
  } catch (error) {
    if (error.code == 'UserNotConfirmedException') {
      window.location.href = "/confirm"
    }
    setErrors(error.message)
  }
  return false
}

```
- Old code remove

![signin-cookiesremoveunsumit](https://user-images.githubusercontent.com/113374279/225572838-545f42ba-bb31-44da-ac60-cfbb90e0da82.png)


- Replace with:

![onsumit-correct](https://user-images.githubusercontent.com/113374279/225574391-a7977396-1ef2-4c10-a299-5ddb7a073509.png)

- Save and go back to the frontend URL, try to sign in, and this should be the output  

![viola2](https://user-images.githubusercontent.com/113374279/225576526-dbae1240-fd24-4c84-9b2d-5121a76b2283.png)


- Create a user. Go to user-pool on AWS to add a user


![create-user1](https://user-images.githubusercontent.com/113374279/225578393-45247dd2-6122-464e-972f-9cb7eee16f25.png)


![create-user2](https://user-images.githubusercontent.com/113374279/225578561-df52e3bf-32ed-4ee9-9a7e-056c2955b034.png)


- Signing in with the created user will create this error.


![error-signin](https://user-images.githubusercontent.com/113374279/225579152-93551663-650c-426c-bf0b-afa19351b7ce.png)

- Fix

This line of code caused the error, this is because the token hasn't been set yet. This issue will be fixed subsequently but there is a short cut or a quick fix so that testing can contiune.

![local-storage](https://user-images.githubusercontent.com/113374279/225581838-be3774ba-f745-4f06-b300-aa9973464306.png)


The fix was from [here](https://stackoverflow.com/questions/40287012/how-to-change-user-status-force-change-password)

```
aws cognito-idp admin-set-user-password \
  --user-pool-id <your-user-pool-id> \
  --username <username> \
  --password <password> \
  --permanent
  
```  
- Executed like this:

![cooodeee](https://user-images.githubusercontent.com/113374279/225583147-41f93eaf-befa-418b-87b0-328f5ba157eb.png)

- Test frontend URL again

![signin-final](https://user-images.githubusercontent.com/113374279/225583922-219586bd-3e35-477c-92b8-1dd4228b1000.png)

- Click on signout. At this point we can sign out successfully.

- Also at this point, the username section said "@handle" instead of the actual users handle because  "prefered username" want selected     as an attribute. We would fix that.

- Go back to created user created user, set a name and set additional attribute and a value.

- Edit user attribute

![edit-userattributes](https://user-images.githubusercontent.com/113374279/225587764-414bb342-4e23-4a58-a4b5-f4e4a1b16cf1.png)


- Edit `Optional attributes` and choose `preffered namme`

- `Save Changes`


![jollof](https://user-images.githubusercontent.com/113374279/225590237-4b232b05-f505-4a52-8dc7-616d9906c597.png)

- Sign out. Delete created user because we are about to create the signup function next, in order to add a user with the signup button.


### Signup Page

- Editing [SignupPage.js](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/frontend-react-js/src/pages/SignupPage.js) file.

- Replace the import cookie line with `import { Auth } from 'aws-amplify';` just  like the signin page.

- Replace this code 


![signup-oldcode](https://user-images.githubusercontent.com/113374279/225593362-773b82a4-1dab-4cb0-89aa-e69f07b0fd80.png)


- With

```
const onsubmit = async (event) => {
  event.preventDefault();
  setErrors('')
  try {
    const { user } = await Auth.signUp({
      username: email,
      password: password,
      attributes: {
        name: name,
        email: email,
        preferred_username: username,
      },
      autoSignIn: { // optional - enables auto sign in after user is confirmed
        enabled: true,
      }
    });
    console.log(user);
    window.location.href = `/confirm?email=${email}`
  } catch (error) {
      console.log(error);
      setErrors(error.message)
  }
  return false
}

```

### Confirmation Page

- In [ConfirmationPage.js](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/frontend-react-js/src/pages/ConfirmationPage.js) file,  replace the import cookie line with `import { Auth } from 'aws-amplify';`

- Replace the resend code with the lines of code below


```
const resend_code = async (event) => {
  setErrors('')
  try {
    await Auth.resendSignUp(email);
    console.log('code resent successfully');
    setCodeSent(true)
  } catch (err) {
    // does not return a code
    // does cognito always return english
    // for this to be an okay match?
    console.log(err)
    if (err.message == 'Username cannot be empty'){
      setErrors("You need to provide an email in order to send Resend Activiation Code")   
    } else if (err.message == "Username/client id combination not found."){
      setErrors("Email is invalid or cannot be found.")   
    }
  }
}

```

- Replace the onsubmit code with the lines of code below:


```
const onsubmit = async (event) => {
  event.preventDefault();
  setErrors('')
  try {
    await Auth.confirmSignUp(email, code);
    window.location.href = "/"
  } catch (error) {
    setErrors(error.message)
  }
  return false
}

```
- Save and refresh

![error-message](https://user-images.githubusercontent.com/113374279/225599750-92cac9a4-b3c4-4bea-8184-618a81132774.png)

- This is the error that will be encountered.

- The error was from the initial user pool created.  "email" and "username"  was used initially for for signup options, as opposed to just email.  Delete the old  user pool and create another one. Make provision  to add the "preferred username" attribute in this one.

- Update  user pool and client ID env var, `docker compose up` and refreshe the web page
  
![confirm-email](https://user-images.githubusercontent.com/113374279/225601431-fcedd6be-a2fb-443d-87fe-d323d365f69f.png)

![email-vericode](https://user-images.githubusercontent.com/113374279/225601642-ebcb80ad-eea1-44f1-8d8b-2f36b1a009ea.png)

- Sign in shoud work at this point, also user should have been created.

![user-created](https://user-images.githubusercontent.com/113374279/225602390-ff8df166-8cf1-4453-bccd-7125cd805eb2.png)



### Recovery Page

- Create a recovery page.  This feature is for users who forget their password.

- Edit [RecoverPage.js](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/frontend-react-js/src/pages/RecoverPage.js) file by adding the `import { Auth } from 'aws-amplify';` import statement.

- Add the "send code" and "confirm code".

- Send code

```
const onsubmit_send_code = async (event) => {
    event.preventDefault();
    setErrors('')
    Auth.forgotPassword(username)
    .then((data) => setFormState('confirm_code') )
    .catch((err) => setErrors(err.message) );
    return false
  }
  
```

- Confirm code

```
  const onsubmit_confirm_code = async (event) => {
    event.preventDefault();
    setErrors('')
    if (password == passwordAgain){
      Auth.forgotPasswordSubmit(username, code, password)
      .then((data) => setFormState('success'))
      .catch((err) => setErrors(err.message) );
    } else {
      setErrors('Passwords do not match')
    }
    return false
  }

```

![send-code](https://user-images.githubusercontent.com/113374279/225607411-f25c6400-e85a-4091-9534-79c54a99cc29.png)

- Save and commit  changes.

- Go back to the frontend page, and click on login

- Click on forgot password

- An email for password recovery should drop in your mail box.

![recovery-code](https://user-images.githubusercontent.com/113374279/225608603-71d94b02-d858-46c7-845b-8aee277ff515.png)


![password-reset](https://user-images.githubusercontent.com/113374279/225608475-5f3fff79-120e-4318-8714-7e361e584d2f.png)

- Change password and log in with the new password.


### Authenticating Server Side

- Add this code to the [HomeFeed.js](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/frontend-react-js/src/pages/HomeFeedPage.js) file to pass along the access token

```
headers: {
          Authorization: `Bearer ${localStorage.getItem("access_token")}`
        },
        
```        


### Configure backend server

- Add `Flask-AWSCognito` to the [requirements.txt](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/requirements.txt) in the backend  

- Change directory into the backend

`cd backend-flask`

- Then run:

`pip install -r requirements.txt`


In the [app.py](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/app.py) change the cors to this


```
cors = CORS(
  app, 
  resources={r"/api/*": {"origins": origins}},
  headers=['Content-Type', 'Authorization'], 
  expose_headers='Authorization',
  methods="OPTIONS,GET,HEAD,POST"
)

```

- Import statements

`import sys`


`from lib.cognito_jwt_token import CognitoJwtToken, extract_access_token, TokenVerifyError`


```
cognito_jwt_token = CognitoJwtToken(
  user_pool_id=os.getenv("AWS_COGNITO_USER_POOL_ID"), 
  user_pool_client_id=os.getenv("AWS_COGNITO_USER_POOL_CLIENT_ID"),
  region=os.getenv("AWS_DEFAULT_REGION")
)
```

- Update Cors, replace the old code with this:


```
cors = CORS(
  app, 
  resources={r"/api/*": {"origins": origins}},
  headers=['Content-Type', 'Authorization'], 
  expose_headers='Authorization',
  methods="OPTIONS,GET,HEAD,POST"
)

```

- Replace the code at api/home/activities with this:


```
@app.route("/api/activities/home", methods=['GET'])
@xray_recorder.capture('activities_home')
def data_home():
  access_token = extract_access_token(request.headers)
  try:
    claims = cognito_jwt_token.verify(access_token)
    # authenicatied request
    app.logger.debug("authenicated")
    app.logger.debug(claims)
    app.logger.debug(claims['username'])
    data = HomeActivities.run(cognito_user_id=claims['username'])
  except TokenVerifyError as e:
    # unauthenicatied request
    app.logger.debug(e)
    app.logger.debug("unauthenicated")
    data = HomeActivities.run()
  return data, 200

```

-  In backkend-flask make a [Lib](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/tree/main/backend-flask/lib) folder.  Make a file in the folder and name it [cognito_jwt_token.py](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/lib/cognito_jwt_token.py) add the following codes into the file

```
import time
import requests
from jose import jwk, jwt
from jose.exceptions import JOSEError
from jose.utils import base64url_decode

class FlaskAWSCognitoError(Exception):
  pass

class TokenVerifyError(Exception):
  pass

def extract_access_token(request_headers):
    access_token = None
    auth_header = request_headers.get("Authorization")
    if auth_header and " " in auth_header:
        _, access_token = auth_header.split()
    return access_token

class CognitoJwtToken:
    def __init__(self, user_pool_id, user_pool_client_id, region, request_client=None):
        self.region = region
        if not self.region:
            raise FlaskAWSCognitoError("No AWS region provided")
        self.user_pool_id = user_pool_id
        self.user_pool_client_id = user_pool_client_id
        self.claims = None
        if not request_client:
            self.request_client = requests.get
        else:
            self.request_client = request_client
        self._load_jwk_keys()


    def _load_jwk_keys(self):
        keys_url = f"https://cognito-idp.{self.region}.amazonaws.com/{self.user_pool_id}/.well-known/jwks.json"
        try:
            response = self.request_client(keys_url)
            self.jwk_keys = response.json()["keys"]
        except requests.exceptions.RequestException as e:
            raise FlaskAWSCognitoError(str(e)) from e

    @staticmethod
    def _extract_headers(token):
        try:
            headers = jwt.get_unverified_headers(token)
            return headers
        except JOSEError as e:
            raise TokenVerifyError(str(e)) from e

    def _find_pkey(self, headers):
        kid = headers["kid"]
        # search for the kid in the downloaded public keys
        key_index = -1
        for i in range(len(self.jwk_keys)):
            if kid == self.jwk_keys[i]["kid"]:
                key_index = i
                break
        if key_index == -1:
            raise TokenVerifyError("Public key not found in jwks.json")
        return self.jwk_keys[key_index]

    @staticmethod
    def _verify_signature(token, pkey_data):
        try:
            # construct the public key
            public_key = jwk.construct(pkey_data)
        except JOSEError as e:
            raise TokenVerifyError(str(e)) from e
        # get the last two sections of the token,
        # message and signature (encoded in base64)
        message, encoded_signature = str(token).rsplit(".", 1)
        # decode the signature
        decoded_signature = base64url_decode(encoded_signature.encode("utf-8"))
        # verify the signature
        if not public_key.verify(message.encode("utf8"), decoded_signature):
            raise TokenVerifyError("Signature verification failed")

    @staticmethod
    def _extract_claims(token):
        try:
            claims = jwt.get_unverified_claims(token)
            return claims
        except JOSEError as e:
            raise TokenVerifyError(str(e)) from e

    @staticmethod
    def _check_expiration(claims, current_time):
        if not current_time:
            current_time = time.time()
        if current_time > claims["exp"]:
            raise TokenVerifyError("Token is expired")  # probably another exception

    def _check_audience(self, claims):
        # and the Audience  (use claims['client_id'] if verifying an access token)
        audience = claims["aud"] if "aud" in claims else claims["client_id"]
        if audience != self.user_pool_client_id:
            raise TokenVerifyError("Token was not issued for this audience")

    def verify(self, token, current_time=None):
        """ https://github.com/awslabs/aws-support-tools/blob/master/Cognito/decode-verify-jwt/decode-verify-jwt.py """
        if not token:
            raise TokenVerifyError("No token provided")

        headers = self._extract_headers(token)
        pkey_data = self._find_pkey(headers)
        self._verify_signature(token, pkey_data)

        claims = self._extract_claims(token)
        self._check_expiration(claims, current_time)
        self._check_audience(claims)

        self.claims = claims 
        return claims
```


- Make changes to [home_activities.py](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/services/home_activities.py) 

- `docker compose up`

![authenticated](https://user-images.githubusercontent.com/113374279/226087799-fbcc2439-e868-464c-83a9-78b6ab7b5f70.png)

  
  
  
































