
---

# 🛠 Jenkins Docker Agent Cheat Sheet

| Task / Purpose                 | Docker Image                        | Notes / Why                                                                                          |
| ------------------------------ | ----------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **Jenkins default agent**      | `jenkins/inbound-agent`             | Preconfigured agent for Jenkins controller. Includes Java and agent tools. Best starting point.      |
| **Java builds**                | `openjdk:17`, `openjdk:21`          | Official OpenJDK images. Pick version matching your project. Lightweight or slim variants available. |
| **Maven builds**               | `maven:3.9-eclipse-temurin-21`      | Maven + JDK included. Ideal for Java projects with Maven.                                            |
| **Gradle builds**              | `gradle:8.4-jdk17`                  | Gradle + JDK installed. Use slim version to save space.                                              |
| **Node.js builds**             | `node:20`, `node:20-alpine`         | Includes npm & npx. Alpine variant is smaller, faster to pull.                                       |
| **Python builds**              | `python:3.12`, `python:3.12-slim`   | Includes pip. Slim variant reduces image size. Add poetry, pytest, or other tools as needed.         |
| **Go builds**                  | `golang:1.21`, `golang:1.21-alpine` | Go compiler and standard tools preinstalled.                                                         |
| **.NET / C# builds**           | `mcr.microsoft.com/dotnet/sdk:8.0`  | .NET SDK for building and testing .NET apps.                                                         |
| **Ruby builds**                | `ruby:3.2`, `ruby:3.2-slim`         | Includes Ruby and RubyGems.                                                                          |
| **PHP builds**                 | `php:8.2-cli`                       | CLI version for running scripts and tests.                                                           |
| **Docker-in-Docker**           | `docker:24-dind`                    | Use when builds need to run Docker commands. Requires mounting `/var/run/docker.sock`.               |
| **Terraform / Infrastructure** | `hashicorp/terraform:1.5.9`         | Preinstalled Terraform for IaC pipelines. Add cloud CLI tools as needed.                             |
| **Linux minimal / generic**    | `ubuntu:24.04`, `debian:12`         | Base image for custom agent images. Install only the tools your pipeline needs.                      |
| **CI utility image**           | `alpine:3.18`                       | Very lightweight. Good for small scripts, quick shell jobs.                                          |

---

## 🔑 Tips for using Docker images in Jenkins

1. **Pick official images first**

   * Safer, updated, widely tested.

2. **Slim / Alpine variants**

   * Faster to pull → faster agent provisioning.

3. **Custom images**

   * Only when official images don’t have your exact tools.
   * Example: `python:3.12-slim` + `poetry` + `pytest` preinstalled.

4. **Labels**

   * Always set the template label in Jenkins (`docker`, `nodejs`, `python`)
   * Match it in your Jenkinsfile:

     ```groovy
     agent { label 'python' }
     ```

5. **Docker-in-Docker (DinD)**

   * Only use if your pipeline runs `docker build` / `docker run` commands.
   * Mount Docker socket: `/var/run/docker.sock:/var/run/docker.sock`
   * Security note: Jenkins gains full Docker control on the host.

---

