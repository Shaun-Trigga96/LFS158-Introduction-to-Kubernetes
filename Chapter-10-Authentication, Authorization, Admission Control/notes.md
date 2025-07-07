# Authentication, Authorization, Admission Control

## Chapter Overview

Every API request reaching the API server has to go through several control stages before being accepted by the server and acted upon. In this chapter, we will learn about the Authentication, Authorization, and Admission Control stages of the Kubernetes API access control.

## Learning Objectives

By the end of this chapter, you should be able to:

Discuss the authentication, authorization, and access control stages of the Kubernetes API access.
Understand the different kinds of Kubernetes users.
Explore the different modules for authentication and authorization.

- `Authentication`, Authorization, and Admission Control - Overview
To access and manage Kubernetes resources or objects in the cluster, we need to access a specific API endpoint on the API server. Each access request goes through the following access control stages:

- `Authentication`
Authenticate a user based on credentials provided as part of API requests.

- `Authorization`
Authorizes the API requests submitted by the authenticated user.
Admission Control
Software modules that validate and/or modify user requests.
The following image depicts the above stages:

![Controlling Access to the API](ControllingAccesstotheAPI.png)

(Retrieved from [kubernetes.io](https://kubernetes.io/docs/concepts/security/controlling-access/))

