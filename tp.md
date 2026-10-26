Continuous Deployment

Now that you've deployed you database, api and proxy manually on your remote server once, integrate what you've done to you GitHub Actions workflow.

This way your application should be automatically deployed everytime a new image is uploaded to DockerHub.

Question

Is it really safe to deploy automatically every new image on the hub ? explain. What can I do to make it more secure?

Check

Continuous deployment works!
Front

Now that you are able to access your api through your server, it is time to add an actual front-end to display informations coming from your API.

Your database, api and httpd must be up on your server and deployed with your Github Actions.

You can find the code of the front ready.

You have to customize your httpd server to make the redirection correct between the API and the front. The httpd server is a proxy within your system.

And don't forget to adapt your CI/CD to also handles the front-end.

Check

Front working
