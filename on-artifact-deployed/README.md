# On Artifact Deployed

This is an experiment for handling a JFrog Platform event within the platform using JFrog Pipelines.

The event in this experiment is "Artifact Deployed". 
This event is fired for each artifact (file) after it is deployed (uploaded) to Artifactory.
The handling in this example is simply annotate the artifact with a new property: `artifact.deployed.handled=true`. 

A similar approach can be used to handle other events and/or do other actions.

## Implementation

On artifact deployed - trigger a JFrog Platform webhook which calls an incoming webhook in JFrog Pipelines, which then triggers a pipeline that runs and adds a custom property on the uploaded file.

1. Ensure [**general prerequisites**](../README.md#prerequisites)
   
2. Create an [**Incoming Webhook Integration**](https://www.jfrog.com/confluence/display/JFROG/Incoming+Webhook+Integration). 
   This integration will create a new endpoint in the JFrog Platform which can then be used in a pipeline. Use:  
   | Configuration Field | Value |
   | ------------------- | ----- |
   | Name                | Give it a name, e.g. `incoming_webhook` |
   | Integration Type    | Select `Incoming Webhook` |
   | Authorization Type  | Select, in this example we use `basic` |
   | User Name           | Provide a username which can use this endpoint |
   | Password            | Provide the expected password for the given username |

3. Create an [**Artifactory Integration**](https://www.jfrog.com/confluence/display/JFROG/Artifactory+Integration).
   This integration will be used by the `jfrog` command line tool to authenticate with Artifactory when setting the custom property on the deployed artifact.
   Use:
   | Configuration Field | Value |
   | ------------------- | ----- |
   | Name                | Give it a name, e.g. `artifactory_deployer` |
   | Integration Type    | Select `Artifactory` |
   | Artifactory URL     | The base URL to your Artifactory, should be pre-populated with the URL of your instance |
   | User                | Set the username to use. Can be your user for simplicity, but should have restricted access in real use case |
   | API Key             | Set the API key of the user to use |

4. Define the [webhook](https://www.jfrog.com/confluence/display/JFROG/Webhooks).
   Use:
   | Configuration Field | Value |
   | ------------------- | ----- |
   | Name                | Give it a name |
   | URL                 | Set the URL of the incoming webhook integration defined above |
   | Events              | Select `Artifact > Artifact was Deployed`. Add the relevant repositories (**DO NOT USE `Any Local Repository`**, see [notes](#notes) below) |
   | Secret Token        | Keep empty (for simplicity) |
   | Custom Headers      | Name: `Authorization` , Value: `Basic <base64-of-credentials>` (use e.g. [this online service](https://www.base64encode.org/) to encode the `<username>:<password>` you defined for the incoming webhook integration) |

Refer to the [`pipelines.yml`](./pipelines.yml) file to see how the `on_artifact_deployed` pipeline and the `incoming_webhook_artifact_deployed` resource are defined.

Now, every time an artifact is deployed to one of the repositories included in the webhook, a pipeline run will be triggered. The webhook will call the incoming webhook integration using an HTTP `POST` request, providing it with the event payload. Pipelines will update the incoming webhook resource, which will then trigger a pipeline run. The pipeline has one step in which the deployed artifact repository key and path are extracted from the payload, and then used to set the custom property on that artifact.

## Notes

1. Don't use `Any Local Repository` in a webhook when the target is a pipeline configured in the same JFrog Platform. 
   The reason is that JFrog Pipelines use Artifactory as a storage and also deploys artifacts to a local repository in it, which will cause an infinite loop which will continuously trigger new runs.