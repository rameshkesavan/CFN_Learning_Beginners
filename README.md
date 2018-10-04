# CFN_Learning_Beginners
   
# Purpose: 
This repository contains a collaboration of Amazon Web Services CloudFormation Template Examples. To give a glimpse of CFN for beginners and progressively deep dive into CFN world of core automation.  

AWS CloudFormation provides a common language for you to describe and provision all the infrastructure resources in your cloud environment.

# Cloud formation in AWS
CloudFormation has two parts: templates and stacks. A template is a JavaScript Object Notation (JSON) or Yet Another Markup Language (YAML) text file. The file, which is declarative and not scripted, defines what AWS resources or non-AWS resources are required to run the application.

# YAML Reference: https://aws.amazon.com/blogs/mt/the-virtues-of-yaml-cloudformation-and-using-cloudformation-designer-to-convert-json-to-yaml/

# What is AWS CFN Template?
To provision and configure your stack resources, you must understand AWS CloudFormation templates, which are formatted text files in JSON or YAML. *It's like a blue print*. 

# Template Anatomy:

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
  set of outputs

# Reference Link: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html

# Working with Templates: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-formats.html

# Resources Template Reference: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html

# What is AWS CFN Stack?
A stack is a collection of AWS resources that you can manage as a single unit. In other words, you can create, update, or delete a collection of resources by creating, updating, or deleting stacks. All the resources in a stack are defined by the stack's AWS CloudFormation template.
