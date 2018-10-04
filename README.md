# CFN_Learning_Beginners
   
## Purpose: 
This repository contains a collaboration of Amazon Web Services CloudFormation Template Examples. To give a glimpse of CFN for beginners and progressively deep dive into CFN world of core automation.  

AWS CloudFormation provides a common language for you to describe and provision all the infrastructure resources in your cloud environment.

## Cloud formation in AWS
CloudFormation has two parts: templates and stacks. A template is a JavaScript Object Notation (JSON) or Yet Another Markup Language (YAML) text file. The file, which is declarative and not scripted, defines what AWS resources or non-AWS resources are required to run the application.

###### YAML Reference: https://aws.amazon.com/blogs/mt/the-virtues-of-yaml-cloudformation-and-using-cloudformation-designer-to-convert-json-to-yaml/

## What is AWS CFN Template?
To provision and configure your stack resources, you must understand AWS CloudFormation templates, which are formatted text files in JSON or YAML. **_It's like a blue print_**. 

## Template Anatomy:
```
AWSTemplateFormatVersion: "version date"
Description:
  String
Metadata:
  template metadata
Parameters:
  set of parameters
Mappings:
  set of mappings
Conditions:
  set of conditions
Transform:
  set of transforms
Resources:
  set of resources
Outputs:
  setof outputs
```

###### Reference Link: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html

###### Working with Templates: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-formats.html

###### Resources Template Reference: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html

###### Template.Resources (YAML)

```
Resources:
Logical ID:
	Type: "Resources Type - Ex: EC2, S3 etc..."
	Properties:
	  ...set of properties...
```
```
EC2 Instance Resource - Example:
Resources:
  MyEC2:
    Type: "AWS::EC2::Instance"
    Properties:
	ImageId: "ami-XXXX"
	Instance Type: t2.micro
```
```
S3 Resource - Example:
Resources:
  Mys3bucket:
    Type: "AWS::S3::Bucket"
    Properties:
	BucketName: Hello S3 Bucket
	AccessControl: PublicRead
	WebsiteConfiguration:
	  IndexDocument: index.html
```
```
SecurityGroup Resource - Example:
Resources:
  MySecurityGroup:
    Type: "AWS::EC2::SecurityGroup"
    Properties:
	GroupDescription: Enable SSH access via port 22
	SecurityGroupIngress: 
	  -IpProtocol:tcp
	   FromPort:'22'
	   ToPort:'22'
	   CidrIp:0.0.0.0/0 
```

## What is AWS CFN Stack?
A stack is a collection of AWS resources that you can manage as a single unit. In other words, you can create, update, or delete a collection of resources by creating, updating, or deleting stacks. All the resources in a stack are defined by the stack's AWS CloudFormation template.


## CloudFormation Features:

```
Intrinstic Functions
Multiple Resources
Pseudo Parameters
Mappings
Input Parameters
Outputs
```

###### AWS CloudFormation provides several built-in functions that help you manage your stacks. 

###### Intrinstic functions:

Use intrinsic functions in your templates to assign values to properties that are not available until runtime.

**Note**: You can use intrinsic functions only in specific parts of a template. Currently, you can use intrinsic functions in resource properties, outputs, metadata attributes, and update policy attributes. You can also use intrinsic functions to conditionally create stack resources.

###### IntrinsicFunctions.yaml

```
Resources:
  Ec2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      InstanceType: t2.micro
      ImageId: ami-01da99628f381e50a # Amazon Linux AMI in Singapore
      Tags:
        - Key: "Name"
          Value: !Join [ " ", [ EC2, Instance, with, Fn, Join ] ]
```

###### References: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html

Go to AWS Console -> CloudFormatoin -> Create Stack -> Choose **_Upload a template to Amazon s3_** (upload your template .yaml file) -> Stack Name "TestingIntrinsicFunctions" -> self service :-)

Go to EC2 services and check AMI ID & Tags to validate.

## Multiple Resources:

###### Resources
The required Resources section declares the AWS resources that you want to include in the stack, such as an Amazon EC2 instance or an Amazon S3 bucket.

###### Multiple Resources: 
CFN comes to life with more resources. This section will illustrate on how to combine multiple resources and create in correct/specific order of creation.

###### MultipleResources.yaml

```
Resources:
  Ec2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId: ami-01da99628f381e50a # Amazon Linux AMI in Singapore
      Tags:
        - Key: "Name"
          Value: !Join [ " ", [ EC2, Instance, with, Fn, Join ] ]
      SecurityGroups: 
        - !Ref MySecurityGroup
  MySecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0
```

Go to AWS Console -> CloudFormatoin -> Create Stack -> Choose **_Upload a template to Amazon s3_** (upload your template .yaml file) -> Stack Name "TestingMultipleResources" -> self service :-)

Go to EC2 services & Secuirty Groyp to check AMI ID, Tags & SG to validate.

## Pseudo Parameters:

Pseudo parameters are parameters that are predefined by AWS CloudFormation. You do not declare them in your template. Use them the same way as you would a parameter, as the argument for the Ref function.

**_Example_**: The value of the AWS::Region pseudo parameter to an output value.

```
Outputs:
  MyStacksRegion:
    Value: !Ref "AWS::Region"
```

```AWS::AccountId``` Returns the AWS account ID of the account in which the stack is being created, such as 12345678245.

###### References: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html

PseudoParameters.yaml

```
Resources:
  Ec2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      InstanceType: t2.micro
      ImageId: ami-01da99628f381e50a # Amazon Linux AMI in Singapore
      SecurityGroups: 
        - !Ref MySecurityGroup
      Tags:
        - Key: "Name"
          Value: !Join 
            - ""
            - - "EC2 Instance for "
              - !Ref AWS::Region
  MySecurityGroup:
    Type: "AWS::EC2::SecurityGroup"
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: "22"
          ToPort: "22"
          CidrIp: 0.0.0.0/0
```

Go to AWS Console -> CloudFormatoin -> Create Stack -> Choose **_Upload a template to Amazon s3_** (upload your template .yaml file) -> Stack Name "TestingPseudoParameters" -> self service :-)

Go to EC2 services check Tags to validate.

## Mappings
The optional Mappings section matches a key to a corresponding set of named values. You use the Fn::FindInMap intrinsic function to retrieve values in a map.

You cannot include parameters, pseudo parameters, or intrinsic functions in the Mappings section.

###### Mappings.yaml

```
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-1853ac65
    us-west-1:
      AMI: ami-bf5540df
    eu-west-1:
      AMI: ami-3bfab942
    ap-southeast-1:
      AMI: ami-e2adf99e
    ap-southeast-2:
      AMI: ami-43874721
Resources:
  Ec2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId:
        Fn::FindInMap:
        - RegionMap
        - !Ref AWS::Region
        - AMI
      SecurityGroups: 
        - !Ref MySecurityGroup
      Tags:
        - Key: "Name"
          Value: !Join 
            - ""
            - - "EC2 Instance for "
              - !Ref AWS::Region
  MySecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
         CidrIp: 0.0.0.0/0
```

###### References: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/mappings-section-structure.html

