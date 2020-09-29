
  <div class="page">
    <div id="preview-page" class="preview-page" data-autorefresh-url="">

    

      <div role="main" class="main-content">
        <div class="container new-discussion-timeline experiment-repo-nav">
          <div class="repository-content">
            <div id="readme" class="readme boxed-group clearfix announce instapaper_body md">              
              <article class="markdown-body entry-content" itemprop="text" id="grip-content">
                # &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#build-a-photo-sharing-app-with-react-and-aws-amplify)Build a Photo Sharing App with React and AWS Amplify
In this workshop we'll learn how to build a full stack cloud application with React, GraphQL, &amp; [Amplify](https://docs.amplify.aws/).
[![](amplify-banner.jpg)](amplify-banner.jpg)
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#overview)Overview
We'll start from scratch, using the [Create React App CLI](https://reactjs.org/docs/create-a-new-react-app.html#create-react-app) to create a new React web project. We'll then, step by step, use the [Amplify CLI](https://github.com/aws-amplify/amplify-cli) to build out and configure our cloud infrastructure and then use the [Amplify JS Libraries](https://github.com/aws-amplify/amplify-js) to connect the React client application to the APIs we create using the CLI.
The app will be a basic CRUD + List app with real-time updates. When you think of many types of applications like Instagram, Twitter, or Facebook, they consist of a list of items and often the ability to drill down into a single item view. The app we will be building will be very similar to this, displaying a list of posts with images and data like the name, location, and description of the post. You will also be able to see only a view containing only a list of your own posts.
This workshop should take you anywhere between 2 to 5 hours to complete.
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#environment)Environment
Before we begin, make sure you have the following installed:

- Node.js v10.x or later
- npm v5.x or later
- git v2.14.1 or later

We will be working from a terminal using a [Bash shell](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) to run Amplify CLI commands to provision infrastructure and also to run a local version of the React web app and test it in a web browser.
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#background-needed--level)Background needed / level
This workshop is intended for intermediate to advanced front end &amp; back end developers wanting to learn more about full stack serverless development.
While some level of React and GraphQL is helpful, this workshop requires zero previous knowledge about React or GraphQL.
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#topics-well-be-covering)Topics we'll be covering:

- GraphQL API with AWS AppSync
- Authentication
- Object (image) storage
- Authorization
- Hosting
- Deleting the resources

## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#getting-started---creating-the-react-application)Getting Started - Creating the React Application
To get started, we first need to create a new React project using the [Create React App CLI](https://github.com/facebook/create-react-app).
<div class="highlight highlight-source-shell"><pre>$ npx create-react-app postagram</pre></div>
Now change into the new app directory &amp; install AWS Amplify, AWS Amplify UI React, react-router-dom, emotion, &amp; uuid:
<div class="highlight highlight-source-shell"><pre>$ <span class="pl-c1">cd</span> postagram
$ npm install aws-amplify emotion uuid react-router-dom @aws-amplify/ui-react</pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#installing-the-cli--initializing-a-new-aws-amplify-project)Installing the CLI &amp; Initializing a new AWS Amplify Project
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#installing-the-cli)Installing the CLI
Next, we'll install the AWS Amplify CLI:
<div class="highlight highlight-source-shell"><pre>$ npm install -g @aws-amplify/cli</pre></div>
Now we need to configure the CLI with our credentials.
> 
> If you'd like to see a video walkthrough of this configuration process, click [here](https://www.youtube.com/watch?v=fWbM5DLh25U).

<div class="highlight highlight-source-shell"><pre>$ amplify configure

- Specify the AWS Region: us-east-1 <span class="pl-k">||</span> us-west-2 <span class="pl-k">||</span> eu-central-1
- Specify the username of the new IAM user: amplify-cli-user
<span class="pl-k">&gt;</span> In the AWS Console, click Next: Permissions, Next: Tags, Next: Review, <span class="pl-k">&amp;</span> Create User to create the new IAM user. Then <span class="pl-k">return</span> to the <span class="pl-c1">command</span> line <span class="pl-k">&amp;</span> press Enter.
- Enter the access key of the newly created user:   
<span class="pl-k">?</span> accessKeyId: (<span class="pl-k">&lt;</span>YOUR_ACCESS_KEY_ID<span class="pl-k">&gt;</span>)  
<span class="pl-k">?</span> secretAccessKey: (<span class="pl-k">&lt;</span>YOUR_SECRET_ACCESS_KEY<span class="pl-k">&gt;</span>)
- Profile Name: amplify-cli-user</pre></div>
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#initializing-a-new-project)Initializing A New Project
<div class="highlight highlight-source-shell"><pre>$ amplify init

- Enter a name <span class="pl-k">for</span> the project: postagram
- Enter a name <span class="pl-k">for</span> the environment: dev
- Choose your default editor: Visual Studio Code (or your default editor)
- Please choose the <span class="pl-c1">type</span> of app that youre building: javascript
- What javascript framework are you using: react
- Source Directory Path: src
- Distribution Directory Path: build
- Build Command: npm run-script build
- Start Command: npm run-script start
- Do you want to use an AWS profile<span class="pl-k">?</span> Y
- Please choose the profile you want to use: amplify-cli-user</pre></div>
The Amplify CLI has initialized a new project &amp; you will see a new folder: **amplify** &amp; a new file called `aws-exports.js` in the **src** directory. These files hold your project configuration.
To view the status of the amplify project at any time, you can run the Amplify `status` command:
<div class="highlight highlight-source-shell"><pre>$ amplify status</pre></div>
To view the amplify project in the Amplify console at any time, run the `console` command:
<div class="highlight highlight-source-shell"><pre>$ amplify console</pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#adding-an-aws-appsync-graphql-api)Adding an AWS AppSync GraphQL API
To add a GraphQL API, we can use the following command:
<div class="highlight highlight-source-shell"><pre>$ amplify add api

<span class="pl-k">?</span> Please <span class="pl-k">select</span> <span class="pl-smi">from</span> one of the above mentioned services: GraphQL
<span class="pl-k">?</span> Provide API name: Postagram
<span class="pl-k">?</span> Choose the default authorization <span class="pl-c1">type</span> <span class="pl-k">for</span> the API: API key
<span class="pl-k">?</span> Enter a description <span class="pl-k">for</span> the API key: public
<span class="pl-k">?</span> After how many days from now the API key should expire (1-365): 365 (or your preferred expiration)
<span class="pl-k">?</span> Do you want to configure advanced settings <span class="pl-k">for</span> the GraphQL API: No
<span class="pl-k">?</span> Do you have an annotated GraphQL schema<span class="pl-k">?</span> N 
<span class="pl-k">?</span> Choose a schema template: Single object with fields
<span class="pl-k">?</span> Do you want to edit the schema now<span class="pl-k">?</span> (Y/n) Y</pre></div>
The CLI should open this GraphQL schema in your text editor.
**amplify/backend/api/Postagram/schema.graphql**
Update the schema to the following:
<div class="highlight highlight-source-graphql"><pre><span class="pl-k">type</span> <span class="pl-c1">Post</span> <span class="pl-en">@model</span> {
  <span class="pl-v">id</span>: <span class="pl-c1">ID</span><span class="pl-k">!</span>
  <span class="pl-v">name</span>: <span class="pl-c1">String</span><span class="pl-k">!</span>
  <span class="pl-v">location</span>: <span class="pl-c1">String</span><span class="pl-k">!</span>
  <span class="pl-v">description</span>: <span class="pl-c1">String</span><span class="pl-k">!</span>
  <span class="pl-v">image</span>: <span class="pl-c1">String</span>
}</pre></div>
After saving the schema, go back to the CLI and press enter.
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#deploying-the-api)Deploying the API
To deploy the API, run the push command:
    $ amplify push
    
    ? Are you sure you want to continue? Y
    
    # You will be walked through the following questions for GraphQL code generation
    ? Do you want to generate code for your newly created GraphQL API? Y
    ? Choose the code generation language target: javascript
    ? Enter the file name pattern of graphql queries, mutations and subscriptions: src/graphql/**/*.js
    ? Do you want to generate/update all possible GraphQL operations - queries, mutations and subscriptions? Yes
    ? Enter maximum statement depth [increase from default if your schema is deeply nested]: 2

> 
> Alternately, you can run `amplify push -y` to answer **Yes** to all questions.

Now the API is live and you can start interacting with it!
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#testing-the-api)Testing the API
To test it out we can use the GraphiQL editor in the AppSync dashboard. To open the AppSync dashboard, run the following command:
<div class="highlight highlight-source-shell"><pre>$ amplify console api

<span class="pl-k">&gt;</span> Choose GraphQL</pre></div>
In the AppSync dashboard, click on **Queries** to open the GraphiQL editor. In the editor, create a new post with the following mutation:
<div class="highlight highlight-source-graphql"><pre><span class="pl-k">mutation</span> <span class="pl-en">createPost</span> {
  <span class="pl-v">createPost</span>(<span class="pl-v">input</span>: {
    <span class="pl-c1"><span class="pl-s">name</span></span>: <span class="pl-s"><span class="pl-pds">"</span>My first post<span class="pl-pds">"</span></span>
    <span class="pl-c1"><span class="pl-s">location</span></span>: <span class="pl-s"><span class="pl-pds">"</span>New York<span class="pl-pds">"</span></span>
    <span class="pl-c1"><span class="pl-s">description</span></span>: <span class="pl-s"><span class="pl-pds">"</span>Best burgers in NYC - Jackson Hole<span class="pl-pds">"</span></span>
  }) {
    <span class="pl-v">id</span>
    <span class="pl-v">name</span>
    <span class="pl-v">location</span>
    <span class="pl-v">description</span>
  }
}</pre></div>
Then, query for the posts:
<div class="highlight highlight-source-graphql"><pre><span class="pl-k">query</span> <span class="pl-en">listPosts</span> {
  <span class="pl-v">listPosts</span> {
    <span class="pl-v">items</span> {
      <span class="pl-v">id</span>
      <span class="pl-v">name</span>
      <span class="pl-v">location</span>
      <span class="pl-v">description</span>
    }
  }
}</pre></div>
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#configuring-the-react-applicaion)Configuring the React applicaion
Now, our API is created &amp; we can test it out in our app!
The first thing we need to do is to configure our React application to be aware of our Amplify project. We can do this by referencing the auto-generated `aws-exports.js` file that is now in our src folder.
To configure the app, open **src/index.js** and add the following code below the last import:
<div class="highlight highlight-source-js"><pre><span class="pl-k">import</span> <span class="pl-v">Amplify</span> <span class="pl-k">from</span> <span class="pl-s">'aws-amplify'</span>
<span class="pl-k">import</span> <span class="pl-s1">config</span> <span class="pl-k">from</span> <span class="pl-s">'./aws-exports'</span>
<span class="pl-v">Amplify</span><span class="pl-kos">.</span><span class="pl-en">configure</span><span class="pl-kos">(</span><span class="pl-s1">config</span><span class="pl-kos">)</span></pre></div>
Now, our app is ready to start using our AWS services.
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#interacting-with-the-graphql-api-from-our-client-application---querying-for-data)Interacting with the GraphQL API from our client application - Querying for data
Now that the GraphQL API is running we can begin interacting with it. The first thing we'll do is perform a query to fetch data from our API.
To do so, we need to define the query, execute the query, store the data in our state, then list the items in our UI.
The main thing to notice in this component is the API call. Take a look at this piece of code:
<div class="highlight highlight-source-js"><pre><span class="pl-c">/* Call API.graphql, passing in the query that we'd like to execute. */</span>
<span class="pl-k">const</span> <span class="pl-s1">postData</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-c1">API</span><span class="pl-kos">.</span><span class="pl-en">graphql</span><span class="pl-kos">(</span><span class="pl-kos">{</span> <span class="pl-c1">query</span>: <span class="pl-s1">listPosts</span> <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">;</span></pre></div>
#### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#srcappjs)src/App.js
<div class="highlight highlight-source-js"><pre><span class="pl-c">// src/App.js</span>
<span class="pl-k">import</span> <span class="pl-v">React</span><span class="pl-kos">,</span> <span class="pl-kos">{</span> <span class="pl-s1">useState</span><span class="pl-kos">,</span> <span class="pl-s1">useEffect</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'react'</span><span class="pl-kos">;</span>

<span class="pl-c">// import API from Amplify library</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-c1">API</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'aws-amplify'</span>

<span class="pl-c">// import query definition</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">listPosts</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'./graphql/queries'</span>

<span class="pl-k">export</span> <span class="pl-k">default</span> <span class="pl-k">function</span> <span class="pl-v">App</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-k">const</span> <span class="pl-kos">[</span><span class="pl-s1">posts</span><span class="pl-kos">,</span> <span class="pl-s1">setPosts</span><span class="pl-kos">]</span> <span class="pl-c1">=</span> <span class="pl-en">useState</span><span class="pl-kos">(</span><span class="pl-kos">[</span><span class="pl-kos">]</span><span class="pl-kos">)</span>
  <span class="pl-en">useEffect</span><span class="pl-kos">(</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">{</span>
    <span class="pl-en">fetchPosts</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-kos">}</span><span class="pl-kos">,</span> <span class="pl-kos">[</span><span class="pl-kos">]</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-k">async</span> <span class="pl-k">function</span> <span class="pl-en">fetchPosts</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
    <span class="pl-k">try</span> <span class="pl-kos">{</span>
      <span class="pl-k">const</span> <span class="pl-s1">postData</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-c1">API</span><span class="pl-kos">.</span><span class="pl-en">graphql</span><span class="pl-kos">(</span><span class="pl-kos">{</span> <span class="pl-c1">query</span>: <span class="pl-s1">listPosts</span> <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-en">setPosts</span><span class="pl-kos">(</span><span class="pl-s1">postData</span><span class="pl-kos">.</span><span class="pl-c1">data</span><span class="pl-kos">.</span><span class="pl-c1">listPosts</span><span class="pl-kos">.</span><span class="pl-c1">items</span><span class="pl-kos">)</span>
    <span class="pl-kos">}</span> <span class="pl-k">catch</span> <span class="pl-kos">(</span><span class="pl-s1">err</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
      <span class="pl-smi">console</span><span class="pl-kos">.</span><span class="pl-en">log</span><span class="pl-kos">(</span><span class="pl-kos">{</span> err <span class="pl-kos">}</span><span class="pl-kos">)</span>
    <span class="pl-kos">}</span>
  <span class="pl-kos">}</span>
  <span class="pl-k">return</span> <span class="pl-kos">(</span>
    <span class="pl-c1">&lt;</span><span class="pl-ent">div</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">h1</span><span class="pl-c1">&gt;</span>Hello World<span class="pl-c1">&lt;</span>/<span class="pl-ent">h1</span><span class="pl-c1">&gt;</span>
      <span class="pl-kos">{</span>
        <span class="pl-s1">posts</span><span class="pl-kos">.</span><span class="pl-en">map</span><span class="pl-kos">(</span><span class="pl-s1">post</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">(</span>
          <span class="pl-c1">&lt;</span><span class="pl-ent">div</span> <span class="pl-c1">key</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">id</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>
            <span class="pl-c1">&lt;</span><span class="pl-ent">h3</span><span class="pl-c1">&gt;</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">}</span><span class="pl-c1">&lt;</span>/<span class="pl-ent">h3</span><span class="pl-c1">&gt;</span>
            <span class="pl-c1">&lt;</span><span class="pl-ent">p</span><span class="pl-c1">&gt;</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">location</span><span class="pl-kos">}</span><span class="pl-c1">&lt;</span>/<span class="pl-ent">p</span><span class="pl-c1">&gt;</span>
          <span class="pl-c1">&lt;</span>/<span class="pl-ent">div</span><span class="pl-c1">&gt;</span>
        <span class="pl-kos">)</span><span class="pl-kos">)</span>
      <span class="pl-kos">}</span>
    <span class="pl-c1">&lt;</span>/<span class="pl-ent">div</span><span class="pl-c1">&gt;</span>
  <span class="pl-kos">)</span>
<span class="pl-kos">}</span></pre></div>
In the above code we are using `API.graphql` to call the GraphQL API, and then taking the result from that API call and storing the data in our state. This should be the list of posts you created via the GraphiQL editor.
Next, test the app:
<div class="highlight highlight-source-shell"><pre>$ npm start</pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#adding-authentication)Adding Authentication
Next, let's update the app to add authentication.
To add the authentication service, we can use the following command:
<div class="highlight highlight-source-shell"><pre>$ amplify add auth

<span class="pl-k">?</span> Do you want to use default authentication and security configuration<span class="pl-k">?</span> Default configuration 
<span class="pl-k">?</span> How <span class="pl-k">do</span> you want users to be able to sign <span class="pl-k">in</span> when using your Cognito User Pool<span class="pl-k">?</span> Username
<span class="pl-k">?</span> Do you want to configure advanced settings<span class="pl-k">?</span> No, I am done.   </pre></div>
To deploy the authentication service, you can run the `push` command:
<div class="highlight highlight-source-shell"><pre>$ amplify push

<span class="pl-k">?</span> Are you sure you want to continue<span class="pl-k">?</span> Yes</pre></div>
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#using-the-withauthenticator-component)Using the withAuthenticator component
To add authentication in the React app, we'll go into **src/App.js** and first import the `withAuthenticator` HOC (Higher Order Component) from `@aws-amplify/ui-react`:
<div class="highlight highlight-source-js"><pre><span class="pl-c">// src/App.js, import the withAuthenticator component</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">withAuthenticator</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'@aws-amplify/ui-react'</span></pre></div>
Next, we'll wrap our default export (the App component) with the `withAuthenticator` HOC:
<div class="highlight highlight-source-js"><pre><span class="pl-k">function</span> <span class="pl-v">App</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span><span class="pl-c">/* existing code here, no changes */</span><span class="pl-kos">}</span>

