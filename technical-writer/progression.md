# My Learning Progression
This document is a way to keep track of my learning progress over the course of the week. I decided to create this for several reasons:
- **Learning Retention** &ndash; Notes that I can continue to refer to and that I will copy from this document prior to the PR and transfer to my own machine
- **Motivation** &ndash; I've got a long way to go, and seeing how far you've come is a great push.
- **Proof of my Commitment** &ndash; I am taking this assignment seriously and can absolutely learn whatever is necessary. Now and always. (Plus, I am ChatGPT-proofing myself.)

## Terms and Concepts to Explore
- **Kubernetes as a whole** &ndash; We have a basic understanding of docker now (Dockerfile --> Image --> Container). A Dockerfile is essentially a text document that is a recipe for an image. It goes in a structured order, beginning with the OS, and specifies all dependencies of an application needed for it to run. It gets extremely detailed, all the way down to the version number. You run the Dockerfile once to get an image. This image has the desired application and all of its dependencies (downloaded?) at rest. You cannot change an image once it's compiled; if you need to update the image, you need to update the Dockerfile and recreate the image. When you run the image, it becomes a container. Docker is wonderful because it allows each application to run in an isolated container and be distributed again and again, meaning each application will function identically on each computer, no matter the system. There is no additional config needed, and it is far more efficient than VMs. 
  - The [Kubernetes Overview](https://kubernetes.io/docs/concepts/overview/) page describes Kubernetes as a system and way to handle multiple containers at once, scaling them as needed to fit traffic and replacing them should there be a problem. 
- **Vertical Scaling** - Making a single machine/server stronger to carry out a certain function.
- **Horizontal Scaling** - Using multiple machines/servers to carry out a certain function.

## Types of Technical Documentation
What is the difference between a procedure? A tutorial? A reference page? Let’s start [here](https://danielsieger.com/blog/2023/04/24/framework-for-better-documentation.html) to know what kind of mind to approach this with.
- **[Tutorial](https://docs.spectrocloud.com/clusters/public-cloud/deploy-k8s-cluster/)** &ndash;   Tutorials are *learning-oriented* and more for beginners. They walk you through how to do something with additional explanations to help build your base of understanding. **Purpose = Learning**.
- **[Procedure](https://docs.spectrocloud.com/clusters/cluster-management/remove-clusters/)** &ndash; Procedures can also be referred to as "help guides." They are for intermediary knowledge of a certain topic. Procedures are *task-oriented*. The scope is more honed and provides steps on how to achieve one thing. It is generally not long. Unnecessary details can be omitted, and it is not designed to give the full picture. **Purpose = Problem solving**.
- **[Reference](https://docs.spectrocloud.com/vertex/install-palette-vertex/install-on-kubernetes/vertex-helm-ref/#required-parameters)** &ndash; Describes in detail how something works. In software, usually API or library references. For experienced audiences since they just need to usually remember or reference something. Should be concise and to the point. **Purpose = Quick information retrieval**.

We want to create a *reference* doc. The audience has basic Kubernetes knowledge. So, this likely means likely H2s, a small blurb (serves as a refresher or quick summaries), and tables. We want to base this off of existing SpectroCloud documentation to maintain consistency.

## Style Guide Reminders
Items that differ from Drake's style guide that I need to remember:
- Avoid present participles and gerunds in headings. 
- Use title caps for acronyms and initialisms. If the term only appears once, do not provide the acronym or initialism in parenthesis. Camel case is acceptable.
- Use first-person and take ownership.

## What I Did Today
### Thursday, September 26
- Downloaded Git + Git Bash on my local machine; decided to steer away from GitKraken in favor of working in the terminal and learning basic commands
- Cloned the **docs-assignments** repo
- Downloaded Obsidian and began learning Markdown (as seen throughout this document, eeeeeeeyyyyy!)
- Started the deep dive into Kubernetes
- Began this document for record-keeping purposes
- Familiarized self with provided Style Guide
#### Next Steps
- Get a Docker container running in preparation for QA