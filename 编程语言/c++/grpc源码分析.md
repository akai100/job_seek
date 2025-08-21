```mermaid
classDiagram
    class ClientAsyncResponseReaderInterface {
        + virtual void StartCall()
        + virtual void ReadInitialMetadata(void* tag)
        + virtual void Finish(R* msg, grpc::Status* status, void* tag)
    }
    class ClientAsyncResponseReader {
        + static void operator delete(void* /*ptr*/, std::size_t size)
        + static void operator delete(void*, void*)
        + void StartCall()
        + void ReadInitialMetadata(void* tag)
        + void Finish(R* msg, grpc::Status* status, void* tag)
    }
    ClientAsyncResponseReaderInterface <|-- ClientAsyncResponseReader
    class GrpcLibrary {
        
    }
    class ChannelCredentials {
    }
    class ServerBuilder {
        + ServerBuilder& AddListeningPort(const std::string& addr_uri, std::shared_ptr<grpc::ServerCredentials> creds, int* selected_port = nullptr)
        + ServerBuilder& RegisterService(const std::string& host, grpc::Service* service)
        + std::unique_ptr<grpc::Server> BuildAndStart()
    }
    class CallHook {
        + void PerformOpsOnCall(CallOpSetInterface* ops, Call* call) virtual = 0
    }
    class ServerInterface {
    }
```