<span class="pl-c">/* src/App.js, change the default export to this: */</span>
<span class="pl-k">export</span> <span class="pl-k">default</span> <span class="pl-en">withAuthenticator</span><span class="pl-kos">(</span><span class="pl-v">App</span><span class="pl-kos">)</span></pre></div>
Next test it out in the browser:
<div class="highlight highlight-source-shell"><pre>npm start</pre></div>
Now, we can run the app and see that an Authentication flow has been added in front of our App component. This flow gives users the ability to sign up &amp; sign in.
Once you sign up, check your email to confirm the sign up.
Now that you have the authentication service created, you can view it any time in the console by running the following command:
<div class="highlight highlight-source-shell"><pre>$ amplify console auth

<span class="pl-k">&gt;</span> Choose User Pool</pre></div>
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#adding-a-sign-out-button)Adding a sign out button
You can also easily add a preconfigured UI component for signing out.
<div class="highlight highlight-source-js"><pre><span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">withAuthenticator</span><span class="pl-kos">,</span> <span class="pl-v">AmplifySignOut</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'@aws-amplify/ui-react'</span><span class="pl-kos">;</span>

<span class="pl-c">/* Somewhere in the UI */</span>
<span class="pl-c1">&lt;</span><span class="pl-ent">AmplifySignOut</span> /<span class="pl-c1">&gt;</span></pre></div>
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#styling-the-ui-components)Styling the UI components
Next, let's update the UI component styling by setting styles for the `:root` pseudoclass.
To do so, open **src/index.css** and add the following styling:
<div class="highlight highlight-source-css"><pre>:<span class="pl-c1">root</span> {
  <span class="pl-c1">--amplify-primary-color</span>: <span class="pl-pds"><span class="pl-kos">#</span>006eff</span>;
  <span class="pl-c1">--amplify-primary-tint</span>: <span class="pl-pds"><span class="pl-kos">#</span>005ed9</span>;
  <span class="pl-c1">--amplify-primary-shade</span>: <span class="pl-pds"><span class="pl-kos">#</span>005ed9</span>;
}</pre></div>
> 
> To learn more about theming the Amplify React UI components, check out the documentation [here](https://docs.amplify.aws/ui/customization/theming/q/framework/react)

### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#accessing-user-data)Accessing User Data
We can access the user's info now that they are signed in by calling `Auth.currentAuthenticatedUser()` in `useEffect`.
<div class="highlight highlight-source-js"><pre><span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-c1">API</span><span class="pl-kos">,</span> <span class="pl-v">Auth</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'aws-amplify'</span>

<span class="pl-en">useEffect</span><span class="pl-kos">(</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">{</span>
  <span class="pl-en">fetchPosts</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-en">checkUser</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span> <span class="pl-c">// new function call</span>
<span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">;</span>

<span class="pl-k">async</span> <span class="pl-k">function</span> <span class="pl-en">checkUser</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-k">const</span> <span class="pl-s1">user</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-v">Auth</span><span class="pl-kos">.</span><span class="pl-en">currentAuthenticatedUser</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-smi">console</span><span class="pl-kos">.</span><span class="pl-en">log</span><span class="pl-kos">(</span><span class="pl-s">'user: '</span><span class="pl-kos">,</span> <span class="pl-s1">user</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-smi">console</span><span class="pl-kos">.</span><span class="pl-en">log</span><span class="pl-kos">(</span><span class="pl-s">'user attributes: '</span><span class="pl-kos">,</span> <span class="pl-s1">user</span><span class="pl-kos">.</span><span class="pl-c1">attributes</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
<span class="pl-kos">}</span></pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#image-storage-with-amazon-s3)Image Storage with Amazon S3
The last feature we need to have is image storage. To add image storage, we'll use Amazon S3. Amazon S3 can be configured and created via the Amplify CLI:
<div class="highlight highlight-source-shell"><pre>$ amplify add storage

<span class="pl-k">?</span> Please <span class="pl-k">select</span> <span class="pl-smi">from</span> one of the below mentioned services: Content
<span class="pl-k">?</span> Please provide a friendly name <span class="pl-k">for</span> <span class="pl-smi">your resource that will be used to label this category</span> <span class="pl-k">in</span> the project: images
<span class="pl-k">?</span> Please provide bucket name: postagram14148f2f4aeb4f259c847e1e27145a2 <span class="pl-k">&lt;</span>use_default<span class="pl-k">&gt;</span>
<span class="pl-k">?</span> Who should have access: Auth and guest users
<span class="pl-k">?</span> What kind of access <span class="pl-k">do</span> you want <span class="pl-k">for</span> Authenticated users<span class="pl-k">?</span> create, update, read, delete
<span class="pl-k">?</span> What kind of access <span class="pl-k">do</span> you want <span class="pl-k">for</span> Guest users<span class="pl-k">?</span> <span class="pl-c1">read</span>
<span class="pl-k">?</span> Do you want to add a Lambda Trigger <span class="pl-k">for</span> your S3 Bucket<span class="pl-k">?</span> N</pre></div>
To deploy the service, run the following command:
<div class="highlight highlight-source-shell"><pre>$ amplify push</pre></div>
To save items to S3, we use the `Storage` API. The `Storage` API works like this.

1. Saving an item:

<div class="highlight highlight-source-js"><pre><span class="pl-k">const</span> <span class="pl-s1">file</span> <span class="pl-c1">=</span> <span class="pl-s1">e</span><span class="pl-kos">.</span><span class="pl-c1">target</span><span class="pl-kos">.</span><span class="pl-c1">files</span><span class="pl-kos">[</span><span class="pl-c1">0</span><span class="pl-kos">]</span><span class="pl-kos">;</span>
<span class="pl-k">await</span> <span class="pl-v">Storage</span><span class="pl-kos">.</span><span class="pl-en">put</span><span class="pl-kos">(</span><span class="pl-s1">file</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">,</span> <span class="pl-s1">file</span><span class="pl-kos">)</span><span class="pl-kos">;</span></pre></div>

1. Getting an item:

<div class="highlight highlight-source-js"><pre><span class="pl-k">const</span> <span class="pl-s1">image</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-v">Storage</span><span class="pl-kos">.</span><span class="pl-en">get</span><span class="pl-kos">(</span><span class="pl-s">'my-image-key.jpg'</span><span class="pl-kos">)</span></pre></div>
Now we can start saving images to S3 and we can continue building the Photo Sharing App Travel app.
# &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#photo-sharing-app-travel-app)Photo Sharing App Travel App
Now that we have the services we need, let's continue by building out the front end of the app.
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#creating-the-folder-structure-for-our-app)Creating the folder structure for our app
Next, create the following files in the **src** directory:
<div class="highlight highlight-source-shell"><pre>Button.js
CreatePost.js
Header.js
Post.js
Posts.js</pre></div>
Next, we'll go one by one and update these files with our new code.
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#buttonjs)Button.js
Here, we will create a button that we'll be reusing across the app:
<div class="highlight highlight-source-js"><pre><span class="pl-k">import</span> <span class="pl-v">React</span> <span class="pl-k">from</span> <span class="pl-s">'react'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">css</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'emotion'</span><span class="pl-kos">;</span>

<span class="pl-k">export</span> <span class="pl-k">default</span> <span class="pl-k">function</span> <span class="pl-v">Button</span><span class="pl-kos">(</span><span class="pl-kos">{</span>
  <span class="pl-s1">title</span><span class="pl-kos">,</span> <span class="pl-s1">onClick</span><span class="pl-kos">,</span> type <span class="pl-c1">=</span> <span class="pl-s">"action"</span>
<span class="pl-kos">}</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-k">return</span> <span class="pl-kos">(</span>
    <span class="pl-c1">&lt;</span><span class="pl-ent">button</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-en">buttonStyle</span><span class="pl-kos">(</span><span class="pl-s1">type</span><span class="pl-kos">)</span><span class="pl-kos">}</span> <span class="pl-c1">onClick</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">onClick</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>
      <span class="pl-kos">{</span> <span class="pl-s1">title</span> <span class="pl-kos">}</span>
    <span class="pl-c1">&lt;</span>/<span class="pl-ent">button</span><span class="pl-c1">&gt;</span>
  <span class="pl-kos">)</span>
<span class="pl-kos">}</span>

<span class="pl-k">const</span> <span class="pl-en">buttonStyle</span> <span class="pl-c1">=</span> <span class="pl-s1">type</span> <span class="pl-c1">=&gt;</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">background-color</span>:<span class="pl-c1"></span> <span class="pl-s1"><span class="pl-kos">${</span><span class="pl-s1">type</span> <span class="pl-c1">===</span> <span class="pl-s">"action"</span> ? <span class="pl-s">"black"</span> : <span class="pl-s">"red"</span><span class="pl-kos">}</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">height</span>: <span class="pl-c1">40<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">width</span>: <span class="pl-c1">160<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">font-weight</span>: <span class="pl-c1">600</span>;</span>
<span class="pl-s">  <span class="pl-c1">font-size</span>: <span class="pl-c1">16<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">color</span>: white;</span>
<span class="pl-s">  <span class="pl-c1">outline</span>: none;</span>
<span class="pl-s">  <span class="pl-c1">border</span>: none;</span>
<span class="pl-s">  <span class="pl-c1">margin-top</span>: <span class="pl-c1">5<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">cursor</span>: pointer;</span>
<span class="pl-s">  :<span class="pl-c1">hover</span> {</span>
<span class="pl-s">    <span class="pl-c1">background-color</span>: <span class="pl-pds"><span class="pl-kos">#</span>363636</span>;</span>
<span class="pl-s">  }</span>
<span class="pl-s">`</span></pre></div>
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#headerjs)Header.js
Add the following code in **Header.js**
<div class="highlight highlight-source-js"><pre><span class="pl-k">import</span> <span class="pl-v">React</span> <span class="pl-k">from</span> <span class="pl-s">'react'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">css</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'emotion'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-v">Link</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'react-router-dom'</span><span class="pl-kos">;</span>

<span class="pl-k">export</span> <span class="pl-k">default</span> <span class="pl-k">function</span> <span class="pl-v">Header</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-k">return</span> <span class="pl-kos">(</span>
    <span class="pl-c1">&lt;</span><span class="pl-ent">div</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">headerContainer</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">h1</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">headerStyle</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>Postagram<span class="pl-c1">&lt;</span>/<span class="pl-ent">h1</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">Link</span> <span class="pl-c1">to</span><span class="pl-c1">=</span><span class="pl-s">"/"</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">linkStyle</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>All Posts<span class="pl-c1">&lt;</span>/<span class="pl-ent">Link</span><span class="pl-c1">&gt;</span>
    <span class="pl-c1">&lt;</span>/<span class="pl-ent">div</span><span class="pl-c1">&gt;</span>
  <span class="pl-kos">)</span>
<span class="pl-kos">}</span>

<span class="pl-k">const</span> <span class="pl-s1">headerContainer</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">padding-top</span>: <span class="pl-c1">20<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">headerStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">font-size</span>: <span class="pl-c1">40<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">margin-top</span>: <span class="pl-c1">0<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">linkStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">color</span>: black;</span>
<span class="pl-s">  <span class="pl-c1">font-weight</span>: bold;</span>
<span class="pl-s">  <span class="pl-c1">text-decoration</span>: none;</span>
<span class="pl-s">  <span class="pl-c1">margin-right</span>: <span class="pl-c1">10<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  :<span class="pl-c1">hover</span> {</span>
<span class="pl-s">    <span class="pl-c1">color</span>: <span class="pl-pds"><span class="pl-kos">#</span>058aff</span>;</span>
<span class="pl-s">  }</span>
<span class="pl-s">`</span></pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#postsjs)Posts.js
The next thing we'll do is create the **Posts** component to render a list of posts.
This will go in the main view of the app. The only data from the post that will be rendered in this view is the post name and post image.
The `posts` array will be passed in as a prop to the **Posts** component.
<div class="highlight highlight-source-js"><pre><span class="pl-k">import</span> <span class="pl-v">React</span> <span class="pl-k">from</span> <span class="pl-s">'react'</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">css</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'emotion'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-v">Link</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'react-router-dom'</span><span class="pl-kos">;</span>

<span class="pl-k">export</span> <span class="pl-k">default</span> <span class="pl-k">function</span> <span class="pl-v">Posts</span><span class="pl-kos">(</span><span class="pl-kos">{</span>
  posts <span class="pl-c1">=</span> <span class="pl-kos">[</span><span class="pl-kos">]</span>
<span class="pl-kos">}</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-k">return</span> <span class="pl-kos">(</span>
    <span class="pl-c1">&lt;</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">h1</span><span class="pl-c1">&gt;</span>Posts<span class="pl-c1">&lt;</span>/<span class="pl-ent">h1</span><span class="pl-c1">&gt;</span>
      <span class="pl-kos">{</span>
        <span class="pl-s1">posts</span><span class="pl-kos">.</span><span class="pl-en">map</span><span class="pl-kos">(</span><span class="pl-s1">post</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">(</span>
          <span class="pl-c1">&lt;</span><span class="pl-ent">Link</span> <span class="pl-c1">to</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s">`/post/<span class="pl-s1"><span class="pl-kos">${</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">id</span><span class="pl-kos">}</span></span>`</span><span class="pl-kos">}</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">linkStyle</span><span class="pl-kos">}</span> <span class="pl-c1">key</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">id</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>
            <span class="pl-c1">&lt;</span><span class="pl-ent">div</span> <span class="pl-c1">key</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">id</span><span class="pl-kos">}</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">postContainer</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>
              <span class="pl-c1">&lt;</span><span class="pl-ent">h1</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">postTitleStyle</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">}</span><span class="pl-c1">&lt;</span>/<span class="pl-ent">h1</span><span class="pl-c1">&gt;</span>
              <span class="pl-c1">&lt;</span><span class="pl-ent">img</span> <span class="pl-c1">alt</span><span class="pl-c1">=</span><span class="pl-s">"post"</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">imageStyle</span><span class="pl-kos">}</span> <span class="pl-c1">src</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">}</span> /<span class="pl-c1">&gt;</span>
            <span class="pl-c1">&lt;</span>/<span class="pl-ent">div</span><span class="pl-c1">&gt;</span>
          <span class="pl-c1">&lt;</span>/<span class="pl-ent">Link</span><span class="pl-c1">&gt;</span>
        <span class="pl-kos">)</span><span class="pl-kos">)</span>
      <span class="pl-kos">}</span>
    <span class="pl-c1">&lt;</span>/<span class="pl-c1">&gt;</span>
  <span class="pl-kos">)</span>
<span class="pl-kos">}</span>

<span class="pl-k">const</span> <span class="pl-s1">postTitleStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">margin</span>: <span class="pl-c1">15<span class="pl-smi">px</span></span> <span class="pl-c1">0<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">color</span>: <span class="pl-pds"><span class="pl-kos">#</span>0070f3</span>;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">linkStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">text-decoration</span>: none;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">postContainer</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">border-radius</span>: <span class="pl-c1">10<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">padding</span>: <span class="pl-c1">1<span class="pl-smi">px</span></span> <span class="pl-c1">20<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">border</span>: <span class="pl-c1">1<span class="pl-smi">px</span></span> solid <span class="pl-pds"><span class="pl-kos">#</span>ddd</span>;</span>
<span class="pl-s">  <span class="pl-c1">margin-bottom</span>: <span class="pl-c1">20<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  :<span class="pl-c1">hover</span> {</span>
<span class="pl-s">    <span class="pl-c1">border-color</span>: <span class="pl-pds"><span class="pl-kos">#</span>0070f3</span>;</span>
<span class="pl-s">  }</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">imageStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">width</span>: <span class="pl-c1">100<span class="pl-smi">%</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">max-width</span>: <span class="pl-c1">400<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">`</span></pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#createpostjs)CreatePost.js
The next component we'll create is `CreatePost`. This component is a form that will be displayed to the user as an `overlay` or a `modal`. In it, the user will be able to toggle the overlay to show and hide it, and also be able to create a new post.
The props this component will receive are the following:

1. 
    `updateOverlayVisibility` - This function will toggle the overlay to show / hide it
1. 
    `updatePosts` - This function will allow us to update the main posts array
1. 
    `posts` - The posts coming back from our API

This component has a lot going on, so before we dive into the code let's walk through what is happening:

1. We create some initial state using the `useState` hook. This state is created using the `initialState` object.
1. The `onChangeText` handler sets the name, description, and location fields of the post
1. The `onChangeImage` handler allows the user to upload an image and saves it to state. It also creates a unique image name.
1. The `save` function does the following:


- First checks to make sure that all of the form fields are populated
- Next it updates the `saving` state to true to show a saving indicator
- We then create a unique ID for the post using the `uuid` library
- Using the form state and the `uuid`, we create a post object that will be sent to the API.
- Next, we upload the image to S3 using `Storage.put`, passing in the image name and the file
- Once the image upload is successful, we create the `post` in our GraphQL API
- Finally, we update the local state, close the popup, and update the local `posts` array with the new post

<div class="highlight highlight-source-js"><pre><span class="pl-k">import</span> <span class="pl-v">React</span><span class="pl-kos">,</span> <span class="pl-kos">{</span> <span class="pl-s1">useState</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'react'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">css</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'emotion'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-v">Button</span> <span class="pl-k">from</span> <span class="pl-s">'./Button'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">v4</span> <span class="pl-k">as</span> <span class="pl-s1">uuid</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'uuid'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-v">Storage</span><span class="pl-kos">,</span> <span class="pl-c1">API</span><span class="pl-kos">,</span> <span class="pl-v">Auth</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'aws-amplify'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">createPost</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'./graphql/mutations'</span><span class="pl-kos">;</span>

<span class="pl-c">/* Initial state to hold form input, saving state */</span>
<span class="pl-k">const</span> <span class="pl-s1">initialState</span> <span class="pl-c1">=</span> <span class="pl-kos">{</span>
  <span class="pl-c1">name</span>: <span class="pl-s">''</span><span class="pl-kos">,</span>
  <span class="pl-c1">description</span>: <span class="pl-s">''</span><span class="pl-kos">,</span>
  <span class="pl-c1">image</span>: <span class="pl-kos">{</span><span class="pl-kos">}</span><span class="pl-kos">,</span>
  <span class="pl-c1">file</span>: <span class="pl-s">''</span><span class="pl-kos">,</span>
  <span class="pl-c1">location</span>: <span class="pl-s">''</span><span class="pl-kos">,</span>
  <span class="pl-c1">saving</span>: <span class="pl-c1">false</span>
<span class="pl-kos">}</span><span class="pl-kos">;</span>

<span class="pl-k">export</span> <span class="pl-k">default</span> <span class="pl-k">function</span> <span class="pl-v">CreatePost</span><span class="pl-kos">(</span><span class="pl-kos">{</span>
  <span class="pl-s1">updateOverlayVisibility</span><span class="pl-kos">,</span> <span class="pl-s1">updatePosts</span><span class="pl-kos">,</span> <span class="pl-s1">posts</span>
<span class="pl-kos">}</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-c">/* 1. Create local state with useState hook */</span>
  <span class="pl-k">const</span> <span class="pl-kos">[</span><span class="pl-s1">formState</span><span class="pl-kos">,</span> <span class="pl-s1">updateFormState</span><span class="pl-kos">]</span> <span class="pl-c1">=</span> <span class="pl-en">useState</span><span class="pl-kos">(</span><span class="pl-s1">initialState</span><span class="pl-kos">)</span>

  <span class="pl-c">/* 2. onChangeText handler updates the form state when a user types into a form field */</span>
  <span class="pl-k">function</span> <span class="pl-en">onChangeText</span><span class="pl-kos">(</span><span class="pl-s1">e</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
    <span class="pl-s1">e</span><span class="pl-kos">.</span><span class="pl-en">persist</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-en">updateFormState</span><span class="pl-kos">(</span><span class="pl-s1">currentState</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">(</span><span class="pl-kos">{</span> ...<span class="pl-s1">currentState</span><span class="pl-kos">,</span> <span class="pl-kos">[</span><span class="pl-s1">e</span><span class="pl-kos">.</span><span class="pl-c1">target</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">]</span>: <span class="pl-s1">e</span><span class="pl-kos">.</span><span class="pl-c1">target</span><span class="pl-kos">.</span><span class="pl-c1">value</span> <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-kos">}</span>

  <span class="pl-c">/* 3. onChangeFile handler will be fired when a user uploads a file  */</span>
  <span class="pl-k">function</span> <span class="pl-en">onChangeFile</span><span class="pl-kos">(</span><span class="pl-s1">e</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
    <span class="pl-s1">e</span><span class="pl-kos">.</span><span class="pl-en">persist</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-k">if</span> <span class="pl-kos">(</span>! <span class="pl-s1">e</span><span class="pl-kos">.</span><span class="pl-c1">target</span><span class="pl-kos">.</span><span class="pl-c1">files</span><span class="pl-kos">[</span><span class="pl-c1">0</span><span class="pl-kos">]</span><span class="pl-kos">)</span> <span class="pl-k">return</span><span class="pl-kos">;</span>
    <span class="pl-k">const</span> <span class="pl-s1">image</span> <span class="pl-c1">=</span> <span class="pl-kos">{</span> <span class="pl-c1">fileInfo</span>: <span class="pl-s1">e</span><span class="pl-kos">.</span><span class="pl-c1">target</span><span class="pl-kos">.</span><span class="pl-c1">files</span><span class="pl-kos">[</span><span class="pl-c1">0</span><span class="pl-kos">]</span><span class="pl-kos">,</span> <span class="pl-c1">name</span>: <span class="pl-s">`<span class="pl-s1"><span class="pl-kos">${</span><span class="pl-s1">e</span><span class="pl-kos">.</span><span class="pl-c1">target</span><span class="pl-kos">.</span><span class="pl-c1">files</span><span class="pl-kos">[</span><span class="pl-c1">0</span><span class="pl-kos">]</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">}</span></span>_<span class="pl-s1"><span class="pl-kos">${</span><span class="pl-en">uuid</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">}</span></span>`</span><span class="pl-kos">}</span>
    <span class="pl-en">updateFormState</span><span class="pl-kos">(</span><span class="pl-s1">currentState</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">(</span><span class="pl-kos">{</span> ...<span class="pl-s1">currentState</span><span class="pl-kos">,</span> <span class="pl-c1">file</span>: <span class="pl-c1">URL</span><span class="pl-kos">.</span><span class="pl-en">createObjectURL</span><span class="pl-kos">(</span><span class="pl-s1">e</span><span class="pl-kos">.</span><span class="pl-c1">target</span><span class="pl-kos">.</span><span class="pl-c1">files</span><span class="pl-kos">[</span><span class="pl-c1">0</span><span class="pl-kos">]</span><span class="pl-kos">)</span><span class="pl-kos">,</span> image <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">)</span>
  <span class="pl-kos">}</span>

  <span class="pl-c">/* 4. Save the post  */</span>
  <span class="pl-k">async</span> <span class="pl-k">function</span> <span class="pl-en">save</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
    <span class="pl-k">try</span> <span class="pl-kos">{</span>
      <span class="pl-k">const</span> <span class="pl-kos">{</span> name<span class="pl-kos">,</span> description<span class="pl-kos">,</span> location<span class="pl-kos">,</span> image <span class="pl-kos">}</span> <span class="pl-c1">=</span> <span class="pl-s1">formState</span><span class="pl-kos">;</span>
      <span class="pl-k">if</span> <span class="pl-kos">(</span>!<span class="pl-s1">name</span> <span class="pl-c1">||</span> !<span class="pl-s1">description</span> <span class="pl-c1">||</span> !<span class="pl-s1">location</span> <span class="pl-c1">||</span> !<span class="pl-s1">image</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">)</span> <span class="pl-k">return</span><span class="pl-kos">;</span>
      <span class="pl-en">updateFormState</span><span class="pl-kos">(</span><span class="pl-s1">currentState</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">(</span><span class="pl-kos">{</span> ...<span class="pl-s1">currentState</span><span class="pl-kos">,</span> <span class="pl-c1">saving</span>: <span class="pl-c1">true</span> <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-k">const</span> <span class="pl-s1">postId</span> <span class="pl-c1">=</span> <span class="pl-en">uuid</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-k">const</span> <span class="pl-s1">postInfo</span> <span class="pl-c1">=</span> <span class="pl-kos">{</span> name<span class="pl-kos">,</span> description<span class="pl-kos">,</span> location<span class="pl-kos">,</span> <span class="pl-c1">image</span>: <span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">,</span> <span class="pl-c1">id</span>: <span class="pl-s1">postId</span> <span class="pl-kos">}</span><span class="pl-kos">;</span>

      <span class="pl-k">await</span> <span class="pl-v">Storage</span><span class="pl-kos">.</span><span class="pl-en">put</span><span class="pl-kos">(</span><span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">,</span> <span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">.</span><span class="pl-c1">fileInfo</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-k">await</span> <span class="pl-c1">API</span><span class="pl-kos">.</span><span class="pl-en">graphql</span><span class="pl-kos">(</span><span class="pl-kos">{</span>
        <span class="pl-c1">query</span>: <span class="pl-s1">createPost</span><span class="pl-kos">,</span> <span class="pl-c1">variables</span>: <span class="pl-kos">{</span> <span class="pl-c1">input</span>: <span class="pl-s1">postInfo</span> <span class="pl-kos">}</span>
      <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-s1">updatePosts</span><span class="pl-kos">(</span><span class="pl-kos">[</span>...<span class="pl-s1">posts</span><span class="pl-kos">,</span> <span class="pl-kos">{</span> ...<span class="pl-s1">postInfo</span><span class="pl-kos">,</span> <span class="pl-c1">image</span>: <span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">file</span> <span class="pl-kos">}</span><span class="pl-kos">]</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-en">updateFormState</span><span class="pl-kos">(</span><span class="pl-s1">currentState</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">(</span><span class="pl-kos">{</span> ...<span class="pl-s1">currentState</span><span class="pl-kos">,</span> <span class="pl-c1">saving</span>: <span class="pl-c1">false</span> <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-s1">updateOverlayVisibility</span><span class="pl-kos">(</span><span class="pl-c1">false</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-kos">}</span> <span class="pl-k">catch</span> <span class="pl-kos">(</span><span class="pl-s1">err</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
      <span class="pl-smi">console</span><span class="pl-kos">.</span><span class="pl-en">log</span><span class="pl-kos">(</span><span class="pl-s">'error: '</span><span class="pl-kos">,</span> <span class="pl-s1">err</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-kos">}</span>
  <span class="pl-kos">}</span>

  <span class="pl-k">return</span> <span class="pl-kos">(</span>
    <span class="pl-c1">&lt;</span><span class="pl-ent">div</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">containerStyle</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">input</span>
        <span class="pl-c1">placeholder</span><span class="pl-c1">=</span><span class="pl-s">"Post name"</span>
        <span class="pl-c1">name</span><span class="pl-c1">=</span><span class="pl-s">"name"</span>
        <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">inputStyle</span><span class="pl-kos">}</span>
        <span class="pl-c1">onChange</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">onChangeText</span><span class="pl-kos">}</span>
      /<span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">input</span>
        <span class="pl-c1">placeholder</span><span class="pl-c1">=</span><span class="pl-s">"Location"</span>
        <span class="pl-c1">name</span><span class="pl-c1">=</span><span class="pl-s">"location"</span>
        <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">inputStyle</span><span class="pl-kos">}</span>
        <span class="pl-c1">onChange</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">onChangeText</span><span class="pl-kos">}</span>
      /<span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">input</span>
        <span class="pl-c1">placeholder</span><span class="pl-c1">=</span><span class="pl-s">"Description"</span>
        <span class="pl-c1">name</span><span class="pl-c1">=</span><span class="pl-s">"description"</span>
        <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">inputStyle</span><span class="pl-kos">}</span>
        <span class="pl-c1">onChange</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">onChangeText</span><span class="pl-kos">}</span>
      /<span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">input</span> 
        <span class="pl-c1">type</span><span class="pl-c1">=</span><span class="pl-s">"file"</span>
        <span class="pl-c1">onChange</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">onChangeFile</span><span class="pl-kos">}</span>
      /<span class="pl-c1">&gt;</span>
      <span class="pl-kos">{</span> <span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">file</span> <span class="pl-c1">&amp;&amp;</span> <span class="pl-c1">&lt;</span><span class="pl-ent">img</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">imageStyle</span><span class="pl-kos">}</span> <span class="pl-c1">alt</span><span class="pl-c1">=</span><span class="pl-s">"preview"</span> <span class="pl-c1">src</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">file</span><span class="pl-kos">}</span> /<span class="pl-c1">&gt;</span> <span class="pl-kos">}</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">Button</span> <span class="pl-c1">title</span><span class="pl-c1">=</span><span class="pl-s">"Create New Post"</span> <span class="pl-c1">onClick</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">save</span><span class="pl-kos">}</span> /<span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">Button</span> <span class="pl-c1">type</span><span class="pl-c1">=</span><span class="pl-s">"cancel"</span> <span class="pl-c1">title</span><span class="pl-c1">=</span><span class="pl-s">"Cancel"</span> <span class="pl-c1">onClick</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-c1">=&gt;</span> <span class="pl-s1">updateOverlayVisibility</span><span class="pl-kos">(</span><span class="pl-c1">false</span><span class="pl-kos">)</span><span class="pl-kos">}</span> /<span class="pl-c1">&gt;</span>
      <span class="pl-kos">{</span> <span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">saving</span> <span class="pl-c1">&amp;&amp;</span> <span class="pl-c1">&lt;</span><span class="pl-ent">p</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">savingMessageStyle</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>Saving post...<span class="pl-c1">&lt;</span>/<span class="pl-ent">p</span><span class="pl-c1">&gt;</span> <span class="pl-kos">}</span>
    <span class="pl-c1">&lt;</span>/<span class="pl-ent">div</span><span class="pl-c1">&gt;</span>
  <span class="pl-kos">)</span>
<span class="pl-kos">}</span>

<span class="pl-k">const</span> <span class="pl-s1">inputStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">margin-bottom</span>: <span class="pl-c1">10<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">outline</span>: none;</span>
<span class="pl-s">  <span class="pl-c1">padding</span>: <span class="pl-c1">7<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">border</span>: <span class="pl-c1">1<span class="pl-smi">px</span></span> solid <span class="pl-pds"><span class="pl-kos">#</span>ddd</span>;</span>
<span class="pl-s">  <span class="pl-c1">font-size</span>: <span class="pl-c1">16<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">border-radius</span>: <span class="pl-c1">4<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">imageStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">height</span>: <span class="pl-c1">120<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">margin</span>: <span class="pl-c1">10<span class="pl-smi">px</span></span> <span class="pl-c1">0<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">object-fit</span>: contain;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">containerStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">display</span>: flex;</span>
<span class="pl-s">  <span class="pl-c1">flex-direction</span>: column;</span>
<span class="pl-s">  <span class="pl-c1">width</span>: <span class="pl-c1">400<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">height</span>: <span class="pl-c1">420<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">position</span>: fixed;</span>
<span class="pl-s">  <span class="pl-c1">left</span>: <span class="pl-c1">0</span>;</span>
<span class="pl-s">  <span class="pl-c1">border-radius</span>: <span class="pl-c1">4<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">top</span>: <span class="pl-c1">0</span>;</span>
<span class="pl-s">  <span class="pl-c1">margin-left</span>: <span class="pl-en">calc</span>(<span class="pl-c1">50<span class="pl-smi">vw</span></span> <span class="pl-c1">-</span> <span class="pl-c1">220<span class="pl-smi">px</span></span>);</span>
<span class="pl-s">  <span class="pl-c1">margin-top</span>: <span class="pl-en">calc</span>(<span class="pl-c1">50<span class="pl-smi">vh</span></span> <span class="pl-c1">-</span> <span class="pl-c1">230<span class="pl-smi">px</span></span>);</span>
<span class="pl-s">  <span class="pl-c1">background-color</span>: white;</span>
<span class="pl-s">  <span class="pl-c1">border</span>: <span class="pl-c1">1<span class="pl-smi">px</span></span> solid <span class="pl-pds"><span class="pl-kos">#</span>ddd</span>;</span>
<span class="pl-s">  <span class="pl-c1">box-shadow</span>: <span class="pl-en">rgba</span>(<span class="pl-c1">0</span><span class="pl-kos">,</span> <span class="pl-c1">0</span><span class="pl-kos">,</span> <span class="pl-c1">0</span><span class="pl-kos">,</span> <span class="pl-c1">0.25</span>) <span class="pl-c1">0<span class="pl-smi">px</span></span> <span class="pl-c1">0.125<span class="pl-smi">rem</span></span> <span class="pl-c1">0.25<span class="pl-smi">rem</span></span>;</span>
<span class="pl-s">  <span class="pl-c1">padding</span>: <span class="pl-c1">20<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">savingMessageStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">margin-bottom</span>: <span class="pl-c1">0<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">`</span></pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#postjs)Post.js
The next component that we'll build is the Post component.
In this component, we will be reading the post `id` from the router parameters. We'll then use this post `id` to make an API call to the GraphQL API to fetch the post details.
Another thing to look at is how we deal with images.
When storing an image in S3, we
<div class="highlight highlight-source-js"><pre><span class="pl-k">import</span> <span class="pl-v">React</span><span class="pl-kos">,</span> <span class="pl-kos">{</span> <span class="pl-s1">useState</span><span class="pl-kos">,</span> <span class="pl-s1">useEffect</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'react'</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">css</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'emotion'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">useParams</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'react-router-dom'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-c1">API</span><span class="pl-kos">,</span> <span class="pl-v">Storage</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'aws-amplify'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">getPost</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'./graphql/queries'</span><span class="pl-kos">;</span>

<span class="pl-k">export</span> <span class="pl-k">default</span> <span class="pl-k">function</span> <span class="pl-v">Post</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-k">const</span> <span class="pl-kos">[</span><span class="pl-s1">loading</span><span class="pl-kos">,</span> <span class="pl-s1">updateLoading</span><span class="pl-kos">]</span> <span class="pl-c1">=</span> <span class="pl-en">useState</span><span class="pl-kos">(</span><span class="pl-c1">true</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-k">const</span> <span class="pl-kos">[</span><span class="pl-s1">post</span><span class="pl-kos">,</span> <span class="pl-s1">updatePost</span><span class="pl-kos">]</span> <span class="pl-c1">=</span> <span class="pl-en">useState</span><span class="pl-kos">(</span><span class="pl-c1">null</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-k">const</span> <span class="pl-kos">{</span> id <span class="pl-kos">}</span> <span class="pl-c1">=</span> <span class="pl-en">useParams</span><span class="pl-kos">(</span><span class="pl-kos">)</span>
  <span class="pl-en">useEffect</span><span class="pl-kos">(</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">{</span>
    <span class="pl-en">fetchPost</span><span class="pl-kos">(</span><span class="pl-kos">)</span>
  <span class="pl-kos">}</span><span class="pl-kos">,</span> <span class="pl-kos">[</span><span class="pl-kos">]</span><span class="pl-kos">)</span>
  <span class="pl-k">async</span> <span class="pl-k">function</span> <span class="pl-en">fetchPost</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
    <span class="pl-k">try</span> <span class="pl-kos">{</span>
      <span class="pl-k">const</span> <span class="pl-s1">postData</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-c1">API</span><span class="pl-kos">.</span><span class="pl-en">graphql</span><span class="pl-kos">(</span><span class="pl-kos">{</span>
        <span class="pl-c1">query</span>: <span class="pl-s1">getPost</span><span class="pl-kos">,</span> <span class="pl-c1">variables</span>: <span class="pl-kos">{</span> id <span class="pl-kos">}</span>
      <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-k">const</span> <span class="pl-s1">currentPost</span> <span class="pl-c1">=</span> <span class="pl-s1">postData</span><span class="pl-kos">.</span><span class="pl-c1">data</span><span class="pl-kos">.</span><span class="pl-c1">getPost</span>
      <span class="pl-k">const</span> <span class="pl-s1">image</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-v">Storage</span><span class="pl-kos">.</span><span class="pl-en">get</span><span class="pl-kos">(</span><span class="pl-s1">currentPost</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">)</span><span class="pl-kos">;</span>

      <span class="pl-s1">currentPost</span><span class="pl-kos">.</span><span class="pl-c1">image</span> <span class="pl-c1">=</span> <span class="pl-s1">image</span><span class="pl-kos">;</span>
      <span class="pl-en">updatePost</span><span class="pl-kos">(</span><span class="pl-s1">currentPost</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-en">updateLoading</span><span class="pl-kos">(</span><span class="pl-c1">false</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-kos">}</span> <span class="pl-k">catch</span> <span class="pl-kos">(</span><span class="pl-s1">err</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
      <span class="pl-smi">console</span><span class="pl-kos">.</span><span class="pl-en">log</span><span class="pl-kos">(</span><span class="pl-s">'error: '</span><span class="pl-kos">,</span> <span class="pl-s1">err</span><span class="pl-kos">)</span>
    <span class="pl-kos">}</span>
  <span class="pl-kos">}</span>
  <span class="pl-k">if</span> <span class="pl-kos">(</span><span class="pl-s1">loading</span><span class="pl-kos">)</span> <span class="pl-k">return</span> <span class="pl-c1">&lt;</span><span class="pl-ent">h3</span><span class="pl-c1">&gt;</span>Loading...<span class="pl-c1">&lt;</span>/<span class="pl-ent">h3</span><span class="pl-c1">&gt;</span>
  <span class="pl-smi">console</span><span class="pl-kos">.</span><span class="pl-en">log</span><span class="pl-kos">(</span><span class="pl-s">'post: '</span><span class="pl-kos">,</span> <span class="pl-s1">post</span><span class="pl-kos">)</span>
  <span class="pl-k">return</span> <span class="pl-kos">(</span>
    <span class="pl-c1">&lt;</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">h1</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">titleStyle</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">}</span><span class="pl-c1">&lt;</span>/<span class="pl-ent">h1</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">h3</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">locationStyle</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">location</span><span class="pl-kos">}</span><span class="pl-c1">&lt;</span>/<span class="pl-ent">h3</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">p</span><span class="pl-c1">&gt;</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">description</span><span class="pl-kos">}</span><span class="pl-c1">&lt;</span>/<span class="pl-ent">p</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">img</span> <span class="pl-c1">alt</span><span class="pl-c1">=</span><span class="pl-s">"post"</span> <span class="pl-c1">src</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">}</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">imageStyle</span><span class="pl-kos">}</span> /<span class="pl-c1">&gt;</span>
    <span class="pl-c1">&lt;</span>/<span class="pl-c1">&gt;</span>
  <span class="pl-kos">)</span>
<span class="pl-kos">}</span>

<span class="pl-k">const</span> <span class="pl-s1">titleStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">margin-bottom</span>: <span class="pl-c1">7<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">locationStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">color</span>: <span class="pl-pds"><span class="pl-kos">#</span>0070f3</span>;</span>
<span class="pl-s">  <span class="pl-c1">margin</span>: <span class="pl-c1">0</span>;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">imageStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">max-width</span>: <span class="pl-c1">500<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">  <span class="pl-k">@media</span> (<span class="pl-c1">max-width</span>: <span class="pl-c1">500<span class="pl-smi">px</span></span>) {</span>
<span class="pl-s">    <span class="pl-c1">width</span>: <span class="pl-c1">100<span class="pl-smi">%</span></span>;</span>
<span class="pl-s">  }</span>
<span class="pl-s">`</span></pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#router---appjs)Router - App.js
Next, create the router in App.js. Our app will have two main routes:

1. A home route - `/`. This route will render a list of posts from our API
1. A post details route - `/post/:id`. This route will render a single post and details about that post.

Using React Router, we can read the Post ID from the route and fetch the post associated with it. This is a common pattern in many apps as it makes the link shareable.
Another way to do this would be to have some global state management set up and setting the post ID in the global state. The main drawback of this approach is that the URL cannot be shared.
Other than routing, the main functionality happening in this component is an `API` call to fetch posts from our API.
<div class="highlight highlight-source-js"><pre><span class="pl-k">import</span> <span class="pl-v">React</span><span class="pl-kos">,</span> <span class="pl-kos">{</span> <span class="pl-s1">useState</span><span class="pl-kos">,</span> <span class="pl-s1">useEffect</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">"react"</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span>
  <span class="pl-v">HashRouter</span><span class="pl-kos">,</span>
  <span class="pl-v">Switch</span><span class="pl-kos">,</span>
  <span class="pl-v">Route</span>
<span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">"react-router-dom"</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">withAuthenticator</span><span class="pl-kos">,</span> <span class="pl-v">AmplifySignOut</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'@aws-amplify/ui-react'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">css</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'emotion'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-c1">API</span><span class="pl-kos">,</span> <span class="pl-v">Storage</span><span class="pl-kos">,</span> <span class="pl-v">Auth</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'aws-amplify'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-kos">{</span> <span class="pl-s1">listPosts</span> <span class="pl-kos">}</span> <span class="pl-k">from</span> <span class="pl-s">'./graphql/queries'</span><span class="pl-kos">;</span>

<span class="pl-k">import</span> <span class="pl-v">Posts</span> <span class="pl-k">from</span> <span class="pl-s">'./Posts'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-v">Post</span> <span class="pl-k">from</span> <span class="pl-s">'./Post'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-v">Header</span> <span class="pl-k">from</span> <span class="pl-s">'./Header'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-v">CreatePost</span> <span class="pl-k">from</span> <span class="pl-s">'./CreatePost'</span><span class="pl-kos">;</span>
<span class="pl-k">import</span> <span class="pl-v">Button</span> <span class="pl-k">from</span> <span class="pl-s">'./Button'</span><span class="pl-kos">;</span>

<span class="pl-k">function</span> <span class="pl-v">Router</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-c">/* create a couple of pieces of initial state */</span>
  <span class="pl-k">const</span> <span class="pl-kos">[</span><span class="pl-s1">showOverlay</span><span class="pl-kos">,</span> <span class="pl-s1">updateOverlayVisibility</span><span class="pl-kos">]</span> <span class="pl-c1">=</span> <span class="pl-en">useState</span><span class="pl-kos">(</span><span class="pl-c1">false</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-k">const</span> <span class="pl-kos">[</span><span class="pl-s1">posts</span><span class="pl-kos">,</span> <span class="pl-s1">updatePosts</span><span class="pl-kos">]</span> <span class="pl-c1">=</span> <span class="pl-en">useState</span><span class="pl-kos">(</span><span class="pl-kos">[</span><span class="pl-kos">]</span><span class="pl-kos">)</span><span class="pl-kos">;</span>

  <span class="pl-c">/* fetch posts when component loads */</span>
  <span class="pl-en">useEffect</span><span class="pl-kos">(</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">{</span>
      <span class="pl-en">fetchPosts</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-kos">}</span><span class="pl-kos">,</span> <span class="pl-kos">[</span><span class="pl-kos">]</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-k">async</span> <span class="pl-k">function</span> <span class="pl-en">fetchPosts</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
    <span class="pl-c">/* query the API, ask for 100 items */</span>
    <span class="pl-k">let</span> <span class="pl-s1">postData</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-c1">API</span><span class="pl-kos">.</span><span class="pl-en">graphql</span><span class="pl-kos">(</span><span class="pl-kos">{</span> <span class="pl-c1">query</span>: <span class="pl-s1">listPosts</span><span class="pl-kos">,</span> <span class="pl-c1">variables</span>: <span class="pl-kos">{</span> <span class="pl-c1">limit</span>: <span class="pl-c1">100</span> <span class="pl-kos">}</span><span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-k">let</span> <span class="pl-s1">postsArray</span> <span class="pl-c1">=</span> <span class="pl-s1">postData</span><span class="pl-kos">.</span><span class="pl-c1">data</span><span class="pl-kos">.</span><span class="pl-c1">listPosts</span><span class="pl-kos">.</span><span class="pl-c1">items</span><span class="pl-kos">;</span>
    <span class="pl-c">/* map over the image keys in the posts array, get signed image URLs for each image */</span>
    <span class="pl-s1">postsArray</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-v">Promise</span><span class="pl-kos">.</span><span class="pl-en">all</span><span class="pl-kos">(</span><span class="pl-s1">postsArray</span><span class="pl-kos">.</span><span class="pl-en">map</span><span class="pl-kos">(</span><span class="pl-k">async</span> <span class="pl-s1">post</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">{</span>
      <span class="pl-k">const</span> <span class="pl-s1">imageKey</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-v">Storage</span><span class="pl-kos">.</span><span class="pl-en">get</span><span class="pl-kos">(</span><span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
      <span class="pl-s1">post</span><span class="pl-kos">.</span><span class="pl-c1">image</span> <span class="pl-c1">=</span> <span class="pl-s1">imageKey</span><span class="pl-kos">;</span>
      <span class="pl-k">return</span> <span class="pl-s1">post</span><span class="pl-kos">;</span>
    <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-c">/* update the posts array in the local state */</span>
    <span class="pl-en">setPostState</span><span class="pl-kos">(</span><span class="pl-s1">postsArray</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-kos">}</span>
  <span class="pl-k">async</span> <span class="pl-k">function</span> <span class="pl-en">setPostState</span><span class="pl-kos">(</span><span class="pl-s1">postsArray</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
    <span class="pl-en">updatePosts</span><span class="pl-kos">(</span><span class="pl-s1">postsArray</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-kos">}</span>
  <span class="pl-k">return</span> <span class="pl-kos">(</span>
    <span class="pl-c1">&lt;</span><span class="pl-c1">&gt;</span>
      <span class="pl-c1">&lt;</span><span class="pl-ent">HashRouter</span><span class="pl-c1">&gt;</span>
          <span class="pl-c1">&lt;</span><span class="pl-ent">div</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">contentStyle</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>
            <span class="pl-c1">&lt;</span><span class="pl-ent">Header</span> /<span class="pl-c1">&gt;</span>
            <span class="pl-c1">&lt;</span><span class="pl-ent">hr</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">dividerStyle</span><span class="pl-kos">}</span> /<span class="pl-c1">&gt;</span>
            <span class="pl-c1">&lt;</span><span class="pl-ent">Button</span> <span class="pl-c1">title</span><span class="pl-c1">=</span><span class="pl-s">"New Post"</span> <span class="pl-c1">onClick</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-c1">=&gt;</span> <span class="pl-en">updateOverlayVisibility</span><span class="pl-kos">(</span><span class="pl-c1">true</span><span class="pl-kos">)</span><span class="pl-kos">}</span> /<span class="pl-c1">&gt;</span>
            <span class="pl-c1">&lt;</span><span class="pl-ent">Switch</span><span class="pl-c1">&gt;</span>
              <span class="pl-c1">&lt;</span><span class="pl-ent">Route</span> <span class="pl-c1">exact</span> <span class="pl-c1">path</span><span class="pl-c1">=</span><span class="pl-s">"/"</span> <span class="pl-c1">&gt;</span>
                <span class="pl-c1">&lt;</span><span class="pl-ent">Posts</span> <span class="pl-c1">posts</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">posts</span><span class="pl-kos">}</span> /<span class="pl-c1">&gt;</span>
              <span class="pl-c1">&lt;</span>/<span class="pl-ent">Route</span><span class="pl-c1">&gt;</span>
              <span class="pl-c1">&lt;</span><span class="pl-ent">Route</span> <span class="pl-c1">path</span><span class="pl-c1">=</span><span class="pl-s">"/post/:id"</span> <span class="pl-c1">&gt;</span>
                <span class="pl-c1">&lt;</span><span class="pl-ent">Post</span> /<span class="pl-c1">&gt;</span>
              <span class="pl-c1">&lt;</span>/<span class="pl-ent">Route</span><span class="pl-c1">&gt;</span>
            <span class="pl-c1">&lt;</span>/<span class="pl-ent">Switch</span><span class="pl-c1">&gt;</span>
          <span class="pl-c1">&lt;</span>/<span class="pl-ent">div</span><span class="pl-c1">&gt;</span>
          <span class="pl-c1">&lt;</span><span class="pl-ent">AmplifySignOut</span> /<span class="pl-c1">&gt;</span>
        <span class="pl-c1">&lt;</span>/<span class="pl-ent">HashRouter</span><span class="pl-c1">&gt;</span>
        <span class="pl-kos">{</span> <span class="pl-s1">showOverlay</span> <span class="pl-c1">&amp;&amp;</span> <span class="pl-kos">(</span>
          <span class="pl-c1">&lt;</span><span class="pl-ent">CreatePost</span>
            <span class="pl-c1">updateOverlayVisibility</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">updateOverlayVisibility</span><span class="pl-kos">}</span>
            <span class="pl-c1">updatePosts</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">setPostState</span><span class="pl-kos">}</span>
            <span class="pl-c1">posts</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">posts</span><span class="pl-kos">}</span>
          /<span class="pl-c1">&gt;</span>
        <span class="pl-kos">)</span><span class="pl-kos">}</span>
    <span class="pl-c1">&lt;</span>/<span class="pl-c1">&gt;</span>
  <span class="pl-kos">)</span><span class="pl-kos">;</span>
<span class="pl-kos">}</span>

<span class="pl-k">const</span> <span class="pl-s1">dividerStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">margin-top</span>: <span class="pl-c1">15<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">`</span>

<span class="pl-k">const</span> <span class="pl-s1">contentStyle</span> <span class="pl-c1">=</span> <span class="pl-en">css</span><span class="pl-s">`</span>
<span class="pl-s">  <span class="pl-c1">min-height</span>: <span class="pl-en">calc</span>(<span class="pl-c1">100<span class="pl-smi">vh</span></span> <span class="pl-c1">-</span> <span class="pl-c1">45<span class="pl-smi">px</span></span>);</span>
<span class="pl-s">  <span class="pl-c1">padding</span>: <span class="pl-c1">0<span class="pl-smi">px</span></span> <span class="pl-c1">40<span class="pl-smi">px</span></span>;</span>
<span class="pl-s">`</span>

<span class="pl-k">export</span> <span class="pl-k">default</span> <span class="pl-en">withAuthenticator</span><span class="pl-kos">(</span><span class="pl-v">Router</span><span class="pl-kos">)</span><span class="pl-kos">;</span></pre></div>
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#deleting-the-existing-data)Deleting the existing data
Now the app is ready to test out, but before we do let's delete the existing data in the database. To do so, follow these steps:

1. Open the Amplify Console

<div class="highlight highlight-source-shell"><pre>$ amplify console</pre></div>

1. 
    Click on **API**, then click on **PostTable** under the **Data sources** tab.

1. 
    Click on the **Items** tab.

1. 
    Select the items in the database and delete them by choosing **Delete** from the **Actions** button.


### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#testing-the-app)Testing the app
Now we can try everything out. To start the app, run the `start` command:
<div class="highlight highlight-source-shell"><pre>$ npm start</pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#hosting)Hosting
The Amplify Console is a hosting service with continuous integration and deployment.
The first thing we need to do is [create a new GitHub repo](https://github.com/new) for this project. Once we've created the repo, we'll copy the URL for the project to the clipboard &amp; initialize git in our local project:
<div class="highlight highlight-source-shell"><pre>$ git init

$ git remote add origin git@github.com:username/project-name.git

$ git add <span class="pl-c1">.</span>

$ git commit -m <span class="pl-s"><span class="pl-pds">'</span>initial commit<span class="pl-pds">'</span></span>

$ git push origin master</pre></div>
Next we'll visit the Amplify Console for the app we've already deployed:
<div class="highlight highlight-source-shell"><pre>$ amplify console</pre></div>
In the **Frontend Environments** section, under **Connect a frontend web app** choose **GitHub** then then click on **Connect branch**.
Next, under "Frontend environments", authorize Github as the repository service.
Next, we'll choose the new repository &amp; branch for the project we just created &amp; click **Next**.
In the next screen, we'll create a new role &amp; use this role to allow the Amplify Console to deploy these resources &amp; click **Next**.
Finally, we can click **Save and Deploy** to deploy our application!
Now, we can push updates to Master to update our application.
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#adding-authorization-to-the-graphql-api)Adding Authorization to the GraphQL API
You can update the AppSync API to enable multiple authorization modes.
In this example, we will update the API to use the both Cognito and API Key to enable a combination of public and private access. This will also enable us to implement authorization for the API.
To enable multiple authorization modes, reconfigure the API:
<div class="highlight highlight-source-shell"><pre>$ amplify update api

<span class="pl-k">?</span> Please <span class="pl-k">select</span> <span class="pl-smi">from</span> one of the below mentioned services: GraphQL   
<span class="pl-k">?</span> Select from the options below: Update auth settings
<span class="pl-k">?</span> Choose the default authorization <span class="pl-c1">type</span> <span class="pl-k">for</span> the API: API key
<span class="pl-k">?</span> Enter a description <span class="pl-k">for</span> the API key: public
<span class="pl-k">?</span> After how many days from now the API key should expire (1-365): 365 <span class="pl-k">&lt;</span>or your preferred expiration<span class="pl-k">&gt;</span>
<span class="pl-k">?</span> Configure additional auth types<span class="pl-k">?</span> Y
<span class="pl-k">?</span> Choose the additional authorization types you want to configure <span class="pl-k">for</span> the API: Amazon Cognito User Pool</pre></div>
Now, update the GraphQL schema to the following:
**amplify/backend/api/Postagram/schema.graphql**
<div class="highlight highlight-source-graphql"><pre><span class="pl-k">type</span> <span class="pl-c1">Post</span> <span class="pl-en">@model</span>
  <span class="pl-en">@auth</span>(<span class="pl-v">rules</span>: [
    { <span class="pl-c1"><span class="pl-s">allow</span></span>:<span class="pl-c1"> owner</span> },
    { <span class="pl-c1"><span class="pl-s">allow</span></span>:<span class="pl-c1"> public</span>, <span class="pl-c1"><span class="pl-s">operations</span></span>: [<span class="pl-c1">read</span>] },
    { <span class="pl-c1"><span class="pl-s">allow</span></span>:<span class="pl-c1"> private</span>, <span class="pl-c1"><span class="pl-s">operations</span></span>: [<span class="pl-c1">read</span>] }
  ]) {
  <span class="pl-v">id</span>: <span class="pl-c1">ID</span><span class="pl-k">!</span>
  <span class="pl-v">name</span>: <span class="pl-c1">String</span><span class="pl-k">!</span>
  <span class="pl-v">location</span>: <span class="pl-c1">String</span><span class="pl-k">!</span>
  <span class="pl-v">description</span>: <span class="pl-c1">String</span><span class="pl-k">!</span>
  <span class="pl-v">image</span>: <span class="pl-c1">String</span>
  <span class="pl-v">owner</span>: <span class="pl-c1">String</span>
}</pre></div>
Deploy the changes:
<div class="highlight highlight-source-shell"><pre>$ amplify push -y</pre></div>
Now, you will have two types of API access:

1. Private (Cognito) - to create a post, a user must be signed in. Once they have created a post, they can update and delete their own post. They can also read all posts.
1. Public (API key) - Any user, regardless if they are signed in, can query for posts or a single post.

Using this combination, you can easily query for just a single user's posts or for all posts.
To make this secondary private API call from the client, the authorization type needs to be specified in the query or mutation:
<div class="highlight highlight-source-js"><pre><span class="pl-k">const</span> <span class="pl-s1">postData</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-c1">API</span><span class="pl-kos">.</span><span class="pl-en">graphql</span><span class="pl-kos">(</span><span class="pl-kos">{</span>
  <span class="pl-c1">mutation</span>: <span class="pl-s1">createPost</span><span class="pl-kos">,</span>
  <span class="pl-c1">authMode</span>: <span class="pl-s">'AMAZON_COGNITO_USER_POOLS'</span><span class="pl-kos">,</span>
  <span class="pl-c1">variables</span>: <span class="pl-kos">{</span>
    <span class="pl-c1">input</span>: <span class="pl-s1">postInfo</span>
  <span class="pl-kos">}</span>
<span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">;</span></pre></div>
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#adding-a-new-route-to-view-only-your-own-posts)Adding a new route to view only your own posts
Next we will update the app to create a new route for viewing only the posts that we've created.
To do so, first open **CreatePost.js** and update the `save` mutation with the following to specify the `authmode` and set the owner of the post in the local state:
<div class="highlight highlight-source-js"><pre><span class="pl-k">async</span> <span class="pl-k">function</span> <span class="pl-en">save</span><span class="pl-kos">(</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-k">try</span> <span class="pl-kos">{</span>
    <span class="pl-k">const</span> <span class="pl-kos">{</span> name<span class="pl-kos">,</span> description<span class="pl-kos">,</span> location<span class="pl-kos">,</span> image <span class="pl-kos">}</span> <span class="pl-c1">=</span> <span class="pl-s1">formState</span><span class="pl-kos">;</span>
    <span class="pl-k">if</span> <span class="pl-kos">(</span>!<span class="pl-s1">name</span> <span class="pl-c1">||</span> !<span class="pl-s1">description</span> <span class="pl-c1">||</span> !<span class="pl-s1">location</span> <span class="pl-c1">||</span> !<span class="pl-s1">image</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">)</span> <span class="pl-k">return</span><span class="pl-kos">;</span>
    <span class="pl-en">updateFormState</span><span class="pl-kos">(</span><span class="pl-s1">currentState</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">(</span><span class="pl-kos">{</span> ...<span class="pl-s1">currentState</span><span class="pl-kos">,</span> <span class="pl-c1">saving</span>: <span class="pl-c1">true</span> <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-k">const</span> <span class="pl-s1">postId</span> <span class="pl-c1">=</span> <span class="pl-en">uuid</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-k">const</span> <span class="pl-s1">postInfo</span> <span class="pl-c1">=</span> <span class="pl-kos">{</span> name<span class="pl-kos">,</span> description<span class="pl-kos">,</span> location<span class="pl-kos">,</span> <span class="pl-c1">image</span>: <span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">,</span> <span class="pl-c1">id</span>: <span class="pl-s1">postId</span> <span class="pl-kos">}</span><span class="pl-kos">;</span>

    <span class="pl-k">await</span> <span class="pl-v">Storage</span><span class="pl-kos">.</span><span class="pl-en">put</span><span class="pl-kos">(</span><span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">.</span><span class="pl-c1">name</span><span class="pl-kos">,</span> <span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">image</span><span class="pl-kos">.</span><span class="pl-c1">fileInfo</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-k">await</span> <span class="pl-c1">API</span><span class="pl-kos">.</span><span class="pl-en">graphql</span><span class="pl-kos">(</span><span class="pl-kos">{</span>
      <span class="pl-c1">query</span>: <span class="pl-s1">createPost</span><span class="pl-kos">,</span>
      <span class="pl-c1">variables</span>: <span class="pl-kos">{</span> <span class="pl-c1">input</span>: <span class="pl-s1">postInfo</span> <span class="pl-kos">}</span><span class="pl-kos">,</span>
      <span class="pl-c1">authMode</span>: <span class="pl-s">'AMAZON_COGNITO_USER_POOLS'</span>
    <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">;</span> <span class="pl-c">// updated</span>
    <span class="pl-k">const</span> <span class="pl-kos">{</span> username <span class="pl-kos">}</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-v">Auth</span><span class="pl-kos">.</span><span class="pl-en">currentAuthenticatedUser</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span> <span class="pl-c">// new</span>
    <span class="pl-en">updatePosts</span><span class="pl-kos">(</span><span class="pl-kos">[</span>...<span class="pl-s1">posts</span><span class="pl-kos">,</span> <span class="pl-kos">{</span> ...<span class="pl-s1">postInfo</span><span class="pl-kos">,</span> <span class="pl-c1">image</span>: <span class="pl-s1">formState</span><span class="pl-kos">.</span><span class="pl-c1">file</span><span class="pl-kos">,</span> <span class="pl-c1">owner</span>: <span class="pl-s1">username</span> <span class="pl-kos">}</span><span class="pl-kos">]</span><span class="pl-kos">)</span><span class="pl-kos">;</span> <span class="pl-c">// updated</span>
    <span class="pl-en">updateFormState</span><span class="pl-kos">(</span><span class="pl-s1">currentState</span> <span class="pl-c1">=&gt;</span> <span class="pl-kos">(</span><span class="pl-kos">{</span> ...<span class="pl-s1">currentState</span><span class="pl-kos">,</span> <span class="pl-c1">saving</span>: <span class="pl-c1">false</span> <span class="pl-kos">}</span><span class="pl-kos">)</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
    <span class="pl-en">updateOverlayVisibility</span><span class="pl-kos">(</span><span class="pl-c1">false</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-kos">}</span> <span class="pl-k">catch</span> <span class="pl-kos">(</span><span class="pl-s1">err</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
    <span class="pl-smi">console</span><span class="pl-kos">.</span><span class="pl-en">log</span><span class="pl-kos">(</span><span class="pl-s">'error: '</span><span class="pl-kos">,</span> <span class="pl-s1">err</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-kos">}</span>
<span class="pl-kos">}</span></pre></div>
Next, open **App.js**.
Create a new piece of state to hold your own posts named `myPosts`:
<div class="highlight highlight-source-js"><pre><span class="pl-k">const</span> <span class="pl-kos">[</span><span class="pl-s1">myPosts</span><span class="pl-kos">,</span> <span class="pl-s1">updateMyPosts</span><span class="pl-kos">]</span> <span class="pl-c1">=</span> <span class="pl-en">useState</span><span class="pl-kos">(</span><span class="pl-kos">[</span><span class="pl-kos">]</span><span class="pl-kos">)</span><span class="pl-kos">;</span></pre></div>
Next, in the `setPostState` method, update `myPosts` with posts from the signed in user:
<div class="highlight highlight-source-js"><pre><span class="pl-k">async</span> <span class="pl-k">function</span> <span class="pl-en">setPostState</span><span class="pl-kos">(</span><span class="pl-s1">postsArray</span><span class="pl-kos">)</span> <span class="pl-kos">{</span>
  <span class="pl-k">const</span> <span class="pl-s1">user</span> <span class="pl-c1">=</span> <span class="pl-k">await</span> <span class="pl-v">Auth</span><span class="pl-kos">.</span><span class="pl-en">currentAuthenticatedUser</span><span class="pl-kos">(</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-k">const</span> <span class="pl-s1">myPostData</span> <span class="pl-c1">=</span> <span class="pl-s1">postsArray</span><span class="pl-kos">.</span><span class="pl-en">filter</span><span class="pl-kos">(</span><span class="pl-s1">p</span> <span class="pl-c1">=&gt;</span> <span class="pl-s1">p</span><span class="pl-kos">.</span><span class="pl-c1">owner</span> <span class="pl-c1">===</span> <span class="pl-s1">user</span><span class="pl-kos">.</span><span class="pl-c1">username</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-en">updateMyPosts</span><span class="pl-kos">(</span><span class="pl-s1">myPostData</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
  <span class="pl-en">updatePosts</span><span class="pl-kos">(</span><span class="pl-s1">postsArray</span><span class="pl-kos">)</span><span class="pl-kos">;</span>
<span class="pl-kos">}</span></pre></div>
Now, add a new route to show your posts:
<div class="highlight highlight-source-js"><pre><span class="pl-c1">&lt;</span><span class="pl-ent">Route</span> <span class="pl-c1">exact</span> <span class="pl-c1">path</span><span class="pl-c1">=</span><span class="pl-s">"/myposts"</span> <span class="pl-c1">&gt;</span>
  <span class="pl-c1">&lt;</span><span class="pl-ent">Posts</span> <span class="pl-c1">posts</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">myPosts</span><span class="pl-kos">}</span> /<span class="pl-c1">&gt;</span>
<span class="pl-c1">&lt;</span>/<span class="pl-ent">Route</span><span class="pl-c1">&gt;</span></pre></div>
Finally, open **Header.js** and add a link to the new route:
<div class="highlight highlight-source-js"><pre><span class="pl-c1">&lt;</span><span class="pl-ent">Link</span> <span class="pl-c1">to</span><span class="pl-c1">=</span><span class="pl-s">"/myposts"</span> <span class="pl-c1">className</span><span class="pl-c1">=</span><span class="pl-kos">{</span><span class="pl-s1">linkStyle</span><span class="pl-kos">}</span><span class="pl-c1">&gt;</span>My Posts<span class="pl-c1">&lt;</span>/<span class="pl-ent">Link</span><span class="pl-c1">&gt;</span></pre></div>
Next, test it out:
<div class="highlight highlight-source-shell"><pre>$ npm start</pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#additional-learning--use-cases)Additional learning &amp; use cases
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#relationships)Relationships
What if we wanted to create a relationship between the Post and another type.
In this example, we add a new `Comment` type and create a relationship using the `@connection` directive. Doing this will enable a one to many relationship between `Post` and `Comment` types.
<div class="highlight highlight-source-graphql"><pre><span class="pl-c"># amplify/backend/api/Postagram/schema.graphql</span>

<span class="pl-k">type</span> <span class="pl-c1">Post</span> <span class="pl-en">@model</span>
  <span class="pl-en">@auth</span>(<span class="pl-v">rules</span>: [
    { <span class="pl-c1"><span class="pl-s">allow</span></span>:<span class="pl-c1"> owner</span> },
    { <span class="pl-c1"><span class="pl-s">allow</span></span>:<span class="pl-c1"> public</span>, <span class="pl-c1"><span class="pl-s">operations</span></span>: [<span class="pl-c1">read</span>] },
    { <span class="pl-c1"><span class="pl-s">allow</span></span>:<span class="pl-c1"> private</span>, <span class="pl-c1"><span class="pl-s">operations</span></span>: [<span class="pl-c1">read</span>] }
  ]) {
  <span class="pl-v">id</span>: <span class="pl-c1">ID</span><span class="pl-k">!</span>
  <span class="pl-v">name</span>: <span class="pl-c1">String</span><span class="pl-k">!</span>
  <span class="pl-v">location</span>: <span class="pl-c1">String</span><span class="pl-k">!</span>
  <span class="pl-v">description</span>: <span class="pl-c1">String</span><span class="pl-k">!</span>
  <span class="pl-v">image</span>: <span class="pl-c1">String</span>
  <span class="pl-v">owner</span>: <span class="pl-c1">String</span>
  <span class="pl-v">comments</span>: [<span class="pl-c1">Comment</span>] <span class="pl-en">@connection</span>
}

<span class="pl-k">type</span> <span class="pl-c1">Comment</span> <span class="pl-en">@model</span>
  <span class="pl-en">@auth</span>(<span class="pl-v">rules</span>: [
    { <span class="pl-c1"><span class="pl-s">allow</span></span>:<span class="pl-c1"> owner</span> },
    { <span class="pl-c1"><span class="pl-s">allow</span></span>:<span class="pl-c1"> public</span>, <span class="pl-c1"><span class="pl-s">operations</span></span>: [<span class="pl-c1">read</span>] },
    { <span class="pl-c1"><span class="pl-s">allow</span></span>:<span class="pl-c1"> private</span>, <span class="pl-c1"><span class="pl-s">operations</span></span>: [<span class="pl-c1">read</span>] }
  ]) {
  <span class="pl-v">id</span>: <span class="pl-c1">ID</span>
  <span class="pl-v">message</span>: <span class="pl-c1">String</span>
  <span class="pl-v">owner</span>: <span class="pl-c1">String</span>
}</pre></div>
Now, we can create relationships between posts and comments. Let's test this out with the following operations:
<div class="highlight highlight-source-graphql"><pre><span class="pl-k">mutation</span> <span class="pl-en">createPost</span> {
  <span class="pl-v">createPost</span>(<span class="pl-v">input</span>: {
    <span class="pl-c1"><span class="pl-s">id</span></span>: <span class="pl-s"><span class="pl-pds">"</span>test-id-post-1<span class="pl-pds">"</span></span>
    <span class="pl-c1"><span class="pl-s">name</span></span>: <span class="pl-s"><span class="pl-pds">"</span>Post 1<span class="pl-pds">"</span></span>
    <span class="pl-c1"><span class="pl-s">location</span></span>: <span class="pl-s"><span class="pl-pds">"</span>Jamaica<span class="pl-pds">"</span></span>
    <span class="pl-c1"><span class="pl-s">description</span></span>: <span class="pl-s"><span class="pl-pds">"</span>Great vacation<span class="pl-pds">"</span></span>
  }) {
    <span class="pl-v">id</span>
    <span class="pl-v">name</span>
    <span class="pl-v">description</span>
  }
}

<span class="pl-k">mutation</span> <span class="pl-en">createComment</span> {
  <span class="pl-v">createComment</span>(<span class="pl-v">input</span>: {
    <span class="pl-c1"><span class="pl-s">postCommentsId</span></span>: <span class="pl-s"><span class="pl-pds">"</span>test-id-post-1<span class="pl-pds">"</span></span>
    <span class="pl-c1"><span class="pl-s">message</span></span>: <span class="pl-s"><span class="pl-pds">"</span>Great post!<span class="pl-pds">"</span></span>
  }) {
    <span class="pl-v">id</span> <span class="pl-v">message</span>
  }
}

<span class="pl-k">query</span> <span class="pl-en">listPosts</span> {
  <span class="pl-v">listPosts</span> {
    <span class="pl-v">items</span> {
      <span class="pl-v">id</span>
      <span class="pl-v">name</span>
      <span class="pl-v">description</span>
      <span class="pl-v">location</span>
      <span class="pl-v">comments</span> {
        <span class="pl-v">items</span> {
          <span class="pl-v">message</span>
          <span class="pl-v">owner</span>
        }
      }
    }
  }
}</pre></div>
If you'd like to read more about the `@auth` directive, check out the documentation [here](https://docs.amplify.aws/cli/graphql-transformer/directives#auth).
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#local-mocking)Local mocking
To mock the API, database, and storage locally, you can run the `mock` command:
<div class="highlight highlight-source-shell"><pre>$ amplify mock</pre></div>
## &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#removing-services)Removing Services
If at any time, or at the end of this workshop, you would like to delete a service from your project &amp; your account, you can do this by running the `amplify remove` command:
<div class="highlight highlight-source-shell"><pre>$ amplify remove auth

$ amplify push</pre></div>
If you are unsure of what services you have enabled at any time, you can run the `amplify status` command:
<div class="highlight highlight-source-shell"><pre>$ amplify status</pre></div>
`amplify status` will give you the list of resources that are currently enabled in your app.
### &#10;[<span aria-hidden="true" class="octicon octicon-link"></span>](#deleting-the-amplify-project-and-all-services)Deleting the Amplify project and all services
If you'd like to delete the entire project, you can run the `delete` command:
<div class="highlight highlight-source-shell"><pre>$ amplify delete</pre></div>



              </article>
            </div>
          </div>
        </div>
      </div>

    

  </div>
  <div>&nbsp;</div>
  </div>
  

