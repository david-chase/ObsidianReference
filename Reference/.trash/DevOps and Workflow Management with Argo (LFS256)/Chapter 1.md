# Chapter Overview and Objectives

Embarking on the journey of exploring Argo and its powerful capabilities in Kubernetes workflow management requires a solid foundation in key concepts. This chapter lays the groundwork by focusing on essential concepts that will enhance your comprehension of Argo and its functionalities.

By the end of this chapter, you should be able to:

- Explain key Argo concepts.
- Present the Argo suite’s use cases.
- Identify the role of Kubernetes.
- Install Kubernetes to work with the Argo suite.

### Introduction to ArgoEssential Concepts for Argo

# What Is GitOps?

GitOps embodies a set of principles that form the backbone of modern software delivery practices. At its core, GitOps hinges on five key aspects, each contributing to a streamlined and efficient development and deployment process.

_Select "Expand" to read more about them._

Core Elements

- Close Declarative configuration
    
    First and foremost is the principle of declarative configuration. This implies a departure from the traditional imperative style of issuing specific commands. Instead, GitOps centers around expressing the desired end state of a system, allowing developers to declare intentions rather than prescribe exact actions. For instance, specifying the desire for three containers for a given application prompts automated agents to adjust the current state accordingly, potentially terminating existing containers to align with the declared configuration.
    
- Close Immutable storage
    
    Git, in the context of GitOps, serves as more than just a version-controlled storage solution; it represents immutable storage. While Git is the predominant source control system, the principles of GitOps are adaptable to other version control systems as well. The immutability aspect underscores the idea that once a configuration is committed to Git, it becomes a static reference point, aiding in reproducibility and traceability. It is also often referred to as the source of truth.
    
- Close Automation
    
    Automation is a cornerstone of GitOps, emphasizing the importance of eliminating manual interventions post-commit to the version control system. The moment changes are integrated into the VCS, the baton is passed to software agents. 
    
- Close Software agents
    
    These agents play a pivotal role in executing the necessary actions to realize the newly declared configuration. The process involves assessing the disparity between the existing system state and the desired state specified in the version control, encapsulating the essence of GitOps' closed-loop nature.
    
- Close Closed loop
    
    Closed loop, in the GitOps paradigm, encapsulates the continuous feedback loop between the actual and desired states of a system. It signifies that the automated actions taken by software agents are not arbitrary but are a direct response to the evolving configuration stored in the version control. The closed loop mechanism ensures that the system perpetually converges towards the declared state, fostering consistency and predictability.