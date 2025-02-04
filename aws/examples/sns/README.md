# Work with SNS via terraform

A terraform module for making SNS.


## Usage
----------------------
Import the module and retrieve with ```terraform get``` or ```terraform get --update```. Adding a module resource to your template, e.g. `main.tf`:

```
#
# MAINTAINER Vitaliy Natarov "vitaliy.natarov@yahoo.com"
#
terraform {
  required_version = "~> 1.0"
}

provider "aws" {
  region                  = "us-east-1"
  shared_credentials_file = pathexpand("~/.aws/credentials")
}

module "sns" {
  source      = "../../modules/sns"
  name        = "TEST"
  environment = "stage"

  # SNS topic
  enable_sns_topic          = true
  sns_topic_name            = "sns-test-stage"
  sns_topic_delivery_policy = file("./policies/sns_topic_delivery_policy_document.json.tpl")

  # SNS topic policy
  enable_sns_topic_policy    = true
  sns_topic_policy_topic_arn = ""
  sns_topic_policy_policy    = ""

  # SNS topic subscription
  enable_sns_topic_subscription = true

  sns_topic_subscription_sns_protocol  = "sqs"
  sns_topic_subscription_sns_endpoints = ["arn:aws:sqs:us-east-1:XXXXXXXXXXXXXXXX:my_sqs"]

  #
  enable_sns_platform_application = false
  sns_platform_application_name   = "test"

  tags = tomap({
    "Environment"   = "dev",
    "Createdby"     = "Vitaliy Natarov",
    "Orchestration" = "Terraform"
  })
}

```

