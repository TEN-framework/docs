# Goal

Modularity is a cornerstone of robust software design, offering numerous advantages including simplified maintenance, enhanced scalability, and increased development velocity. To fully leverage these benefits, the platform must provide comprehensive mechanisms to foster the implementation of best engineering practices for modular develop.

Modularity is a cornerstone of robust software design, offering numerous advantages including simplified maintenance, enhanced scalability, and increased development velocity. To fully leverage these benefits, the platform must provide comprehensive mechanisms to foster the implementation of best engineering practices for modular development.

## Modularization

1. Defined Module Boundaries

   Including boundaries for the control plane and data plane, which can be declared statically, facilitating inspection and post-processing of offline tools.

2. Dynamic Service Composition

   The number and type of modules contained in a process can be determined at runtime, without the need to statically determine it during compilation.

3. Streamlined Module Integration

   In scenarios with multiple module compositions, it is desirable to require only declarations without modifying any code in the modules, and ideally, without even writing glue code to combine different modules.

4. Decentralized Module Coupling

   In the architecture, there is no coupling between modules, allowing different modules to be developed by different individuals and development teams, and they can be dynamically replaced.

5. Flexible Module Interface Communication

   The interface invocation between modules needs to have two types. One type requires the parameters of the invocation to be consistent on both sides, such as parameter names, types, etc. The other type allows the parameters of the invocation to be different on both sides, such as different parameter names and types, but the platform provides mechanisms to bridge the invocation on both sides. This second type is an important foundation for the independent development and deployment of modules.

## Orchestration

Once the modules are successfully separated, they need to be combined to create a truly functional service. Therefore, the architecture needs to provide sufficient mechanisms to flexibly and easily compose multiple modules into a service. This composition should be highly adaptable, even in cases where the module source code is unavailable, enabling the modules to be combined with great flexibility.

1. Directed Cyclic Graph (DCG) Topologies

   The composition between modules supports not only single-line pipeline topologies but also arbitrary graph structures that are independent of each other in the control plane and data plane. This includes 1-to-n, n-to-1, and n-to-m interaction relationships.

2. Cross-Process and Thread Orchestration

   Coordinating module instances running in different processes and threads to form a final service scenario.

3. Dynamic Orchestration Configuration

   Orchestration can be dynamically issued during runtime or pre-configured within the service before it runs.

## Flexible Configuration Settings

Each instance of a module needs to have a method for independent configuration, where the configurations can be set either before runtime or dynamically during runtime or even distributed as configuration updates.

1. Hierarchical Configuration Support

   Architecturally, different instances of modules may have different configuration possibilities, and the configuration possibilities may also vary across the hierarchical context of running modules.

2. Versatile Configuration Capabilities

   Configurations can be pre-set before runtime, dynamically changed during runtime, or even distributed during the execution process.

## Diverse Releasing Capabilities

Some modules may prefer not to open-source their codes. Therefore, the architecture needs to allow the full utilization of these modules' functionalities, even without accessing their source codes. This includes the ability to integrate these modules to form a larger and more comprehensive service.

1. Support Binary & Source Release

   Modules support source distribution and binary distribution, and both distribution modes can coexist for the same module.

2. Integration With Cloud Marketplace

   Both source and binary distributed modules can be published on the cloud marketplace for developers to download and incorporate into their projects.

## Multiple Instances Capability

The flexibility of having multiple instances is always superior to the restriction of having only a single instance. Therefore, it is necessary to ensure, in the architecture, to support multiple instances in all aspects.

1. Multiple Instances of the Platform Itself

   The platform itself does not use any global variables or employ any flawed designs that would prevent the external environment utilizing the platform from having multiple platform instances within a single business process.

2. Multiple Instances of Each Module

   Architecturally, it should allow module developers to develop modules without relying on any global variables, enabling multiple instances of the same module within a process without conflicts. These different instances of the modules not only can run in the same thread or different threads, but they also can run within the same process or different processes.

3. Multiple Instances of the Orchestrated Pipelines

   Within a single process, it is possible to include multiple orchestrated pipelines that can run concurrently in different threads or sequentially within the same process.

## Diverse Distribution Capability

Different modules can be deployed in different execution environments based on actual requirements. Furthermore, modules that run in these different environments need to have sufficient mechanisms in the architecture to facilitate their seamless integration or adaptation.

1. Dynamic Switching of Multiple Distribution Configurations

   The service itself can be quickly split and distributed to different processes and threads to achieve module-level high availability and high concurrency, and it is easier to achieve hot-updating of modules after the application has been made.

2. Decoupling of Distribution Configuration From Code

   When distributing modules across different processes and threads, it is preferable that the modules themselves do not require any modifications.

3. Decoupling of Distribution Configuration From Compilation and Build

   The distribution of modules across different processes and threads should be accomplished through simple declarations.

## Flexibility Input/Output Support

The way modules handle their external input and output determines the flexibility of their arrangement and the possibility of achieving low coupling between them. Therefore, the architecture must provide sufficient mechanisms to enable input and output interactions between modules to achieve a high degree of decoupling. This greatly facilitates the flexible composition of multiple modules.

