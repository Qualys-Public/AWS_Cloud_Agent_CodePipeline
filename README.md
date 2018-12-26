# deploy_qualys_CD_Pipeline_AWS
# Installing QCA on Existing AWS Instances using Continuous Configuration management (DevOps) 

Infrastructure as code has helped customers move into continuous deployment phase and can leverage the same concept to introduce continuous configuration management in their environment. In this mode, configuration management of instances is done automatically, using the above technologies, as configurations are committed to version control.
We can consider a scenario where infrastructure as code is stored in git repository as ansible playbook and when any changes/ addition are made like introduction of playbook for installation of QCA and committed to master branch, it gets deployed to all ssm managed instance. SSM managed instances are considered owing to the fact that ssh could not be enabled for all instances for management by ansible.

This tutorial makes use of following services:

1.	AWS CodePipeline

2.	AWS Lambda

3.	Amazon EC2 Run Command

4.	CloudWatch Events

5.	S3 Bucket

6.	Ansible

7.	Git

# Logic: 

we start by creating a Pipeline which will get triggered by any changes in master branch of your selected repository “Configuration Management” (create a new one to start with). That trigger will invoke an AWS Lambda function to execute Run Command on the instance to run ansible playbook locally to install QCA.

# Prerequisites:

1.	Instances are SSM managed

2.	Instances have ansible installed. [Note: installation of ansible could be added to the module as a command.]


# Create a Pipeline:

1.	Create Pipeline named “Configuration_management” with all default parameters. Select Github as source and select master branch of  this forked repo.

![createpipeline](createpipeline.png?raw=true "createpipeline")

![createpipeline2](createpipeline2.png?raw=true "createpipeline2")

2. if you make changes via Console, you will have to create two stages to complete the pipeline. Create a dummy build stage which can later be replaced by invoke stage.

# Deploy Lambdas:

1.	Create IAM roles for the lambdas with permissions as listed in file "Conf_role" from this repo. 

2. Create two lambda functions running on 2.7 named as conf_main and conf_helper and replace them with the contents of the "Conf_main.py" and "Conf_helper.py" from the repo.

![createlambdas](createlambdas.png?raw=true "createlambdas")

# Modify Pipeline:

1.	View the Pipeline and edit it and add one stage after source named as “Invoke-stage”. Click on Add Action Group and give the Action name as “Lambda” and Action Provider as “AWS Lambda”. Select your Lambda Function and Input Artifacts as “SourceArtifacts”.

![addstage](addstage.png?raw=true "addstage")

![configureinvokestage](configureinvokestage.png?raw=true "configureinvokestage")

2.	Delete the build stage create earlier.

![deletebuildstage](deletebuildstage.png?raw=true "deletebuildstage")

3.	Add your own ansible playbook to the repo “Configuration Management” under folder ansible. The demo playbook here is named as "InstallQCA.yml".
