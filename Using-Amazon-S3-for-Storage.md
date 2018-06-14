You need to use this Amazon S3 policy (replace both "YOURBUCKETHERE" with the S3 bucket you make/have for this app):

```javascript
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:*"
            ],
            "Resource": [
                "arn:aws:s3:::YOURBUCKETHERE",
                "arn:aws:s3:::YOURBUCKETHERE/*"
            ]
        }
    ]
}
```

For your Amazon IAM User (in Security Credential's admin dashboard, create a custom policy first then create a new user, select it and attach that policy to it) that is associated with that user's Access Key and Secret Access Key from when you initialize GUN on the server:

```javascript
var gun = Gun({
  file: 'data.json', // local testing and development
  s3: {
    key: process.env.AWS_ACCESS_KEY_ID, // AWS Access Key
    secret: process.env.AWS_SECRET_ACCESS_KEY, // AWS Secret Token
    bucket: process.env.AWS_S3_BUCKET // The bucket you want to save into
  }
});
```
NOTE: The `key`, `secret`, and `bucket` params will not accept just any string values. The Gun S3 adaptor expects those three explicit `process.env.*` environment variables listed above, or S3 will fail silently.

If you are using Heroku, this is pretty easy. In their dashboard go and edit your app in the Settings tab, and click to reveal and edit the Config Vars for environment variables, this will let you set something like `AWS_REGION` to `us-east-1`.

> Note: `AWS-SDK` devDependencies gets removed by Heroku after build, which may cause your app to crash. You will also need to set `NPM_CONFIG_PRODUCTION` to `false` to to fix this.

If you are running your own server, you probably have an upstart script or something similar that monitors your app and starts or restarts it if it crashes. You can set ENVIRONMENT VARIABLES there, exactly how depends upon your setup so please google around for that - when we have a good one, we'll update it here.