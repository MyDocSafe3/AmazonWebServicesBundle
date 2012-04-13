# Amazon Web Services Bundle #

This is a Symfony2 Bundle for interfacing with Amazon Web Services (AWS).

## AWS SDK for PHP ##

This bundle utilizes the [AWS SDK for PHP](http://github.com/amazonwebservices/aws-sdk-for-php) by loading the SDK and providing the means to instantiate the SDK's various web service objects, passing them back to you for direct use.

The AWS SDK for PHP is the the official Amazon-supported library for interfacing with with Amazon's Web Service offerings. As such, the bundle merely provides a means (via Dependency Injection) to get at the SDK's various web service objects. There is no additional functionality at present time.

Once objects have been created, you have full access to the SDK. Please see the [AWS SDK for PHP documentation](http://docs.amazonwebservices.com/AWSSDKforPHP/latest/) for a list of each service's API calls.

## Installation ##

AmazonWebServicesBundle may be installed via the deps file (Symfony 2.0.x) or via Composer (2.1+)

1a) Via deps file

Add the AWS SDK for PHP library and the Amazon Web Services Bundle to your project's deps file:

    [aws-sdk-for-php]
        git=http://github.com/amazonwebservices/aws-sdk-for-php.git

    [AmazonWebServicesBundle]
        git=http://github.com/Cybernox/AmazonWebServicesBundle.git
        target=/bundles/Cybernox/AmazonWebServicesBundle

1b) Via Composer

Add ```"cybernox/amazon-webservices-bundle": "*"``` to the ```require``` section of your composer.json file, then run ```composer.phar install```

As of right now, AWS SDK for PHP does not provide a Composer definition and is not registered with packagist.org, therefore you'll have to download it yourself:

```cd /your/project/root/directory/vendor```

```git clone https://github.com/amazonwebservices/aws-sdk-for-php.git```

2) Add AmazonWebServicesBundle to your application kernel:

    // app/AppKernel.php
    public function registerBundles()
    {
        return array(
            // ...
            new Cybernox\AmazonWebServicesBundle\CybernoxAmazonWebServicesBundle(),
            // ...
        );
    }

3) **_If you are using Composer (1b), you may skip this step_**

Register the Cybernox namespace:

    // app/autoload.php
    $loader->registerNamespaces(array(
        // ...
        'Cybernox' => __DIR__.'/../vendor/bundles',
        // ...
    ));

4) Run `bin/vendors install` to have Symfony download and install the packages

5) Set up your configuration.

5a) First configure your parameters.ini:

    // app/config/parameters.ini
    [parameters]
        ...

        ; Amazon Web Services Configuration. Found in the AWS Security Credentials.
        aws_key                        = YOUR_KEY
        aws_secret                     = YOUR_SECRET_KEY
        aws_account_id                 = YOUR_ACCOUNT_ID
        aws_canonical_id               = YOUR_CONONICAL_ID
        aws_canonical_name             = YOUR_CONONICAL_NAME
        aws_mfa_serial                 = YOUR_MFA_SERIAL
        aws_cloudfront_keypair_id      = YOUR_CLOUDFRONT_KEYPAIR_ID
        aws_cloudfront_private_key_pem = YOUR_CLOUDFRONT_PRIVATE_KEY_PEM

**Note, presently only aws_key and aws_secret are being used when constructing objects. Setting them is fine, but it won't do anything.**

5b) Set up your application configuration:

    // app/config/config.yml
    # Amazon Web Services Configuration
    cybernox_amazon_web_services:
        key:                        %aws_key%
        secret:                     %aws_secret%
        account_id:                 %aws_account_id%
        canonical_id:               %aws_canonical_id%
        canonical_name:             %aws_canonical_name%
        mfa_serial:                 %aws_mfa_serial%
        cloudfront_keypair_id:      %aws_cloudfront_keypair_id%
        cloudfront_private_key_pem: %aws_cloudfront_private_key_pem%
        default_cache_config:       apc
        enable_extensions:          false
        certificate_authority:      false

**Note, as in 5a) above, only the key and secret are presently being used, so it is safe to omit the rest if you wish.**

**You're welcome to skip setting the configurations in parameters.ini (5a) and set the values directly in config.yml, however, the setup shown here is following the example that Sensio used for Symfony with regards to database setup, and keeping the actual private configuration values in parameters.ini and utilizing them in config.yml.**

## Usage ##

Once installed, you simply need to request the appropriate service for the Amazon Web Service object you wish to use. The returned object will then allow you full access the the API for the requested service.