1. Supports Control Plane as well as Data Plane (audio/video/data)

   The interaction between modules can involve invoking commands as well as passing raw audio and video data.

2. Simultaneous Support For Inter-process and Inter-thread Input/Output Patterns

   Architecturally, within the same external input-output operations, supports module composition across processes and threads simultaneously.

3. The Essence of RPC

   The module's external input/output needs to possess the essence of RPC, so that different modules can be placed in different processes and ultimately be combined into one scenario.

4. The Ability to Trigger Multiple Downstream Components

   A single external action of a module can trigger operations in an unlimited number of downstream modules, and the platform will aggregate the responses from all downstream modules and return them to the original module.

5. Declarative Capability for External Input and Output

   The external input and output interfaces of modules can be statically declared (in JSON or protobuf, …), which benefits the possibility of static checks in offline tools.

6. Simplifying the Writing of External Input/Output Codes

   To enhance development efficiency, the external input/output interfaces need to support function calls, which facilitate the coding of the module itself. This function interface should be generated automatically by tooling as much as possible.

7. Supporting Data Types That Cannot be Serialized or Deserialized

   The external interface of the module needs to support the transmission of data that cannot be serialized/deserialized. Offline tools can automatically recognize the usage of this transmission method and tag it to the metadata of the module, indicating that the module and other modules using it need to be in the same process.

8. Transparent Underlying Data Exchange for Inter-process Communication in Module

   In the architecture, if the interaction between modules crosses processes, there is no need to worry about the underlying transmission logic and implementation (tcp/http/websocket/…). This aspect is handled entirely by the platform.

9. Support Synchronous and Asynchronous Control Plane Operations

    The control plane has bidirectional interaction, allowing not only forward command sending but also corresponding response returns.

10. Support Restricted Execution Context

    Architecturally, it enables synchronous interaction between modules that occurs within the same thread.

## Freedom in Developing Modules

The architecture must empower developers with the highest level of flexibility when developing modules, enabling them to create modules with unlimited functionalities and types.

1. Freedom to Utilize Third-party Library Functionality

   Modules can utilize any third-party libraries to accomplish desired functionalities.

2. Freedom to Utilize Operating System Functionality

   Developers should be given maximum freedom when developing modules, such as the ability to freely create and terminate native threads within the modules.

## Programming Language Support

Different development languages are more suitable for different scenarios. For example, Python is commonly used in AI scenarios, while C/C++ is often used in high-performance scenarios. Therefore, the architecture must support developers to develop modules using any programming language, and these modules developed in different languages should be well integrated within a service.

1. Unlimited Programming Languages Support

   In the architecture, it must support all programming languages (C/C++/go/Java/Python/JavaScript/TypeScript/ruby/Perl/…), even if only a few programming languages are supported initially.

2. Modules Developed in Different Languages Can Run Together

   Architecturally, it should allow modules written in different programming languages to run within the same process.

3. Unified Interaction Mindset Across Different Languages

   The external interfaces between different language versions of the module have the same interface and behaviors, in addition to the language-specific aspects.

4. Unified Data Types Across Different Languages

   The platform provides a unified data type across different languages, enabling modules written in different languages to have consistent thinking when exchanging data.

## Rich Capabilities for External Interaction

A service is only meaningful if it provides capabilities to the outside world. Therefore, in terms of architecture, it is necessary to empower modules to provide various capabilities to the outside through various standard methods.

1. UI Provider

   In the architecture, as a backend service, it must support providing UI components for the frontend, even if this capability is not initially implemented.

2. RESTful Interface

   As a backend service, it needs to support a RESTful interface, allowing clients to interact with it through RESTful methods.

3. Non-intrusive Integration with Existing Business

   The architecture must support the ability to integrate the framework within existing services that have already been developed, rather than requiring the entire service to be rewritten using the framework.

4. The Whole Can Be in a Single Thread

   In terms of architecture, it can exist as a standalone process or as a thread within an existing business service, and it will not conflict with the existing business code symbolically.

## Testing Friendly

One of the purposes of modularity is to facilitate more effective testing. Therefore, the architecture must support the ability to test each module independently.

1. Diverse Testing Mechanism

   Architecturally, the platform supports multiple testing strategies, including unit test, integration test, module testing, and black-box testing.

2. Independent Module Testing

   Architecturally, modules themselves can be tested independently.

## Offline Processing Capability

The architecture must support the use of offline tools to assist in development, as enhancing the development experience is a straightforward task.

1. Package Manager

   In the architecture, it allows for CLI and the package manager to manage all modules and their combinations.

2. Orchestration Tools

   There are GUI offline tools to facilitate the overall development and orchestration process, enhancing development experience and efficiency.

3. Visual Debugging Tools

   In the architecture, there is the ability to create offline tools to visualize the interaction between modules.

## Robustness

The platform must possess high robustness in order to provide a favorable operating environment for all modules.

1. High Concurrency of Orchestrated Pipelines

   A process can support the simultaneous execution of at least 100 concurrent instances of orchestrated scenes.

2. High Concurrency of RESTful Interface

   A process can support over 20,000 RESTful clients.

3. High Concurrency of Non-RESTful Interface

   A process can support over 1000 non-RESTful clients.
