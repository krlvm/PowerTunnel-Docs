# Getting Started

The language for developing PowerTunnel plugins is Java or any JVM-compatible programming language. You also need Java Development Kit (JDK) for plugin development.

!!! warning
    If you intend to use your plugin on Android versions older than Android 8 Oreo, you should avoid using Java 8 APIs and functional interfaces as they are not supported on older Android versions.

## Getting SDK

PowerTunnel SDK is a Java library that contains all the required definitions for plugin development.

The implementations of these definitions are found in PowerTunnel Core – the base component of the Desktop and Android editions, with an SDK version corresponding to each Core version.

While the SDK is committed to supporting upwards compatibility, it is constantly evolving, so the minimum required target SDK version is the maximum required SDK version for API calls by your plugin.

!!! tip
    Open the About page to determine the version of the Core shipped with your application.

## Installing SDK

To use PowerTunnel SDK, you need add the SDK jarfile as a dependency to your project.

!!! note
    You should never include the SDK files in your plugin artifact