[GitHub actions](https://github.com/features/actions) enables developers/DevOps engineers to smartly incorporate CI/CD pipelines, perform tasks on actions on different branches and many more things. In the blog, we will look into GitHub actions which uses two workflows - 
1. [SAM Pipeline to deploy to AWS environments](#/sam-pipeline)
2. [SNS Publish on commits / delete branch](#/notify)

### My Workflow
#### SAM Pipeline to deploy to AWS environments <a name="sam-pipeline"></a>
[SAM Pipeline](https://aws.amazon.com/blogs/compute/introducing-aws-sam-pipelines-automatically-generate-deployment-pipelines-for-serverless-applications/) enables leveraging SAM and GitHub actions to deploy your Serverless application to AWS environment. This leverages `sam build` and `sam deploy` commands performed via SAM CLI in the GitHub actions (Ubuntu) environment. 
The workflow defines jobs for -
+ test : Checking if the GitHub event is *push*.
+ delete-feature : If the GitHub event is *delete* on a feature branch with the name starting with *feature-**, it deletes that particular feature branch with `sam delete`.
+ build-and-deploy-feature : If the GitHub event is *push* on a feature branch with the name starting with *feature-**, it deploys the app to AWS with the code from feature branch with `sam deploy`.
+ build-and-package : If the GitHub event is *push* on a main branch, it packages the the app for two stages - **dev** and **prod** and uploads the artifacts zip files to S3 bucket with `sam package` command.
+ deploy-testing : The code will use the Stage 1 - **dev** and deploys the Serverless application to AWS with `sam deploy`.
+ integration-test : If the branch is *main*, you can run specific integration tests via bash scripts. And if the tests are passed, it proceeds to the next step.
+ deploy-prod : If the integration tests are passed, the complete code from *main branch* is deployed to AWS with `sam deploy`.

![SAM Pipeline](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/unhftbdtw79mrtyfj11s.png)

#### SNS Publish on commits / delete branch <a name="notify"></a>
The workflow is used to notify with [AWS Simple Notification Service (SNS)](https://aws.amazon.com/sns/) by pushing to a SNS topic. This workflow uses `Danushka96/sns-action@v2` which performs `SNS:PUBLISH` to send message to the SNS topic.

![SNS Publish](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hnwtvs1gb0av89p9sub7.png)

### Submission Category: 
DIY Deployments

### Yaml File or Link to Code
The blog post implementation of 2 workflows is implemented with `pipeline.yaml` and `sns.yml`

{% github zachjonesnoel/sam-github-actions/ %}

### Additional Resources / Info
This project uses several GitHub actions - 
+ [actions/checkout@v2](https://github.com/actions/checkout)
+ [actions/setup-python@v2](https://github.com/actions/setup-python)
+ [aws-actions/setup-sam](https://github.com/actions/checkout-sam)
+ [aws-actions/configure-aws-credentials@v1](https://github.com/aws-actions/configure-aws-credentials)
+ [actions/upload-artifact@v2](https://github.com/actions/upload-artifact)
+ [actions/download-artifact@v2](https://github.com/actions/checkoutom/actions/download-artifact)
+ [Danushka96/sns-action@v2](https://github.com/actions/checkout)

