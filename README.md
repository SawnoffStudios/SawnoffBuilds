# Unreal Build Server

## Files

- [build-project.yml](build-project.yml) – GitHub Actions build script for Unreal Engine projects
- [infra-build.yml](infra-build.yml) – CloudFormation template for EC2 Spot Fleet

## Tools required:

1. [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)
2. [Git for Windows](https://git-scm.com/install/windows)
3. [Git LFS for Windows](https://git-lfs.com/)
4. [Epic Games Launcher](https://store.epicgames.com/en-US/download)
5. [Unreal Engine](https://www.unrealengine.com/) (from Launcher)
6. [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

## Usage

- Create EC2 instance with required tools
- Open Services.msc and open "GitHub Actions Runner Service"
- Update service to Run as Administrator
- Save changes and restart service
- Run commands for [GitHub self-hosted runner](https://docs.github.com/en/actions/how-tos/manage-runners/self-hosted-runners/add-runners)
- Create AMI from EC2 instance
- Create GitHub Actions workflow for your project
- Ensure tags used in `runs-on` match GitHub Actions runner name

## Required IAM policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "cloudformation:CreateStack",
                "cloudformation:DescribeStacks",
                "cloudformation:DescribeEvents",
                "cloudformation:CreateChangeSet",
                "cloudformation:DescribeChangeSet",
                "cloudformation:DeleteChangeSet",
                "cloudformation:ExecuteChangeSet",
                "cloudformation:DeleteStack",
                "ec2:DescribeImages",
                "ec2:CreateLaunchTemplate",
                "ec2:DescribeLaunchTemplates",
                "ec2:DescribeLaunchTemplateVersions",
                "ec2:DeleteLaunchTemplate",
                "ec2:RequestSpotFleet",
                "ec2:RunInstances",
                "ec2:DescribeInstances",
                "ec2:StartInstances",
                "ec2:StopInstances",
                "ec2:DescribeSpotFleetRequests"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "arn:aws:iam::*:role/aws-service-role/://amazonaws.com",
            "Condition": {
                "StringLike": {
                    "iam:AWSServiceName": "spotfleet.amazonaws.com"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Condition": {
                "StringEquals": {
                    "iam:PassedToService": [
                        "ec2.amazonaws.com",
                        "ec2.amazonaws.com.cn"
                    ]
                }
            },
            "Resource": [
                "*"
            ]
        }
    ]
}
```