# GoogleAuth
# Google Auth Lesson

In this lesson, we will learn how to implement Google authentication in a web application using OAuth 2.0. Follow the steps below to set up Google authentication in your project.

## Step 1: Create Google API Credentials

1. Go to the [Google API Console](https://console.cloud.google.com/).
2. Navigate to the **Credentials** page.
3. Click on **Create credentials > OAuth client ID**.
4. Select the **Web application** application type.
5. Name your OAuth 2.0 client and click **Create**.

Once the configuration is complete, take note of the client ID that was created. You will need the client ID for the next steps.

## Step 2: Load Google Platform Library

Add the following script tag to your `index.html` file to load the Google Platform Library:

```html
<script src="<https://accounts.google.com/gsi/client>" async defer></script>

```

## Step 3: Install Required Dependencies

To ensure that our user's authentication token is working correctly, we need to install the `jwt-decode` library. Run the following command to install the dependency:

```bash
npm install jwt-decode
```

## Step 4: Implement Google Login

In your code, create a component called `GoogleLogin` and import the necessary dependencies. Set up the initial state for the user object using the `useState` hook.

Implement the `handleCallbackResponse` function to handle the response from the Google Sign-In API. Decode the encoded JWT(Json Web Token) ID token to extract user information and update the user state. Hide the sign-in div once the user is logged in.

Implement the `handleSignOut` function to handle the sign-out action. Reset the user state and show the sign-in div.

Use the `useEffect` hook to fetch the client ID from your backend and initialize the Google Sign-In API with the client ID. Render the sign-in button using the `google.accounts.id.renderButton` method.

Here is the updated code for the `GoogleLogin` component:

```jsx
import React, { useState, useEffect } from 'react';
import { jwtDecode } from 'jwt-decode';

function GoogleLogin() {
  const [user, setUser] = useState({});

  function handleCallbackResponse(response) {
    console.log("Encoded JWT ID token: " + response.credential);
    var userObject = jwtDecode(response.credential);
    console.log(userObject);
    setUser(userObject);
    document.getElementById("signInDiv").hidden = true;
  }

  function handleSignOut(event) {
    setUser({});
    document.getElementById("signInDiv").hidden = false;
  }

  useEffect(() => {
    const fetchData = async () => {
      const response = await fetch('<http://127.0.0.1:8000/authentication/get_google_client_id/>', {
        'Cross-Origin-Opener-Policy': 'unsafe-none',
        method: 'POST',
      });

      const data = await response.json();
      const client_id = data.client_id;

      google.accounts.id.initialize({
        client_id,
        callback: handleCallbackResponse,
      });

      google.accounts.id.renderButton(document.getElementById("signInDiv"), { theme: "outline", size: 'large' });
    };

    fetchData();
  }, []);

  return (
    <div className='App'>
      <div id="signInDiv"></div>
      {Object.keys(user).length !== 0 && (
        <button onClick={(e) => handleSignOut(e)}>Sign out?</button>
      )}

      {user && (
        <div>
          <img src={user.picture} alt="User Profile" />
          <h3>{user.name}</h3>
        </div>
      )}
    </div>
  );
}

export default GoogleLogin;

```

## Step 5: Backend Setup

In your backend, create a view and a URL to get the Google client ID. The client ID should be stored in your environment variables.

Here is an example view:

```python
from rest_framework.response import Response
from rest_framework.decorators import api_view
from django.conf import settings
import os

@api_view(['POST'])
def get_google_client_id(request):
    client_id = os.environ.get('GOOGLE_CLIENT_ID')
    response = Response({'client_id': client_id})
    response['Cross-Origin-Opener-Policy'] = 'same-origin-allow-popups'
    return response

```

Add the URL for the view to your project's `urls.py` file.

heres the url 

```python
from django.urls import path
from . import views

urlpatterns = [
    path('get_google_client_id/', views.get_google_client_id, name='get_google_client_id'),
]
```

our main project urls

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path('authentication/', include('authentication.urls')), 
]
```

## Step 6: Wrap up :)

Now that you have completed the steps above, your web application should have Google authentication implemented. When a user clicks on the sign-in button, they will be prompted to sign in with their Google account. Once signed in, their profile picture and name will be displayed.

Feel free to customize the styling and layout of your web application to make it visually appealing.

Congratulations! You have successfully implemented Google authentication in your web application.
![Screenshot_2023-11-11_at_9 02 15_AM](https://github.com/Jan-Dro/GoogleAuth/assets/126230256/432d46d7-5fea-4945-a190-d0309639015e)

![Screenshot_2023-11-11_at_9 02 38_AM![Screenshot_2023-11-11_at_9 02 51_AM](https://github.com/Jan-Dro/GoogleAuth/assets/126230256/8602607a-70d1-4cbc-a48e-dc51630a2fc8)
](https://github.com/Jan-Dro/GoogleAuth/assets/126230256/ac6538d6-2c15-490f-9326-f41f628458d6)
![Screenshot_2023-11-11_at_9 03 01_AM](https://github.com/Jan-Dro/GoogleAuth/assets/126230256/0a699074-bcff-4f3d-be02-ab4605641eae)
