# Fanout Test Handler

This is a Compute@Edge app that implements test endpoints for trying out Fanout. It also acts as an example of how to implement a backend handler at the edge.

It subscribes all client connections to channel `test`.

## Usage

1. Deploy the app to a Compute@Edge service.

2. Add a Host to the service, named `self`, that points back to the same service. For example, if the service has a domain `foo.edgecompute.app`, then set the destination host/port to `foo.edgecompute.app` and port 443. Also set Override Host to the same host value.

3. Ask in #websockets-and-fanout-beta-program to have your service ID and backend ID onboarded to Fanout. You will be given a secret to use for publish calls.

After completing these steps, it should be possible to make connections to the service and listen for data. Data can be sent to the connections via the GRIP publish endpoint at `https://fanout.fastly.com/{service-id}/publish/`.

## How it works

The app is invoked twice:

1. Initially, an HTTP or WebSocket request arrives at the app without having been routed through the Fanout proxy yet. The app checks for this via the lack of a `Grip-Sig` header. If that header is not present, the app calls `upgrade_websocket("self")` and exits. This tells the subsystem that the connection should be routed through Fanout. Despite the name of this function, it works for both HTTP and WebSockets.

2. Fanout then makes one or more HTTP requests to the specified backend server. In this case the backend server is the same app (`self`). These requests have the `Grip-Sig` header set, so the app knows they have been routed through Fanout, and handles them accordingly.

# Video switching demo
This demo show's how to 