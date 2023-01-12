# Ironclad EA ServiceNow Example Code
This repository contains all of the ServiceNow application assets for the EA ServiceNow solution guide.

# Installing the ServiceNow application
## Create Github credential
You will need to create a credential in ServiceNow to hold an SSH key for an Github account that can access this repo. If you are only importing the application into ServiceNow (e.g., for a demo), then you only need read access to the repo. If you are making application changes that you want to save back to the repo, you will need read/write access.
### Create SSH Key
If you do not already have a suitable SSH key registered in your Github account, you will need to create one.  Note that ServiceNow has limitations in terms of what encryption algorithms it will support for SSH credentials (e.g., it does NOT support ED25519 encryption, but it DOES support ECDSA).  

Use whatever SSH key generation tool you have - if you are using ssh-keygen on a Mac or other Unix platform, you would do the following to create a new ECDSA key pair:

```
> cd ~/.ssh
> ssh-keygen -t ecdsa -C "<your Github account email address>"
```

Be sure to keep track of the passphrase you use for the SSH key when you create it.  It will be needed in the ServiceNow credential setup.

### Register SSH key with your Github account
The general instructions for adding a new SSH key for your Github account can be [found here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).  The following is a summary of the necessary steps:

- In your GitHub account, navigate to "Settings" (using the menu on your profile picture), and go to "SSH and GPG keys"
- Under "SSH Keys", click the "New" button 
- Set the title to whatever makes sense to you (e.g., "ServiceNow dev key")
- Set the "Key type" to "Authentication key"
- Paste the public key content of your SSH key into the Key field.
- Add the SSH key to your account.

## Install the "Legal Intake" Application in ServiceNow
Installing the Legal Intake app into ServiceNow involves creating a SNOW dev instance (if you don't already have one), creating a new SSH credential to connect to the GitHub repository, and importing the 
### Create a ServiceNow developer instance
- Create an account/login to developer.servicenow.com
- Create a new developer instance if you don’t have one already
### Create ServiceNow SSH Credential
- Go to "Credentials" in the ServiceNow instance and create a new "SSH Private Key" entry in that table.
- Set the name to whatever makes sense to you (e.g., "ServiceNow Guide Github repo")
- Enter your Github user name (NOT your email address) and password, and the passcode for your SSH key.
- Copy the private key contents for your SSH key and paste them into the private key field of the credential.
- Make sure the "Active" checkbox is checked and save the credential entry.
## Import ServiceNow Application
- In the developer instance, go to System Applications -> Studio (e.g., search for “Studio” in the main search)
- In the dialog that appears, choose "Import from Source Control"
- In the dialog that appears, choose the "ssh" option, enter the Github URL for the repository with the ServiceNow code (this repository), choose the SSH credential you create above, and specify the branch containing the code version you want.  Normally you will want to import from "main", but there might be situations where you will want to import a specific version of the application, and if so, use the branch field to specify that.

## Configure Application Properties
There are a set of application properties that need to be configured to allow the application to target the workflows in your Ironclad instance. 

In your ServiceNow instance, open the Legal Intake application in Studio and navigate to the "Properties -> System Properties" section in the navigation.  Then set each property as follows:

- ```ironclad_api_key```: This needs to contain the contents of an API key generated from your Ironclad instance.  
- ```ironclad_environment```: Set this to the appropriate domain for your Ironclad environment ("ironcladapp.com" for production, "demo.ironcladapp.com" for demo, etc.)
- ```ironclad_msa_template```: Set this to the template ID for the MSA workflow you will use in the integration.
- ```ironclad_msa_signer_email_attribute```: Set this to the attribute ID for the countrparty signer email atttibure in your MSA workflow.  Signer attribute IDs are unique for each workflow.
- ```ironclad_msa_signer_name_attribute```: Set this to the attribute ID for the countrparty signer name atttibure in your MSA workflow.  Signer attribute IDs are unique for each workflow.
- ```ironclad_workflow_creator```: Set this to the email address of the Ironclad user you would like to use as the creator of all workflows launched from ServiceNow.
- ```ironclad_nda_template```: Set this to the template ID for the NDA workflow in your Ironclad environment.
## Configure Ironclad Webhooks
Once the application is loaded, you need to retrieve the endpoints of the Scripted REST resources defined in the app and register them to receive webhooks from your Ironclad instance.  In Studio, navigate to the Inbound Integrations -> Scripted REST Resources, and for each item in this list, select it and capture the "Resource path" in the details for the REST endpoint. The full endpoint URL will be the domain of your developer instance (see your browser address bar, typically this will be of the form "devXXXX.service-now.com").  
