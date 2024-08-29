---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 🚧 TEN architecture(beta)

## TEN architecture

Now let's discuss what's under the hood. The TEN architecture is composed of various TEN extensions, developed in different programming languages. These extensions are interconnected using Graph, which describes their relationships and illustrates the flow of data. Furthermore, sharing and downloading extensions are simplified through the TEN Extension Store and the TEN Package Manager.

## TEN extension

An extension is the fundamental unit of composition within the TEN framework. Developers can create extensions in various programming languages and combine them to build diverse scenarios and applications. TEN emphasizes cross-language collaboration, allowing extensions written in different languages to work together seamlessly within the same application or service.

For example, if an application requires real-time communication (RTC) features and advanced AI capabilities, a developer might choose to write RTC-related extensions in C++ for its performance advantages in processing audio and video data. Meanwhile, they could develop AI extensions in Python to leverage its extensive libraries and frameworks for data analysis and machine learning tasks.



## TEN graph

A Graph in TEN describes the data flow between extensions, orchestrating their interactions. For example, the text output from a speech-to-text (STT) extension might be directed to a large language model (LLM) extension. Essentially, a Graph defines which extensions are involved and the direction of data flow between them. Developers can customize this flow, directing outputs from one extension, such as an STT, into another, like an LLM.

In TEN, there are four main types of data flow between extensions, they are **Command**, **Data**, **Image Frame** and **PCM Frame**.

By specifying the direction of these data types in the Graph, developers can enable mutual invocation and unidirectional data flow between plugins. This is especially useful for PCM and image data types, simplifying audio and video processing.



## TEN agent app

A TEN Agent App is a runnable server-side application that combines multiple Extensions following Graph rules to accomplish more sophisticated operations. A TEN Agent App is a robust, server-side application that executes complex operations by integrating multiple Extensions within a flexible framework defined by Graph rules. These Graph rules orchestrate the interplay between various Extensions, enabling the app to perform sophisticated tasks that go beyond the capabilities of individual components.

By leveraging this architecture, a TEN Agent App can seamlessly manage and coordinate different functionalities, ensuring that each Extension interacts harmoniously with others. This design allows developers to create powerful and scalable applications capable of handling intricate workflows and data processing requirements.



## TEN extension store

The TEN Store is a centralized platform designed to foster collaboration and innovation among developers by providing a space where they can share their extensions. This allows developers to contribute to the community, showcase their work, and receive feedback from peers, enhancing the overall quality and functionality of the TEN ecosystem.

In addition to sharing their own extensions, developers can also access a wide array of extensions created by others. This extensive library of extensions makes it easier to find tools and functionalities that can be integrated into their own projects, accelerating development and promoting best practices within the community. The TEN Store thus serves as a valuable resource for both novice and experienced developers looking to expand their capabilities and leverage the collective expertise of the community.



## TEN package manager

The TEN Package Manager streamlines the entire process of handling TEN extensions, making it easy to upload, share, download, and install them. It significantly simplifies the workflow by allowing extensions to specify their dependencies on other extensions and the environment. This ensures that all necessary components are automatically managed and installed, reducing the potential for errors and conflicts.

By automatically managing these dependencies, the TEN Package Manager makes the installation and release of extensions extremely convenient and intuitive. This tool not only saves time but also enhances the user experience by ensuring that every extension works seamlessly within the larger ecosystem. This level of automation and ease of use encourages the development and distribution of more robust and complex extensions, further enriching the TEN framework.

