#helm #chart #k8s #deployment 

**Chart.yaml** is the primary file within any Helm chart, and should always be present. This file describes the details and metadata about the chart.

Here is an example of a basic **Chart.yaml**:

**apiVersion: v2  
name: mychart  
description: A Helm chart for Kubernetes  
type: application  
version: 0.1.0  
appVersion: 1.16.0**

Lets review each of the fields above:

- **apiVersion**  
    The chart API version to use. Note: "**v1**" indicates support for Helm 2).
- **name**  
    The name of the chart.
- **description**  
    A brief description of the chart.
- **type**  
    The type of the chart (either **application** or **library**). We will talk more about **library** charts later in the course.
- **version**  
    The version of the chart.
- **appVersion**  
    The version of the application being deployed.

Note that **version** is not to be confused with **appVersion**, which is used to indicate the released version of the application being deployed. For example, you may have a chart called **my-nginx** which deploys Nginx version 1.17.10. The chart **version** field may be 0.1.0, but **appVersion** would be 1.17.10, indicating the release of the Nginx software itself.

Additionally, the version field is expected to strictly follow the [Semantic Versioning (SemVer) 2](https://semver.org/spec/v2.0.0.html) standard.

Some other valid fields in **Chart.yaml** include the following:

- **home**  
    URL to the project homepage.
- **sources**  
    List of URL(s) for the chart’s source code.
- **keywords**  
    List of terms used to locate this chart.
- **maintainers**  
    A list of name/email combinations of chart maintainers.
- **icon**  
    A URL to an image representing this chart.
- **kubeVersion**  
    A SemVer constraint specifying the version of Kubernetes required.
- **dependencies**  
    A list of dependency charts required by this chart (you can find more information about dependencies later in this chapter, see: _"Dependencies"_).