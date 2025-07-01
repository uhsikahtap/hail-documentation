# Configuration Reference


Configuration variables can be set for Hail Query by:

  1. passing them as keyword arguments to ```init()```,

  2. running a command of the form `hailctl config set <VARIABLE_NAME> <VARIABLE_VALUE>` from the command line, or

  3. setting them as shell environment variables by running a command of the form `export <VARIABLE_NAME>=<VARIABLE_VALUE>` in a terminal, which will set the variable for the current terminal session.

Each method for setting configuration variables listed above overrides
variables set by any and all methods below it. For example, setting a
configuration variable by passing it to [`init()`](api.html#hail.init
"hail.init") will override any values set for the variable using either
`hailctl` or shell environment variables.

Warning

Some environment variables are shared between Hail Query and Hail Batch.
Setting one of these variables via ```init()```,
`hailctl`, or environment variables will affect both Query and Batch. However,
when instantiating a class specific to one of the two, passing configuration
to that class will not affect the other. For example, if one value for
`gcs_bucket_allow_list` is passed to [`init()`](api.html#hail.init
"hail.init"), a different value may be passed to the constructor for Batch’s
`ServiceBackend`, which will only affect that instance of the class (which can
only be used within Batch), and won’t affect Query.

## Supported Configuration Variables

GCS Bucket Allowlist Keyword Argument Name | `gcs_bucket_allow_list`  
---|---  
Keyword Argument Format | `["bucket1", "bucket2"]`  
`hailctl` Variable Name | `gcs/bucket_allow_list`  
Environment Variable Name | `HAIL_GCS_BUCKET_ALLOW_LIST`  
`hailctl` and Environment Variable Format | `bucket1,bucket2`  
Effect | Prevents Hail Query from erroring if the default storage policy for any of the given buckets is to use cold storage. Note: Only the default storage policy for the bucket is checked; individual objects in a bucket may be configured to use cold storage, even if the bucket is not. In the case of public access GCP buckets where the user does not have the appropriate permissions to check the default storage class of the bucket, the first object encountered in the bucket will have its storage class checked, and this will be assumed to be the default storage policy of the bucket.  
Shared between Query and Batch | Yes

---