**Please see the [AWS SDK for PHP documentation](http://docs.amazonwebservices.com/AWSSDKforPHP/latest/) for a list of each service's API calls.**

In this example, we get an AmazonSQS object from the AWS SDK for PHP library by requesting the `aws_sqs` service. We then use that object to retrieve a message from an existing Amazon SQS queue.

```php

    // src/Acme/DemoBundle/Controller/YourController.php
    public function someAction()
    {
        // Get a AmazonSQS object
        $sqs = $this->container->get('aws_sqs');

        // Get a message from an existing queue
        $response = $sqs->receive_message($queueUrl);

        // Do stuff with the received message response object
        // ...
    }
```

### Available Services ###

The following services are available, each returning an object allowing access to the respective Amazon Web Service

_Please note, at this time, thorough testing has not been completed. Because this bundle merely creates object from the SDK and passes them though to you, there shouldn't be any issues. However, this message is here as warning, just in case. In the event that a bug exists within the bundle's service definitions (and not within the SDK itself), please let me know!_

<table>
  <tr>
    <th>Symfony Service Name</th>
    <th>AWS SDK for PHP Object</th>
    <th>Description</th>
  </tr>

  <tr>
    <td>aws_as</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonAS">AmazonAS</a></td>
    <td>Amazon Auto Scaling</td>
  </tr>

  <tr>
    <td>aws_cloud_formation</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonCloudFormation">AmazonCloudFormation</a></td>
    <td>Amazon Cloud Formation</td>
  </tr>

  <tr>
    <td>aws_cloud_front</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonCloudFront">AmazonCloudFront</a></td>
    <td>Amazon Cloud Front</td>
  </tr>

  <tr>
    <td>aws_cloud_watch</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonCloudWatch">AmazonCloudWatch</a></td>
    <td>Amazon Cloud Watch</td>
  </tr>

  <tr>
    <td>aws_dynamo_db</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonDynamoDB">AmazonDynamoDB</a></td>
    <td>Amazon DynamoDB</td>
  </tr>

  <tr>
    <td>aws_ec2</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonEC2">AmazonEC2</a></td>
    <td>Amazon Elastic Compute Cloud (EC2)</td>
  </tr>

  <tr>
    <td>aws_elb</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonELB">AmazonELB</a></td>
    <td>Amazon Elastic Load Balancing</td>
  </tr>

  <tr>
    <td>aws_emr</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonEMR">AmazonEMR</a></td>
    <td>Amazon Elastic MapReduce</td>
  </tr>

  <tr>
    <td>aws_elasti_cache</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonElastiCache">AmazonElastiCache</a></td>
    <td>Amazon ElastiCache</td>
  </tr>

  <tr>
    <td>aws_elastic_beanstalk</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonElasticBeanstalk">AmazonElasticBeanstalk</a></td>
    <td>Amazon Elastic Beanstalk</td>
  </tr>

  <tr>
    <td>aws_iam</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonIAM">AmazonIAM</a></td>
    <td>Amazon Identity and Access Management</td>
  </tr>

  <tr>
    <td>aws_import_export</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonImportExport">AmazonImportExport</a></td>
    <td>Amazon Import/Export</td>
  </tr>

  <tr>
    <td>aws_rds</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonRDS">AmazonRDS</a></td>
    <td>Amazon Relational Database Service</td>
  </tr>

  <tr>
    <td>aws_s3</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonS3">AmazonS3</a></td>
    <td>Amazon Simple Storage Service (S3)</td>
  </tr>

  <tr>
    <td>aws_sdb</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonSDB">AmazonSDB</a></td>
    <td>Amazon SimpleDB</td>
  </tr>

  <tr>
    <td>aws_ses</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonSES">AmazonSES</a></td>
    <td>Amazon Simple Email Service</td>
  </tr>

  <tr>
    <td>aws_sns</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonSNS">AmazonSNS</a></td>
    <td>Amazon Simple Notification Service</td>
  </tr>

  <tr>
    <td>aws_sqs</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonSQS">AmazonSQS</a></td>
    <td>Amazon Simple Queue Service</td>
  </tr>

  <tr>
    <td>aws_sts</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonSTS">AmazonSTS</a></td>
    <td>Amazon Security Token Service</td>
  </tr>

  <tr>
    <td>aws_swf</td>
    <td><a href="http://docs.amazonwebservices.com/AWSSDKforPHP/latest/#i=AmazonSWF">AmazonSWF</a></td>
    <td>Amazon Simple Workflow</td>
  </tr>
</table>