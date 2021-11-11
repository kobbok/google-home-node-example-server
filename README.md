Example is taken and adapted from the [node-oidc-provider](https://github.com/panva/node-oidc-provider) GitHub repository.

# Setup
## Server
Do a `npm install`. Install mongodb (or change to a different adapter) and set that up.

In `support/configuration.js` we need to create a new client:
```js
    {
      client_id: 'f0fd57814c6670254e65510947a06656da6c5b299e98793b8cf1fe095461d9cd',
      client_secret: '26df92eef53fd8cf4554490b81090df591f16738891a2b8cd70bee1b58644385',
      grant_types: ['refresh_token', 'authorization_code'],
      redirect_uris: ['https://oauth-redirect.googleusercontent.com/r/YOUR_PROJECT_ID', 'https://oauth-redirect-sandbox.googleusercontent.com/r/YOUR_PROJECT_ID'],
    }
```

The `CLIENT_ID` and `CLIENT_SECRET` you generate yourself. They can be any string, above strings were generated with nodejs `require(crypto).randomBytes(32).toString('hex')`.

For `redirect_uris` the documentation is [here](https://developers.google.com/assistant/smarthome/develop/implement-oauth#handle_authorization_requests). Specifically point 4:
> Confirm that the URL specified by the redirect_uri parameter has the following form:
> 
> https://oauth-redirect.googleusercontent.com/r/YOUR_PROJECT_ID  
> https://oauth-redirect-sandbox.googleusercontent.com/r/YOUR_PROJECT_ID

You get the `PROJECT_ID` from your [Google Actions Console](https://console.actions.google.com/u/0/), open the project you want and copy the `PROJECT_ID` from the url.
```
    https://console.actions.google.com/u/0/project/newagent-xxxxxx/overview
                                                   ^^^^^^^^^^^^^^^
```
For above example `YOUR_PROJECT_ID` would be `newagent-xxxxxx`.

## Starting the server
If using MongoDB, start the server with `MONGODB_URI=mongodb://localhost/<database-name> PORT=3000 npm start`.

## Console
Make sure the action you create on the [Google Actions Console](https://console.actions.google.com/u/0/) is of the type "Smart Home".

### Account linking
Provide your `CLIENT_ID` and `CLIENT_SECRET` in their respective fields.

Authorization url is `https://<url-to-server>/auth`.

Token url is `https://<url-to-server>/token`.

Under Configure your client (optional) add the scope `profile`. If no scopes are provided all requests will fail with `access_denied`.

### Actions
Fill in fulfillment URL: `https://<url-to-server>/fulfillment`

# Testing
Testing needs to be done on your android device:
1. Download [Google Home app](https://play.google.com/store/apps/details?id=com.google.android.apps.chromecast.app&hl=en&gl=US).
2. In the app click on the `+` in the top left corner
3. Select Set up device
4. Select Works with Google
5. In the top right corner click on search and search for `[test]`
6. Select `[test] <project-name>`
7. The OAuth2 process will now begin. For login provide any username and password (the values are discarded and a "default" user is returned).
8. Once the OAuth2 process is done a request to `POST /fulfillment` will be received.

If you can't see `[test] <project-name>`, try going to the Test tab on your Google console and clicking on "Talk to Test", the request will fail. Refresh the list on your device. The project should now be visible, if it is not, good luck. lol
