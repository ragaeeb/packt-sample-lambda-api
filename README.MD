This app was built using the video [Building A Scalable Serverless Application on AWS by James Cross, October 2017](https://subscription.packtpub.com/video/virtualization_and_cloud/9781788476065).

## Setting up identity

Setup steps after `npm install`

1. Go to AWS Console.
2. Search for `IAM`.
3. Go to `Users` on the left.
4. Click on `Add User`.
5. Enter a username (ie: `serverless-admin`).
6. Select `Programmatic Access` for `Access Type`.
7. Click `Next: Permissions`.
8. Click `Create Group` (if you don't already have an admin group).
9. Type `AdminGroup`, and select `Administrator Access`, and select `Create Group`.
10. Click `Next: Tags`, and then `Next: Review`, and eventually `Create user`.
11. Click on the `serverless-admin` user that was just created.
12. Go to `Security Credentials`.
13. Click on `Create access key`.
14. Make note of the `Access key ID` as well as the `Secret access key`.
15. Run `npm run start -- -key <YOUR_KEY_FROM_STEP_14> --secret <YOUR_SECRET_FROM_STEP_14> -o`.

## Set up S3 bucket

1. Go to `S3` in the `Services` tab in AWS.
2. Create a new bucket with a unique name.
3. Make sure you update `handler.js` in the following section of the code with your new bucket name:

```
  const getParams = {
    Bucket: "my-pdfs", // your bucket name,
    Key: "packt-test-file.txt", // path to the object you're looking for
  };
```

1. Drop a `packt-text-file.txt` in the `my-pdfs` folder in the S3 bucket.

## Running locally

1. Run `npm run dev` from the terminal. It should display contents.

## Deploying to AWS

1. Run `npm run deploy`.
   Note that the `-v` in the `package.json` is for `verbose`.
2. Once deployed, take the `ServiceEndpoint` URL that was generated, and then paste it into your browser.
3. Also be sure to note the region the app is hosted in (ie: `api.eu-west-1`). To see your app listed in AWS, you will need to switch to that region.
4. `https://x.execute-api.eu-west-1.amazonaws.com/dev/reads3` and `https://x.execute-api.eu-west-1.amazonaws.com/dev/hello/ragaeeb` should both work.

## Monitor logs

1. Search for `Cloudwatch` in AWS.
2. On the left side choose `Logs` and `Log Groups`. You will be able to see all the accessed logs here.

## Performance Tuning

1. If the lambda is running slowly in the logs (ie: each run takes `1 second`), increase the CPU.
2. In `Services` go to `Functions`, and open the `packt-sample-lambda-api-dev-reads3` function.
3. In the `Basic Settings`, maximize the memory to the highest value and `Save` it.
4. Now with enough hits, the cache will get hit and thus it'll be much faster.

## Getting API Gateway URL

1. Make sure you're on `eu-west-1` in AWS.
2. `Services` -> `API Gateway`.
3. Click `dev-packt-sample-lambda-api`.
4. Click on `Stages` on the left.
5. Click on `dev`.
6. Note the `Invoke URL`.

## Testing Database Reads/Writes

1. Make sure you're on `eu-west-1` in AWS.
2. `Services` -> `DynamoDB`.
3. `Tables`.
4. `packt-authors-table`.
5. If you click on `Items`, it should be empty.
6. Now go to the Invoke URL you copied from last section's #6.
7. Do a query like this: `https://x.execute-api.eu-west-1.amazonaws.com/dev/getauthors/mail@abc.com` where `mail@abc.com` is some query value. This should return an empty array.
8. Create an author using CURL like this in the terminal:

```
curl -X POST https://x.execute-api.eu-west-1.amazonaws.com/dev/createauthor -d '{ "email_address": "mail@abc.com", "first_name": "Kakuma", "last_name": "Hurmuzi", "course_title": "Bootcamp" }'
```

It should return you:

```
{"email_address":"mail@abc.com","first_name":"Kakuma","last_name":"Hurmuzi","course_title":"Bootcamp"}
```

9. If you go back to `DynamoDB`, it should now show that item in the `Items`.