## Module Input Variables
----------------------
- `name` - Name to be used on all resources as prefix (`default = TEST`)
- `environment` - Environment for service (`default = STAGE`)
- `tags` - A list of tag blocks. Each element should have keys named key, value, etc. (`default = {}`)
- `enable_sns_topic` - Enable SNS topic usage (`default = False`)
- `sns_topic_name` - The friendly name for the SNS topic. By default generated by Terraform. (`default = ""`)
- `sns_topic_name_prefix` - (Optional) The friendly name for the SNS topic. Conflicts with sns_topic_name. (`default = ""`)
- `sns_topic_display_name` - (Optional) The display name for the SNS topic (`default = ""`)
- `sns_topic_delivery_policy` - (Optional) The SNS delivery policy. (`default = ""`)
- `sns_topic_policy` - (Optional) The fully-formed AWS policy as JSON. For more information about building AWS IAM policy documents with Terraform (`default = null`)
- `sns_application_success_feedback_role_arn` - (Optional) The IAM role permitted to receive success feedback for this topic (`default = null`)
- `sns_application_success_feedback_sample_rate` - (Optional) Percentage of success to sample (`default = null`)
- `sns_application_failure_feedback_role_arn` - (Optional) IAM role for failure feedback (`default = null`)
- `sns_topic_http_success_feedback_role_arn` - (Optional) The IAM role permitted to receive success feedback for this topic (`default = null`)
- `sns_topic_http_success_feedback_sample_rate` - (Optional) Percentage of success to sample (`default = null`)
- `sns_topic_http_failure_feedback_role_arn` - (Optional) IAM role for failure feedback (`default = null`)
- `sns_topic_kms_master_key_id` - (Optional) The ID of an AWS-managed customer master key (CMK) for Amazon SNS or a custom CMK. (`default = null`)
- `sns_topic_lambda_success_feedback_role_arn` - (Optional) The IAM role permitted to receive success feedback for this topic (`default = null`)
- `sns_topic_lambda_success_feedback_sample_rate` - (Optional) Percentage of success to sample (`default = null`)
- `sns_topic_lambda_failure_feedback_role_arn` - (Optional) IAM role for failure feedback (`default = null`)
- `sns_topic_sqs_success_feedback_role_arn` - description (`default = null`)
- `sns_topic_sqs_success_feedback_sample_rate` - description (`default = null`)
- `sns_topic_sqs_failure_feedback_role_arn` - (Optional) IAM role for failure feedback (`default = null`)
- `enable_sns_topic_policy` - Enable sns topic policy usage (`default = False`)
- `sns_topic_policy_policy` - (Required) The fully-formed AWS policy as JSON. For more information about building AWS IAM policy documents with Terraform, see the AWS IAM Policy Document Guide. (`default = ""`)
- `sns_topic_policy_topic_arn` - The ARN of the SNS topic (`default = null`)
- `enable_sns_topic_subscription` - Enable sns topic subscription usage (`default = False`)
- `sns_topic_subscription_topic_arn` - (Required) The ARN of the SNS topic to subscribe to (`default = ""`)
- `sns_topic_subscription_sns_protocol` - The protocol to use. The possible values for this are: sqs, sms, lambda, application. (http or https are partially supported, see below) (email is option but unsupported). (`default = sqs`)
- `sns_topic_subscription_sns_endpoints` - The list of endpoints to send data to, the contents will vary with the protocol. (`default = []`)
- `sns_topic_subscription_confirmation_timeout_in_minutes` - Set timeout in minutes. Integer indicating number of minutes to wait in retying mode for fetching subscription arn before marking it as failure. Only applicable for http and https protocols (default is 1 minute). (`default = 1`)
- `sns_topic_subscription_endpoint_auto_confirms` - Enable endpoint auto confirms. Boolean indicating whether the end point is capable of auto confirming subscription e.g., PagerDuty (default is false) (`default = False`)
- `sns_topic_subscription_raw_message_delivery` - Set raw message delivery.Boolean indicating whether or not to enable raw message delivery (the original message is directly passed, not wrapped in JSON with the original message in the message property) (default is false). (`default = False`)
- `sns_topic_subscription_filter_policy` - (Optional) JSON String with the filter policy that will be used in the subscription to filter messages seen by the target resource. (`default = ""`)
- `sns_topic_subscription_delivery_policy` - (Optional) JSON String with the delivery policy (retries, backoff, etc.) that will be used in the subscription - this only applies to HTTP/S subscriptions. Refer to the SNS docs for more details. (`default = ""`)
- `sns_topic_application_success_feedback_role_arn` - (Optional) The IAM role permitted to receive success feedback for this topic (`default = null`)
- `sns_topic_application_success_feedback_sample_rate` - (Optional) Percentage of success to sample (`default = null`)
- `sns_topic_application_failure_feedback_role_arn` - (Optional) IAM role for failure feedback (`default = null`)
- `enable_sns_sms_preferences` - Enable sns sms preferences usage (`default = False`)
- `sns_sms_preferences_monthly_spend_limit` - (Optional) The maximum amount in USD that you are willing to spend each month to send SMS messages. (`default = null`)
- `sns_sms_preferences_delivery_status_iam_role_arn` - (Optional) The ARN of the IAM role that allows Amazon SNS to write logs about SMS deliveries in CloudWatch Logs. (`default = null`)
- `sns_sms_preferences_delivery_status_success_sampling_rate` - (Optional) The percentage of successful SMS deliveries for which Amazon SNS will write logs in CloudWatch Logs. The value must be between 0 and 100. (`default = null`)
- `sns_sms_preferences_default_sender_id` - (Optional) A string, such as your business brand, that is displayed as the sender on the receiving device. (`default = null`)
- `sns_sms_preferences_default_sms_type` - (Optional) The type of SMS message that you will send by default. Possible values are: Promotional, Transactional (`default = null`)
- `sns_sms_preferences_usage_report_s3_bucket` - (Optional) The name of the Amazon S3 bucket to receive daily SMS usage reports from Amazon SNS. (`default = null`)
- `enable_sns_platform_application` - Enable sns platform application usage (`default = False`)
- `sns_platform_application_name` - The friendly name for the SNS platform application (`default = ""`)
- `sns_platform_application_platform` - (Required) The platform that the app is registered with. (`default = ""`)
- `sns_platform_application_platform_credential` - (Required) Application Platform credential.  (`default = ""`)
- `sns_platform_application_event_delivery_failure_topic_arn` - (Optional) SNS Topic triggered when a delivery to any of the platform endpoints associated with your platform application encounters a permanent failure. (`default = null`)
- `sns_platform_application_event_endpoint_created_topic_arn` - (Optional) SNS Topic triggered when a new platform endpoint is added to your platform application. (`default = null`)
- `sns_platform_application_event_endpoint_deleted_topic_arn` - (Optional) SNS Topic triggered when an existing platform endpoint is deleted from your platform application. (`default = null`)
- `sns_platform_application_event_endpoint_updated_topic_arn` - (Optional) SNS Topic triggered when an existing platform endpoint is changed from your platform application. (`default = null`)
- `sns_platform_application_failure_feedback_role_arn` - (Optional) The IAM role permitted to receive failure feedback for this application. (`default = null`)
- `sns_platform_application_platform_principal` - (Optional) Application Platform principal. (`default = null`)
- `sns_platform_application_success_feedback_role_arn` - (Optional) The IAM role permitted to receive success feedback for this application. (`default = null`)
- `sns_platform_application_success_feedback_sample_rate` - (Optional) The percentage of success to sample (0-100) (`default = null`)

## Module Output Variables
----------------------
- `sns_topic_id` - The ARN of the SNS topic
- `sns_topic_arn` - The ARN of the SNS topic, as a more obvious property (clone of id)
- `sns_topic_policy_id` - Get SNS topic policy ID
- `sns_topic_subscription_id` - The ARN of the subscription
- `sns_topic_subscription_arn` - The ARN of the subscription stored as a more user-friendly property
- `sns_topic_subscription_topic_arn` - The ARN of the topic the subscription belongs to
- `sns_topic_subscription_protocol` - The protocol being used
- `sns_topic_subscription_endpoint` - The full endpoint to send data to (SQS ARN, HTTP(S) URL, Application ARN, SMS number, etc.)


## Authors

Created and maintained by [Vitaliy Natarov](https://github.com/SebastianUA). An email: [vitaliy.natarov@yahoo.com](vitaliy.natarov@yahoo.com).

## License

Apache 2 Licensed. See [LICENSE](https://github.com/SebastianUA/terraform/blob/master/LICENSE) for full details.
