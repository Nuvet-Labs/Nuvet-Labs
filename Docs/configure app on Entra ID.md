Prerequisites to make this work
## Create an App Registration: Go to Microsoft Entra ID (Azure AD) > App registrations > New registration. This gives you the Application (client) ID and Directory (tenant) ID.
## Create a Secret: In that App Registration, go to Certificates & secrets > New client secret. Copy the Value immediately (this is your $clientSecret).
## Grant Permissions: Go to your Data Factory resource in the Azure Portal > Access control (IAM) > Add role assignment. Assign the Data Factory Contributor role to the App Registration you just created. Iy will be in the User, Group or Service principal selector
