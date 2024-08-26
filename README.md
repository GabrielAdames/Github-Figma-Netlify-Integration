# Github + Netlify + Figma - Serversless Event-Driven Integration

This guide outlines the steps of detecting Github events, using Netlify as the event listener, a Netlify function for event processing and the Figma API for Interaction. The example includes a Github webhook for when a repository is starred, using Netlify to listen to that event and executing a function to interact with Figma API. This event driven integration will allow developers and designers to obtain real-time notifications and data synchronization based on specific user events on different services.

## Use Case for Figma Designers

- Pull Request Events
  - Opening a pull request - Designers will be informed when changes are proposed in the codebase pertaining to UI/UX so they can provide feedback.
  - Merging a pull request - Informs Designers of changes integrated to the live codebase to ensure implemented changes match their designs.


- Issue Events
  - Opening an issue - Can indicate work that needs a designers input or design changes if labeled UI, UX or Design.

- Push Events
  - Pushed Commits - Informs Designers when changes to the UI/UX have been pushed (meaning uploaded).

- Comment Events
  - Comments on Issues or Pull Requests - Inform deisngesrs on discussion points or feedback pertaining to design aspects

- Release Events
  - Informing Designers when a new release is published such as updates or software versions.

## Use Case for Developers

- Comment Events
  - Informing Developers when a designers provide feedbakc, request changes that may impact implementation and indicate when design issues have been addressed.

- Component Updates
  - Informing Developers when key components in the design system are updated so they can keep code in sync with the design and ensure consistency across websites and applications.

- File Updates
  - Informing Developers when significant changes are made to design files that need to be implemented into code.

## Relevant Terms!

- Netlify Function - Is a serverless function meaning we dont manage the server infrastructure. You only pay for the compute time you consume.This function we deploy is hosted on Netlify's servers. It listens for incoming requests at the endpoint we specify.

- Webhook - A way for an app, in this case Github, to send real-time data to other apps. Github webhooks notify our serverless function when an event occurs. Github sends a POST request to the netlify function (this is managed by Githubs servers)

- API Integration - Connecting different software systems via their APIs - Githubs API via webhooks and Figma's API for comments

## Scenario 1

A users stars your github repository. This action triggers the webhook we configured in Github to send a POST request to the Netlify function URL. Netlify servers recive the webhook event and execute the function. The function processes the event and makes an API call to Figma to post a comment on a specified file. The Figma server receives the API call and adds the comment to the specified file. Now a Designer on figma can be informed when a Github repository has been starred.

### 1. Setup your Github Repository, Figma file and Neltify Site

- Go to GitHub and create a new repository.
- Clone the repository to your local machine
- Sign up for a Figma account if you don't already have one.
- Create a new Figma file in your account.
- Sign up for a Netlify account if you don't already have one.
- Create a new site from Git in Netlify and connect it to your GitHub repository.

### 2. Install Netlify CLI (Command Line Interface)

Go to your directory, for instance...
- cd documents/webhook/webhook-test
- npm init -y (to create a package.json file)
  - npm install netlify -cli -g (for global installation)
- netlify link (link the repository to the netlify site)
- netlify login

### 3. Create a Netlify Function

- Go to your project directory
- mkdir netlify\functions (create a netlify functions folder)
- New-Item -Path .\netlify\functions\webhook.js -ItemType File (Create a file called webhook.js using powershell)

### 4. Write a Webhook Function

Be sure to add your Figma field ID - found on the Figma file URL after /design/ path. Also add your Figma API Key (as seen on step 7)

The following function has been saved on the directory as webhook.js

PLEASE NOTE - tthis repository does not contain any actual JavaScript code or other proprietary content from the project. Instead, I have provided comments and descriptions about the functionality and logic that I implemented in the original project.

The descriptions are meant to give an overview of the key aspects of the work I performed, without revealing any specific implementation details. This approach ensures compliance with confidentiality agreements while allowing for a discussion of the techniques and methodologies 

``` javascript
// Import the 'node-fetch' module to make HTTP requests.

// Define the main handler function that will handle the incoming event.

// Log the received event for debugging purposes.

// Check if the HTTP method of the request is not 'POST'.

// If it's not a POST request, return a 200 status code with a message indicating that only POST is accepted.

// Try block to handle potential errors during event body parsing and comment addition.

// Parse the body of the event to get the payload.

// Check if the action in the payload is 'started' (indicating a GitHub star event).

// If the action is 'started', call the function to add a comment to Figma.

// Log the response from Figma for debugging purposes.

// Return a 200 status code with the response from Figma.

// If the action is not 'started', return a 200 status code with a message indicating it’s not a star event.

// Catch any errors that occur during JSON parsing or comment addition.

// Log the error for debugging purposes.

// Return a 400 status code with an error message and the error details.

// Define a helper function to add a comment to a Figma file.

// Retrieve the Figma File ID and API key from environment variables.

// Define the comment to be added to the Figma file.

// Log the file ID and API key being used (for debugging purposes).

// Try block to handle any potential errors during the HTTP request to the Figma API.

// Make a POST request to the Figma API to add the comment.

// Set the request content type to JSON.

// Include the Figma API key in the request headers.

// Convert the comment object to a JSON string for the request body.

// Check if the response from the Figma API is not successful (status code not in the 200 range).

// If the response is not successful, read the error text from the response.

// Log the error for debugging purposes.

// Throw an error with the response status and error text.

// Parse the response data from the Figma API as JSON.

// Log the successful addition of the comment to the Figma file.

// Return the response data.

// Catch any errors that occur during the HTTP request or response handling.

// Log the error for debugging purposes.

// Throw the error to be handled by the calling function.
```

