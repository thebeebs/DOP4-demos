# Snippets for DOP4 Demo 2

Back Up

```
cd ../
```

Make Directory and move into it

```
mkdir ecs-devops-sandbox-cdk && cd ecs-devops-sandbox-cdk
```

Create a CDK project

```
cdk init --language python 
```

Install requirements

```
pip install -r requirements.txt
```

Install requirements

```
pip install aws_cdk.aws_ec2 aws_cdk.aws_ecs aws_cdk.aws_ecr aws_cdk.aws_iam
```

Replace the contents of the file ecs_devops_sandbox_cdk/ecs_devops_sandbox_cdk_stack.py (automatically created by the CDK) with the code below

```
"""AWS CDK module to create ECS infrastructure"""
from aws_cdk import (core, aws_ecs as ecs, aws_ecr as ecr, aws_ec2 as ec2, aws_iam as iam)

class EcsDevopsSandboxCdkStack(core.Stack):

    def __init__(self, scope: core.Construct, id: str, **kwargs) -> None:
        super().__init__(scope, id, **kwargs)

       
```

Create the ECR Repository

```
        ecr_repository = ecr.Repository(self,
                                        "ecs-devops-sandbox-repository",
                                        repository_name="ecs-devops-sandbox-repository")
```
Create the ECS Cluster (and VPC)

```
        vpc = ec2.Vpc(self,
                      "ecs-devops-sandbox-vpc",
                      max_azs=3)
        cluster = ecs.Cluster(self,
                              "ecs-devops-sandbox-cluster",
                              cluster_name="ecs-devops-sandbox-cluster",
                              vpc=vpc)
```

Create the ECS Task Definition with placeholder container (and named Task Execution IAM Role)

```
        execution_role = iam.Role(self,
                                  "ecs-devops-sandbox-execution-role",
                                  assumed_by=iam.ServicePrincipal("ecs-tasks.amazonaws.com"),
                                  role_name="ecs-devops-sandbox-execution-role")
        execution_role.add_to_policy(iam.PolicyStatement(
            effect=iam.Effect.ALLOW,
            resources=["*"],
            actions=[
                "ecr:GetAuthorizationToken",
                "ecr:BatchCheckLayerAvailability",
                "ecr:GetDownloadUrlForLayer",
                "ecr:BatchGetImage",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
                ]
        ))
        task_definition = ecs.FargateTaskDefinition(self,
                                                    "ecs-devops-sandbox-task-definition",
                                                    execution_role=execution_role,
                                                    family="ecs-devops-sandbox-task-definition")
        container = task_definition.add_container(
            "ecs-devops-sandbox",
            image=ecs.ContainerImage.from_registry("amazon/amazon-ecs-sample")
        )
```
Create the ECS Service

```
        service = ecs.FargateService(self,
                                     "ecs-devops-sandbox-service",
                                     cluster=cluster,
                                     task_definition=task_definition,
                                     service_name="ecs-devops-sandbox-service")
```


Lets Deploy that to create the CDK Infastructure. 

```
cdk deploy
```