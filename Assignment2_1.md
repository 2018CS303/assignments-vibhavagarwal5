# Assignment 2_1 - Jenkins

## Installing Blue Ocean
- Go to `Manage Jenkins > Manage Plugins`
- Choose the `Available` tab and search for `blue ocean`
- Select the `Blue Ocean (BlueOcean Aggregator)` plugin. Only this plugin is recommended since all other Blue Ocean plugins will be installed automatically as dependencies.
- Blue Ocean will be activated when Jenkins restarts.
- Choose the `Open Blue Ocean` option from the left menu to use the Blue Ocean UI.

## Building a private GitHub Repository as a FreeStyle project

- Create a new freestyle project
- Go to `Source Code Management` and select `Git`.
- Add your private repository's URL in the `Repository URL` input box.
- Since the repository is private Jenkins will not be able to access the repo without credentials. Click on `Add` button next to `Credential` input box. 
- Choose `Jenkins` as the Credential Provider in the dropdown menu. A popup should appear
asking for the details.
- Choose `Username with Password` under the `Kind` option. Enter GitHub username and password under their respective text boxes.
- After adding your credentials choose the newly added credentials in the dropdown menu that appears next to `Credentials` label.
- Now the private repository can be used with Jenkins like any other public repository.

## Using Git SCM Poll
- Go to `Build Triggers` section and choose `GitHub hook trigger for GITScm polling`.

- Adding Jenkins service to GitHub. Now we need to set up our GitHub repo to make a request to Jenkins webhook (Whenever a commit is made on the Github repo) so that the polling logic can be applied. 
    - Go to your GitHub repo and navigate to `Settings`.
    - Choose `Webhooks` from the submenu and click on `Add webhook` on the upper right corner.
    - Add `http://<public-ip or URL>/github-webhook/` under `Payload URL`. <br>
    *(Check the how to get **Public IP** as documented below)*
    - Select `Content type` as `application/json`.
    - Choose what kind of event you want github to trigger under `Which events would you like to trigger this webhook?` as `Just the push event`.
    - Make sure the service is active by selective the `Active` checkbox.
    - Now GitHub should make a request to the Jenkins webhook and cause a build (if required) to occur.

- Get Public IP (Using ngrok)
    - You can get the Public IP of your `locolhost:8080` (Where Jenkins is running) using ngrok.
    - Go to [ngrok's official site](https://ngrok.com/download) and follow the instructions to install ngrok. 
    <br>OR<br>
    Go [here](https://www.npmjs.com/package/ngrok) to download ngrok as a `npm package`.
    - Then do `ngrok http 8080` (because 8080 is Jenkins port which has to be mapped to a public IP) and then you will get a public IP of the form `http://<SOMETHING>.ngrok.io` and then use this in the github webhooks.

## Post Build Actions - Email Notification
Steps to setup Email Notifications as a post build action have been documented below. Email Notifications allow us to send customized email notifications after the build process.

- ### Configuring Email
    We need to first add the details of the SMTP server and the mail account so that Jenkins can send the mail.

    - Go to `Manage Jenkins > Configure Systems` and scroll down to the `E-mail Notification` section.
    - Add the SMTP server details e.g. `smtp.gmail.com` for a `Gmail account`.
    - Click on the `Advanced` button to configure the mail account that will be used to send the mails.
    - Select `Use SMTP Authentication` if required e.g. Gmail uses a password for authentication and so this must be selected.
    - Enter the account username under `User Name` and password under `Password`.
    - Select `Use SSL` and specify the `SMTP port` (465 in most cases).
    - You can test the SMTP configurations by checking `Test configuration by sending test e-mail` checkbox and then type in the `Test e-mail recipient` input box and click `Test Configuration` button. If you get the test email that means you have successfully setup the SMTP on jenkins.
    - With this the basic email configuration is complete. 

    **(Optional)** 
    
    - Select `Allow sending to unregistered users` if mail should be sent to non-Jenkins users.
    - One can use the other options to customize the mail sent. For e.g. we can specify the list of default recipients and default content. 
    - Jenkins injects certain variables like `$PROJECT_NAME`, `$BUILD_NUMBER` and `$BUILD_STATUS` which can be used in the content to get dynamic values.

- ### Adding the post build action
    - Go to `Configuration` page of project and scroll down to `Post-build Actions`.
    - Click on `Add post-build action` and choose `Email Notification` from the dropdown menu.
    - Jenkins once again provides the default settings through variables e.g. `$DEFAULT_RECIPIENTS`, `$DEFAULT_SUBJECT` and `$DEFAULT_CONTENT`.
    - These options can be used for project based customizations of the email notification.

With this the Post build action for extended email notification is complete.
