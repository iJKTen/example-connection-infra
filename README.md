# GitHub Connection Infrastructure

CloudFormation template that creates a CodeConnection to GitHub and the IAM role used by Sync from Git. The connection ARN is exported as a cross-stack value so site stacks can import it with `!ImportValue`.

## Prerequisites

- An AWS account

## What Gets Created

- One `AWS::CodeConnections::Connection` resource linked to GitHub
- One `AWS::IAM::Role` (CloudFormationGitSyncRole) for Sync from Git with permissions for CloudFormation, EventBridge, ACM, Route 53, S3, CloudFront, CodePipeline, CodeBuild, IAM, CodeConnections, and CloudWatch Logs
- One cross-stack export: `Example-GitHubConnectionArn`

## Manual Completion

CloudFormation creates the connection in **Pending** status. You must complete the handshake manually:

1. Go to **Developer Tools > Settings > Connections** in the AWS console.
2. Select the pending connection (`github-org-connection`).
3. Click **Update pending connection**.
4. Install the **AWS Connector for GitHub** app on your GitHub account and grant it access to the repos you want to deploy.
5. Authorize the connection.
6. The connection status changes to **Available**.

## Deploy

The initial deploy is manual because Git Sync itself requires a connection to function. You cannot use Git Sync to deploy the connection it depends on.

1. Go to **CloudFormation > Create stack > With new resources**.
2. Upload `infra.yaml` as the template.
3. Name the stack (e.g. `connection-stack`).
4. Create the stack and complete the manual connection steps above.

After the Git Sync role is added to the template (series post 4), Git Sync is connected to this stack. From that point on, pushing changes to this repo updates the stack automatically. No more manual uploads.

Deploy this stack before any site stacks. The site stacks use `!ImportValue Example-GitHubConnectionArn`, which only resolves after this stack reaches `CREATE_COMPLETE` and the connection is available.
