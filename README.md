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

## Intrinstic functions:

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

## Mappings:
The optional Mappings section matches a key to a corresponding set of named values. You use the Fn::FindInMap intrinsic function to retrieve values in a map.

You cannot include parameters, pseudo parameters, or intrinsic functions in the Mappings section.

###### Mappings.yaml

```
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-04681a1dbd79675a5 
    ap-southeast-1:
      AMI: ami-01da99628f381e50a
    ap-southeast-2:
      AMI: ami-00e17d1165b9dd3ec
    eu-west-2:
      ami-e1768386
    eu-central-1:
      AMI: ami-0f5dbc86dd9cbf7a8
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

Go to AWS Console -> CloudFormatoin -> Create Stack -> Choose **_Upload a template to Amazon s3_** (upload your template .yaml file) -> Stack Name "TestingMappings" -> self service :-)

Go to EC2 services check Tags to validate.

## Input Parameters:

Use the optional Parameters section to customize your templates. Parameters enable you to input custom values to your template each time you create or update a stack.

Input Parameters enable us to input custom values to our template. They are defined withing the top level parameters section. Each parameter must be assigned a value at runtime. You can optionally specify a default value.

The only required attribute is **_Type_** which is the data type.

**_Support Parameter Types_**:
```
	String
	Number
	List<Number>
	CommaDelimitedList
	AWS-specific types (AWS::EC2::Image::Id)
	Systems Manager Parameter types
```

###### InputParameters.yaml

```
Parameters:
  NameOfService:
    Description: "The name of the service this stack is to be used for."
    Type: String
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access into the server
    Type: AWS::EC2::KeyPair::KeyName
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-04681a1dbd79675a5 
    ap-southeast-1:
      AMI: ami-01da99628f381e50a
    ap-southeast-2:
      AMI: ami-00e17d1165b9dd3ec
    eu-west-2:
      ami-e1768386
    eu-central-1:
      AMI: ami-0f5dbc86dd9cbf7a8
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
          Value: !Ref NameOfService
      KeyName: !Ref KeyName
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

###### References: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html

Go to AWS Console -> CloudFormatoin -> Create Stack -> Choose **_Upload a template to Amazon s3_** (upload your template .yaml file) -> Stack Name "TestingInputParameters" -> self service :-)

Go to EC2 services check Description & Tags to validate.

## Outputs:

The Outputs section declares output values that you can import into other stacks (to create cross-stack references), return in response (to describe stack calls), or view on the AWS CloudFormation console. 

Outputs enable us to get access to information about resources within a stack. **_For Example _** Create an EC2 Instance and output the Public IP or DNS.

```
Outputs:
 InstanceDns:
   Description:
	The Instance Dns
   Value:	
     !GetAtt
	- EC@Instance
	- PublicDnsName
```

###### References: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html

###### Output.yaml

```
Parameters:
  NameOfService:
    Description: "The name of the service this stack is to be used for."
    Type: String
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access into the server
    Type: AWS::EC2::KeyPair::KeyName
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-04681a1dbd79675a5 
    ap-southeast-1:
      AMI: ami-01da99628f381e50a
    ap-southeast-2:
      AMI: ami-00e17d1165b9dd3ec
    eu-west-2:
      ami-e1768386
    eu-central-1:
      AMI: ami-0f5dbc86dd9cbf7a8
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
          Value: !Ref NameOfService
      KeyName: !Ref KeyName
  MySecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0
Outputs:
  ServerDns:
    Value: !GetAtt
      - Ec2Instance
      - PublicDnsName
```

Go to AWS Console -> CloudFormatoin -> Outputs -> you should see the Server Public DNS Name.

## Platform (EC2) and Application Setup:

Downloading and installing application.
Downloading and installing dependencies/packages.
Configuration.
Creating Users/Groups.
Starting Services.

## Baking Instance with application needs **_UserData_** property section updated.

