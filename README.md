# Functions Framework for Node.js

[![npm version](https://img.shields.io/npm/v/@openfunction/functions-framework.svg)](https://www.npmjs.com/package/@openfunction/functions-framework) [![npm downloads](https://img.shields.io/npm/dm/@openfunction/functions-framework.svg)](https://npmcharts.com/compare/@openfunction/functions-framework?minimal=true)

[![Node unit CI][ff_node_unit_img]][ff_node_unit_link] [![Node lint CI][ff_node_lint_img]][ff_node_lint_link] [![Node conformace CI][ff_node_conformance_img]][ff_node_conformance_link]

> This is OpenFunction's nodejs functions-framework forked from [GCP functions-framework-nodejs](https://github.com/GoogleCloudPlatform/functions-framework-nodejs)

An open source FaaS (Function as a Service) framework based on [Express](https://expressjs.com/)
 and [Restana](https://github.com/BackendStack21/restana) for writing portable sync and async Node.js functions.

The Functions Framework lets you write lightweight functions that run in many
different environments, including:

* [OpenFunction](https://github.com/OpenFunction/OpenFunction)
* [Knative](https://github.com/knative/)-based environments
* [Dapr](https://dapr.io/)-based environments
* [Google Cloud Functions](https://cloud.google.com/functions/)
* [Cloud Run](https://cloud.google.com/run/) and [Cloud Run for Anthos](https://cloud.google.com/anthos/run)
* Your local development machine

Generally speaking, the framework allows you to go from:

```js
/**
 * Send "Hello, World!"
 * @param req https://expressjs.com/en/api.html#req
 * @param res https://expressjs.com/en/api.html#res
 */
exports.helloWorld = (req, res) => {
  res.send('Hello, World!');
};
```

To:

```sh
curl http://my-url
# Output: Hello, World!
```

All without needing to worry about writing an HTTP server or complicated request handling logic.

> Watch [this video](https://youtu.be/yMEcyAkTliU?t=912) to learn more about the Node Functions Framework.

## Features

* Spin up a local development server for quick testing
* Invoke a function in response to a request
* Listen and respond to the events bridged from Dapr system
* Automatically unmarshal events conforming to the [CloudEvents](https://cloudevents.io/) spec
* Portable between serverless platforms

## Installation

Add the Functions Framework to your `package.json` file using `npm`.

```sh
npm install @openfunction/functions-framework
```

## Quickstarts

### Quickstart: "Hello, World" on your local machine

1. Create an `index.js` file with the following contents:

    ```js
    exports.helloWorld = (req, res) => {
      res.send('Hello, World');
    };
    ```

1. Run the following command:

    ```sh
    npx @openfunction/functions-framework --target=helloWorld
    ```

1. Open <http://localhost:8080/> in your browser and see _Hello, World_.

### Quickstart: Set up a new project

1. Create a `package.json` file using `npm init`:

    ```sh
    npm init
    ```

1. Create an `index.js` file with the following contents:

    ```js
    exports.helloWorld = (req, res) => {
      res.send('Hello, World');
    };
    ```

1. Now install the Functions Framework:

    ```sh
    npm install @openfunction/functions-framework
    ```

1. Add a `start` script to `package.json`, with configuration passed via
command-line arguments:

    ```js
      "scripts": {
        "start": "functions-framework --target=helloWorld"
      }
    ```

1. Use `npm start` to start the built-in local development server:

    ```sh
    npm start
    ...
    Serving function...
    Function: helloWorld
    Signature type: http
    URL: http://localhost:8080/
    ```

1. Send requests to this function using `curl` from another terminal window:

    ```sh
    curl localhost:8080
    # Output: Hello, World
    ```

### Quickstart: Build a Deployable Container

1. Install [Docker](https://store.docker.com/search?type=edition&offering=community) and the [pack](https://buildpacks.io/docs/install-pack/) tool.

2. Build a container from your function using the [Cloud Native Buildpacks](https://buildpacks.io/):

    ```sh
    pack build \
      --builder openfunction/builder-node:v2-16.13 \
      --env FUNC_TYPE=http \
      --env FUNC_NAME=helloWorld \
      my-first-function
    ```

3. Start the built function container:

    ```sh
    docker run --rm -p 8080:8080 -e NODE_ENV=dev my-first-function
    # Output: Serving function...
    ```

    > NOTICE: `-e NODE_ENV=dev` is required to display "Serving function...", and you can also append `-e DEBUG=*` to display Express internal debug messages.

4. Send requests to this function using `curl` from another terminal window:

    ```sh
    curl localhost:8080
    # Output: Hello, World!
    ```

## Configure the Functions Framework

You can configure the Functions Framework using command-line flags or
environment variables. If you specify both, the environment variable will be
ignored.

| Command-line flag  | Environment variable      | Description                                                                                                                                                                                                      |
| ------------------ | ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--port`           | `PORT`                    | The port on which the Functions Framework listens for requests. Default: `8080`                                                                                                                                  |
| `--target`         | `FUNCTION_TARGET`         | The name of the exported function to be invoked in response to requests. Default: `function`                                                                                                                     |
| `--signature-type` | `FUNCTION_SIGNATURE_TYPE` | The signature used when writing your function. Controls unmarshalling rules and determines which arguments are used to invoke your function. Default: `http`; accepted values: `http` or `event` or `cloudevent` |
| `--source`         | `FUNCTION_SOURCE`         | The path to the directory of your function. Default: `cwd` (the current working directory)                                                                                                                       |

You can set command-line flags in your `package.json` via the `start` script.
For example:

```js
  "scripts": {
    "start": "functions-framework --target=helloWorld"
  }
```

## Run your function on Serverless platforms

### Container environments based on Knative

The Functions Framework is designed to be compatible with Knative environments. Build and deploy your container to a Knative environment.

### OpenFunction

![OpenFunction Platform Overview](https://openfunction.dev/openfunction-0.5-architecture.png)

Besides Knative function support, one notable feature of OpenFunction is embracing Dapr system, so far Dapr pub/sub and bindings have been support.

Dapr bindings allows you to trigger your applications or services with events coming in from external systems, or interface with external systems. OpenFunction [0.6.0 release](https://openfunction.dev/blog/2022/03/25/announcing-openfunction-0.6.0-faas-observability-http-trigger-and-more/) adds Dapr output bindings to its synchronous functions which enables HTTP triggers for asynchronous functions. For example, synchronous functions backed by the Knative runtime can now interact with middlewares defined by Dapr output binding or pub/sub, and an asynchronous function will be triggered by the events sent from the synchronous function.

Asynchronous function introduces Dapr pub/sub to provide a platform-agnostic API to send and receive messages. A typical use case is that you can leverage synchronous functions to receive an event in plain JSON or Cloud Events format, and then send the received event to a Dapr output binding or pub/sub component, most likely a message queue (e.g. Kafka, NATS Streaming, GCP PubSub, MQTT). Finally, the asynchronous function could be triggered from the message queue.

More details would be brought up to you in some quickstart samples, stay tuned.

### Google Cloud Functions

The [Node.js 10 runtime on Google Cloud Functions](https://cloud.google.com/functions/docs/concepts/nodejs-10-runtime) is based on the Functions Framework. On Cloud Functions, the Functions Framework is completely optional: if you don't add it to your `package.json`, it will be installed automatically.

After you've written your function, you can deploy it from your local machine using the `gcloud` command-line tool. [Check out the Cloud Functions quickstart](https://cloud.google.com/functions/docs/quickstart).

### Cloud Run / Cloud Run for Anthos

After you've written your function, added the Functions Framework and updated your `start` script in `package.json`, deploy it to Cloud Run with `gcloud run deploy`. Check out the [Cloud Run quickstart for Node.js](https://cloud.google.com/run/docs/quickstarts/build-and-deploy/nodejs).

If you want even more control over the environment, you can [deploy to Cloud Run for Anthos](https://cloud.google.com/anthos/run/docs/quickstarts/prebuilt-deploy-gke). With Cloud Run for Anthos, you can run your function on a GKE cluster, which gives you additional control over the environment (including use of GPU-based instances, longer timeouts and more).

## Enable CloudEvents

The Functions Framework can unmarshall incoming [CloudEvents](http://cloudevents.io) payloads to a `cloudevent` object. It will be passed as an argument to your function when it receives a request. Note that your function must use the `cloudevent`-style function signature:

```js
const functions = require('@openfunction/functions-framework');

functions.cloudEvent('helloCloudEvents', (cloudevent) => {
  console.log(cloudevent.specversion);
  console.log(cloudevent.type);
  console.log(cloudevent.source);
  console.log(cloudevent.subject);
  console.log(cloudevent.id);
  console.log(cloudevent.time);
  console.log(cloudevent.datacontenttype);
});
```

Learn how to use CloudEvents in this [guide](docs/cloudevents.md).

## Advanced Docs

More advanced guides and docs can be found in the [`docs/` folder](docs/).

## Contributing

Contributions to this library are welcome and encouraged. See
[CONTRIBUTING](CONTRIBUTING.md) for more information on how to get started.

[ff_node_unit_img]: https://github.com/openfunction/functions-framework-nodejs/workflows/Node.js%20Unit%20CI/badge.svg
[ff_node_unit_link]:  https://github.com/openfunction/functions-framework-nodejs/actions?query=workflow%3A"Node.js+Unit+CI"
[ff_node_lint_img]: https://github.com/openfunction/functions-framework-nodejs/workflows/Node.js%20Lint%20CI/badge.svg
[ff_node_lint_link]:  https://github.com/openfunction/functions-framework-nodejs/actions?query=workflow%3A"Node.js+Lint+CI"
[ff_node_conformance_img]: https://github.com/openfunction/functions-framework-nodejs/workflows/Node.js%20Conformance%20CI/badge.svg
[ff_node_conformance_link]:  https://github.com/openfunction/functions-framework-nodejs/actions?query=workflow%3A"Node.js+Conformance+CI"
