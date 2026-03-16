---
title: Create Feature Engineering gRPC Service
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Create Feature Engineering gRPC Service

In modern ML pipelines, feature engineering services often run as independent microservices so they can scale independently.

In this section, you create a **gRPC service that generates features for ML models**.

## Create project directory

```bash
mkdir flyte-ml-pipeline
cd flyte-ml-pipeline
```

## Create protobuf definition

Create the service definition file.

```bash
vi feature.proto
```

Add the following code.

```python
syntax = "proto3";

service FeatureService {
  rpc GenerateFeatures (FeatureRequest) returns (FeatureResponse);
}

message FeatureRequest {
  int32 value = 1;
}

message FeatureResponse {
  int32 feature = 1;
}
```

## Generate gRPC code

Compile the protobuf file.

```python
python3.11 -m grpc_tools.protoc \
-I. \
--python_out=. \
--grpc_python_out=. \
feature.proto
```

This generates the Python service files used by the gRPC server.

## Create feature service

Create the server implementation.

```bash
vi feature_server.py
```

Add the following code.

```python
import grpc
from concurrent import futures
import feature_pb2
import feature_pb2_grpc


class FeatureService(feature_pb2_grpc.FeatureServiceServicer):

    def GenerateFeatures(self, request, context):

        value = request.value
        feature = value * 10

        print("Generating feature for:", value)

        return feature_pb2.FeatureResponse(feature=feature)


def serve():

    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))

    feature_pb2_grpc.add_FeatureServiceServicer_to_server(
        FeatureService(), server
    )

    server.add_insecure_port("[::]:50051")

    server.start()

    print("Feature gRPC service running on port 50051")

    server.wait_for_termination()


if __name__ == "__main__":
    serve()
```

## Run the service.

```bash
python3.11 feature_server.py
```

The output is similar to:
```output
Feature gRPC service running on port 50051
```

## What you've learned and what's next

In this section, you learned how to:

- define gRPC services using protobuf
- generate Python service code
- implement a feature engineering microservice

In the next section, you will create the **Flyte ML training workflow** that calls this service.
