# TEN Framework Concepts Overview

Within the TEN framework, there are several key concepts.

- Extension
- Graph

## Extension

An extension is the smallest unit within the framework. Developers can create extensions using various programming languages and combine these extensions in desired ways to form different scenarios and applications. Cross-language collaboration is a significant feature in the TEN framework's design philosophy, allowing extensions developed in different programming languages to seamlessly work together within the same application or service.

For example, if an application needs to integrate real-time communication (RTC) features with advanced AI capabilities, a developer might choose to develop an RTC-related extension in C++ to leverage its performance advantages for handling audio and video data, while using Python to develop an AI extension, benefiting from its rich libraries and frameworks for data analysis and machine learning tasks.

## Graph

This concept describes the data flow between extensions. For example, you could direct the text output of a Speech-to-Text (STT) extension to a Large Language Model (LLM) extension. This orchestration mechanism is referred to as a graph in TEN. In simple terms, a graph defines which extensions participate and the flow of data between these extensions. Developers can customize the data flow, such as routing the output of an STT extension to an LLM extension.

In TEN framework, there are four main types of data flows between extensions:

- Command
- Data
- Video frame
- Audio frame

By specifying the flow of these data types within the graph, you can achieve inter-extension calls and unidirectional data streams, particularly with audio frame and video frame data types, making audio and video processing more straightforward and intuitive.

## TEN Cloud Store

The TEN Cloud Store is similar to Google's Play Store for Android and Apple's App Store for iOS, providing a marketplace for extensions. This allows developers to share their extensions or use extensions developed by others, which can be downloaded into the TEN app to assist and facilitate development.

## TEN Manager

The TEN Manager simplifies the actions of uploading, sharing, and installing extensions. Extensions can have dependencies on each other and on the environment. The TEN Manager automatically handles these dependencies, making it extremely convenient to install and publish extensions.

## Application Scenarios

A key goal of the TEN framework is to make AI feature integration and application development more efficient and flexible. On the TEN framework, developers can quickly create and deploy various AI function extensions, then rapidly assemble these extensions to form customized applications or services. Here are some typical AI scenarios that demonstrate how TEN facilitates these integrations:

- Intelligent Speech Processing Applications

  Developers can create extensions specifically for speech processing, such as automatic speech recognition (STT), speech synthesis (TTS), and sentiment analysis. For instance, an automated customer service system can identify customer emotions using a sentiment analysis extension by inputting the STT extension's output (text) and responding through a TTS extension, forming a complete conversational system.

- Visual Recognition Systems

  By utilizing image recognition and video analysis extensions, developers can quickly create security monitoring or smart retail solutions. By combining face recognition, object detection, and motion tracking extensions, a multifunctional video surveillance system can be created for real-time event detection and response.

- Recommendation Systems and Data Analysis

  Through the integration of machine learning model extensions and data processing extensions, personalized recommendation systems or data analysis tools can be constructed. These extensions can handle large amounts of data, utilizing algorithmic models to provide accurate user recommendations or insights, widely applicable in e-commerce, content platforms, and other fields.

- Interactive Education and Training Platforms

  TEN allows developers to assemble interactive teaching tools, intelligent tutoring extensions, and assessment modules into a comprehensive educational technology solution. Such a platform can automatically adjust teaching content and difficulty, adapting to different students' learning speeds and styles.

- Health Monitoring and Diagnostic Systems

  By combining medical image analysis, and AI-driven diagnostic tool extensions, TEN can assist healthcare professionals in quickly developing systems for disease monitoring and early diagnosis.

Through the TEN framework, developers can not only rely on existing extensions but also create and share their own AI extensions, extending individual functions to multiple applications, greatly improving development speed and innovation capacity. Additionally, the flexible architecture of the TEN framework ensures scalability and maintainability for various AI applications, providing a solid foundation for modern AI solutions.
