> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../documentation/quickstart.md) and the [Model APIs overview](../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Using fal within an n8n workflow

> This guide will demonstrate, step-by-step, how to use fal within an n8n workflow.

## Prerequisites

* An n8n account ([https://n8n.io/](https://n8n.io/))
* A fal account ([https://fal.ai/dashboard](https://fal.ai/dashboard))
* A fal API key (generated from your account dashboard)

## Workflow Overview

This n8n workflow consists of three main HTTP requests:

**1. Submit Request**

Send a POST request to initiate content generation

**2. Check Status**

Poll the status of your request using GET

**3. Retrieve Result**

Fetch the final generated content

## Step 1: Create Your Workflow

**Step 1**

In n8n, create a new workflow

**Step 2**

Start with a **Manual Trigger** node to initiate the workflow manually

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/01.webp?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=86505fc90245b94c09161f2a1762d388)

## Step 2: Submit Request (POST)

### Add HTTP Request Node

**Step 1**

Add an **HTTP Request** node after your trigger

**Step 2**

Set the **Method** to `POST`

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/02.webp?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=cd7de677bed6fd24446726e7fdfd5999)

### Configure the URL

**Step 1**

Navigate to [fal.ai](https://fal.ai/dashboard) and select your desired model (e.g., `fal-ai/veo3/fast`)

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/03.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=3f36e5adf0e0359186dc049e83546101)

**Step 2**

Click on the **API** tab, select "HTTP (cURL)" and "Submit a request". Copy and save the URL and data JSON as those will be needed for later.

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/04.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=c9b0c680a03dd7291a1457743c8ebb62)

**Step 3**

Copy the URL (e.g., `https://queue.fal.run/fal-ai/veo3/fast`) and paste it into the URL field in n8n

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/05.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=c1d3192e62f6b9d6868d2ae32e4004dd)

### Set Up Authentication

**Step 1**

Navigate to [fal.ai API Keys](https://fal.ai/dashboard/keys), create a new key and copy its value.

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/06.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=d2104fe703ca052d8123a24e5f2a85df)

**Step 2**

Back in n8n, in the **Authentication** section, select **Generic Credential Type**

**Step 3**

Choose **Header Auth** from the dropdown

**Step 4**

Click **+ Create new credential**

**Step 5**

Set:

* **Name**: `Authorization`
* **Value**: `Key YOUR_FAL_KEY`

**Step 6**

Save the credential and close

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/07.webp?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=7dc459c9cb98b714d5faa0e7950ae1dc)

### Configure Request Body

**Step 1**

Toggle **Send Body** to `ON`

**Step 2**

Set **Body Content Type** to `JSON`

**Step 3**

Choose **Specify Body** as `USING JSON`

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/08.webp?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=5fc79b5a0908e101aafa7fd35b5d476f)

**Step 4**

In fal, go again to the model page, select **JSON** from dropdown and copy the payload

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/09.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=6e0c82d5d7c580e9aa49ec9e8ea66ada)

**Step 5**

Copy the JSON payload and paste it into n8n's JSON text box

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/10.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=ad419e143400976fa0a08921b391c968)

### Execute the Node

**Step 1**

Click **Execute Node** to test the request. You should receive a response with a request ID.

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/11.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=c8cdd5bb3f4083774e498441e820dbd9)

## Step 3: Check Status (GET)

### Add Second HTTP Request Node

**Step 1**

Click on the first HTTP Request node and add another **HTTP Request** node

**Step 2**

Set the **Method** to `GET`

### Configure Status Check URL

**Step 1**

In fal, go to your model's **API** section and find the **GET** URL for status checking

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/12.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=d8963881f4ac744d8e40644115f0aeaa)

**Step 2**

Copy this URL and paste it into the URL field

**Step 3**

You'll need to replace the request ID from the previous step on this URL, with `{{ $json.request_id }}`

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/13.webp?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=ded90911ad1c72b9f3dd21e580dfdf63)

### Set Authentication

**Step 1**

Use the same **Header Auth** credential created earlier

**Step 2**

Select your existing **Authorization** credential

### Execute the Node

1. This will check the status of your generation request.

![](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/n8n/14.webp?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=80f6186759e48b38a2512042c4fa2c79)

## Step 4: Retrieve Result (GET)

### Add Third HTTP Request Node

1. Add a final **HTTP Request** node
2. Set the **Method** to `GET`

### Configure Result URL

1. Use the result URL from the status response. Set the URL to `{{ $json.response_url }}`.

### Set Authentication

1. Use the same **Header Auth** credential

### Execute the Node

This will retrieve your final generated content.

## Testing Your Workflow

**Step 1**

Save your workflow

**Step 2**

Click **Execute Workflow** to run the complete process

**Step 3**

Monitor each node to ensure successful execution

**Step 4**

Check the final node output for your generated content

## Best Practices

* **Error Handling**: Add error handling nodes to manage failed requests
* **Delays**: Consider adding **Wait** nodes between status checks to avoid overwhelming the API
* **Conditional Logic**: Use **IF** nodes to check status before proceeding to result retrieval
* **Data Transformation**: Use **Set** nodes to extract and format specific data from responses

## Troubleshooting

* **401 Unauthorized**: Check that your API key is correctly set in the authentication header
* **Request ID Missing**: Ensure the first POST request completed successfully and returned a request ID
* **Status Still Processing**: Add appropriate delays between status checks
* **Invalid JSON**: Verify your JSON payload matches the model's expected format

## Next Steps

Once you have a working workflow, you can:

* Connect it to external triggers (webhooks, schedules, etc.)
* Integrate with other services in your n8n workflow
* Add data processing and transformation steps
* Set up notifications for completed generations
