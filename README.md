# Containers as Steps Workshop
Container-based pipelines have quickly become the standard for of creating scalable continuous integration and delivery pipelines. Container-based pipelines solve numerous problems caused by the traditional VM based pipelines. Issues like version and library conflicts are gone thanks to isolation. Caching can be greatly improved thanks to a shared volume model. And the biggest advantage: developers have much more power and choice when creating their pipelines. They can pick the tools and versions they need without having to open a ticket. This is the core of self serve operations. 

No matter where you implement your container-based pipeline the basic unit will be containers. In this short workshop, we'll take you through the creation of purpose built Docker images designed to be used in pipelines. This primer is quick and easy and should only take a few minutes. Only basic knowledge of Docker is required. 

![alt CI/CD](https://github.com/todaywasawesome/containers-as-steps/blob/master/Docker-cicd-pipelines.jpg)


## Introduction
CI/CD pipelines are easier to make, maintain, and use when every step is it's own image. Creating custom steps that can be reused over and over is incredibly easy. Checkout https://steps.codefresh.io/ for examples of images that have been built into CI/CD steps. 

## Guide

### Prerequisites
- [Docker](https://docs.docker.com/install/)
- A free [Codefresh](https://codefresh.io/) account

### Create and Run An Image That Knows How To Curl
If you'd like to see this in a pipeline, skip to the next step.

On your machine, create a new folder called `example` and Create a file called `Dockerfile`. Add this code to it.

```
FROM alpine
RUN apk add --no-cache curl
CMD curl $URL
```

Now run `docker build . -t mycurl`

This will build a Docker image that accepts an environmental variable for `URL`. Try it out by running `docker run -it -e URL=ismycomputeron.com mycurl`

This should return a simple webpage letting you know that in fact, yes, your computer is on. 

You've now created a single purpose Docker image! To build your image, just make sure it has all the components installed you need and you leave a `CMD` at the end that accepts whatever variables you're looking for. 

### Create and Run an Image in a Pipeline
1. Create your Codefresh account by going to https://codefresh.io/. 
2. After you login, click "Add New Repository" and paste in the URL for this repo. 
3. Select "I have a Codefresh Yaml" and put `example/alpine-curl.yml` for the path. Click next, this will show a preview verifying you have the right file. 
4. Save the pipeline and click build.

This will build an image defined inside the Codefresh yaml and then run it demonstrating curl. 

### Passing Information and Dependencies to Later Steps
One of the most common challenges of using a container for a step is that it be difficult to share information between steps. To make things easy, Codefresh creates a persistent volume for every pipeline available at `/volume/codefresh`. For ease of use, inside Codefresh this path can be filledin using the variables `${{CF_VOLUME_PATH}}`. 

That means if we write info in step 1, we can read it in step 2, even though they're in different. containers. 

To try this demo, use the same steps as the past step. Add the Codefresh yaml inside example-passinfo directory to your Codefresh account and run the pipeline. 

This example uses three steps. 
1. Builds a Docker image that "knows" how to write some text to the shared volume. 
2. Runs the utility image we built in step one to write text to the shared volume.
3. Uses Alpine to read out the variables we wrote in step two.

## Why Use a Container for Every Step?
Tl;dr It's efficent, easy, repeatable. 

For a longer answer, check out this webinar explaining the practice. https://codefresh.io/events/docker-based-pipelines-devops-com/