UserData is Base64 encoded.
**Only** runs on the first boot cycle.
Run as root, not run interactively & logs output to /var/log/cloud-init-output.log

```
#!/bin/bash
yum update -y
yum install -y httpd
service httpd start
```

```
Resources:
 EC2Instance:
   Type: AWS::EC2::Instance
   Properties:
     UserData:
       !Base64 |
         #!/bin/bash
	 yum update -y
	 yum install -y httpd
	 service httpd start 
```

## Helper Scripts:

AWS CloudFormation provides Python helper scripts that you can use to install software and start services on an Amazon EC2 instance that you create as part of your stack

**cfn-init**: Use to retrieve and interpret resource metadata, install packages, create files, and start services.

**cfn-signal**: Use to signal with a CreationPolicy or WaitCondition, so you can synchronize other resources in the stack when the prerequisite resource or application is ready.

**cfn-get-metadata**: Use to retrieve metadata for a resource or path to a specific key.

**cfn-hup**: Use to check for updates to metadata and execute custom hooks when changes are detected.

```
Procedural scripting is not ideal.
CFN optimise this using helper scripts.
Preinstalled on Amazon Linux AMI.

yum install -y aws-cfn-bootstrap
```

###### References: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-helper-scripts-reference.html

## CFN Init:

The cfn-init helper script enables us to use: AWS::CloudFormation::Init

Single Config Key: CFN Init script calls **_Config_**

```
Resource:
  EC2Instance:
    Type:"AWS::EC2::Instance"
    Metadata:
      AWS::CloudFormation::Init:
	config:
	  packages:
	  groups:
	  users:
	  sources:
   	    files
	  commands:
	  services:
	 Properties:
```

Multiple Config Keys: CFN Init script calls **_Configsets_**

Configsets contain a list of config keys, in a desired execution order.

```
AWS::CloudFormation:Init:
  configSets:
    webphp:
      - "installphp"
      - "installweb"
```

```
Parameters:
  NameOfService:
    Description: "The name of the service this stack is to be used for."
    Type: String
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access into the server
    Type: AWS::EC2::KeyPair::KeyName
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-04681a1dbd79675a5 
    ap-southeast-1:
      AMI: ami-01da99628f381e50a
    ap-southeast-2:
      AMI: ami-00e17d1165b9dd3ec
    eu-west-2:
      ami-e1768386
    eu-central-1:
      AMI: ami-0f5dbc86dd9cbf7a8
Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Metadata: 
      AWS::CloudFormation::Init:
        config: 
          packages: 
            yum:
              httpd: []
              php: []
          files: 
            /var/www/html/index.php:
              content: !Sub |
                <?php print "Hello New Company"; ?>
          services: 
            sysvinit:
              httpd:
                enabled: true
                ensureRunning: true
    Properties:
      InstanceType: t2.micro
      ImageId:
        Fn::FindInMap:
        - RegionMap
        - !Ref AWS::Region
        - AMI
      SecurityGroupIds:
        - !Ref MySecurityGroup
      Tags:
        - Key: Name
          Value: !Ref NameOfService
      KeyName: !Ref KeyName
      UserData:
        'Fn::Base64': 
          !Sub |
            #!/bin/bash -xe            
            # Ensure AWS CFN Bootstrap is the latest
            yum install -y aws-cfn-bootstrap
            # Install the files and packages from the metadata
            /opt/aws/bin/cfn-init -v --stack ${AWS::StackName} --resource EC2Instance  --region ${AWS::Region}
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Open Ports 22 and 80
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: '22'
        ToPort: '22'
        CidrIp: 0.0.0.0/0
      - IpProtocol: tcp
        FromPort: '80'
        ToPort: '80'
        CidrIp: 0.0.0.0/0
Outputs:
  Website:
    Description: The Public DNS for the EC2 Instance
    Value: !Sub 'http://${EC2Instance.PublicDnsName}'
```






























































































































































































































# Continues.......