### 5.Deploy the Netlify Function

- netlify deploy --prod
- for the publish directory, just hit the "." key and press enter

### 6.Setup GitHub Webhook and Test with Beeceptor

1. Create a Mock Payload URL
2. Go to Beeceptor and create a mock endpoint.
3. Add GitHub Webhook
4. Go to your GitHub repository > Settings > Webhooks > Add webhook.
5. Enter the Beeceptor payload URL
6. Set content type to application/json.
7. Select "Let me select individual events" and check the "Watch" event.
8. Unstar and Star Your Repository
9. Check beeceptor for any POST logs to confirm that the webhook is working.

### 7.Test the Figma API

Generate a New Figma Personal Access Token:

1. Go to Figma > Click your profile photo > Settings > Personal Access Tokens > Generate a new token. Be sure to save this token!
2. Set the scopes (file_read, webhooks).

Test Figma API with Postman:

1. Go to Postman.
2. Create a new request.
3. Set the method to GET and the URL to https://api.figma.com/v1/files/YourFigmaFileIdHere.
4. In the Headers tab, add X-Figma-Token with the value of your personal access token.
5. Send the request to ensure it works.

### 8. Update GitHub Webhook Payload URL

Update GitHub Webhook:

- Edit the webhook payload URL to https://netlifyspecificurl.netlify.app/.netlify/functions/webhook.

Check Netlify Logs:

- Go to Netlify > Click your site > Logs > Functions > webhook > check function log.

Unstar and Star Your Repository:

- Ensure the function is triggered and a comment is added to your Figma file.

## ADDING MORE GITHUB WEBHOOK EVENTS

Here is how to add more events to your functions.



# ADDRESSING ERRORS
The following are steps I took to troubleshoot errors.

## 1. Netlify

Make sure your repository is connected to the netlify site. Next make sure you installed the netlify CLI using netlify link

```text
netlify link
```

Install Netlify CLI
```text
npm netlify-cli -g
```

Make sure your site has two environment variables. One for FIGMA_ID and another for FIGMA_API_KEY

Make sure any changes you make are pushed to github then deployed to netlify
```text
netlify deploy --prod
```

Go to netlify functions, grab the URL from the function and use that as the webhook payload URL.

Create a Netlify.toml file in your directory folder. Add the following
```text
[build]
  functions = "netlify/functions"

[dev]
  functions = "netlify/functions"
```

## 2. Creating a local development server

```text
netlify dev
```

In your command prompt use netlify dev to start your local development server. Then open a new page on your browser and type the following with your function at the end
```text
http://localhost:8888/.netlify/functions/yourfunctionhere
```

This will invoke the function locally. You should see a message on the page indicating the endpoint only accepts POST requests {"message":"This endpoint only accepts POST requests."}

## 3. Package.json

add the following in your package.json file, view json file for reference
```text
"type": "module",
```

### incompatible dependencies
Next you want to remove incompatible dependencies. There was a problem with a package that our function relied on, specifically, fetch-blob and formdata-polyfill. To resolve the issue we need to ensure compatibility between Node.js, node-fetch and related dependencies. The approach we used is uninstalling our current node-fetch and related packages

```text
npm uninstall node-fetch formdata-polyfill
```

Then we installed a compatible verison of node-fetch which was the latest V2 version of node-fetch which does not require fetch-blob

```text
npm install node-fetch@2
```

Then we updated our function to use common.js

## 4. Powershell command to test the function

Paste this to your powershell - make sure the function at the end of the url is correct
```text
$headers = @{'Content-Type' = 'application/json'}
$body = '{"action": "started"}'
Invoke-RestMethod -Uri "http://localhost:8888/.netlify/functions/yourfunctionhere" -Method Post -Headers $headers -Body $body
```

If the function above is working correctly you should see a response from your add comment function printed in the powershell window.

This would indidcate you deployed a netlify function tha integrates with the FIGMA API, the function parses the incoming post request and interacts with the FIGMA API to add comments. You tested the function locally using powershell and confirmed it adds comments to your figma file.
