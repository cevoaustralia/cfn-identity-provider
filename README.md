# A simple SAML Identity Provider (IdP) provisioner

This CloudFormation template creates a SAML identity provider in Amazon Web Services' (AWS) Identity and Access
Management (IAM) configuration.

In order to use it, you'll need:

* an AWS account
* rights within that AWS account to create, update, and delete:
    * CloudFormation stacks
    * IAM Roles and Policies
    * Lambda functions
    * Identity Providers
* a SAML Identity Provider (IdP)
* the Federation metadata (an XML document) from the Identity Provider (how to get this differs for every IdP)

## Preparing metadata

1. Download metadata from your IdP
1. Make it all be on one line and escape the double-quote (`"`) character:
   `tr -d '\n' metadata.xml | sed -e 's/"/\"/g' > out.xml`
1. Copy the `out.xml` into the `ParameterValue` field of the `params.json`

## Configuration

You can set the name of your identity provider via the `SamlProviderName` parameter to the stack; this can be
configured in the `params.json`. It defaults to `MyProvider`

## Returned Values from the Custom Resource

The `ProviderCreator` custom resource returns an `Arn` which is the ARN of the Identity Provider.

You can use the returned values from the custom resource via the `Fn::GetAtt` or `!GetAtt` intrinsic functions
of CloudFormation.

For example:

```yaml
...

  TrustingIdp:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Federated: !GetAtt IdentityProvider.Arn
            Action: sts:AssumeRoleWithSAML
            Condition:
              StringEquals:
                "SAML:aud": "https://signin.aws.amazon.com/saml"
...
```

## Updating the stack

The stack can be updated, though the only changes you can make to the Identity Provider is to change the SAML
Metadata document, in case you need to update the trust relationship.
