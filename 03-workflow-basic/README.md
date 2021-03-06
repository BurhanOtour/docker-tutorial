# Introduction

In this tutorial we use Docker to run a react app. 

# Development Workflow 

In this tutorial, we try to incorporate docker into the traditional software application development workflow. 

## Development workflow

The development workflow is centralized around a github repository (or any other VCS). A developer develop a feature and push it into a **feature branch**. 

When feature is pushed, a **pull requrest** to a **master branch** is created to merge the newly created feature into the new version of the app.

When a pull request is made, the feature banch is pushed to `Continues Integration (CI)` system (like `Travis CI`) to check for the system's buildability and being passing all tests.

When the application run and pass all tests, the new version of the app is deloyed. For example, if the application is a web app, it is pushed to some sort of web hosting system to serve the app.

### Where is Docker in all this?

Docker is not a hard requirement for previously mentioned development workflow, however, using Docker will make the life much easier.

## Docker and development workflow

In order to user Docker correctly, we need to understand that each phase in the development workflow is mapped to an instruction that is used to triger a workflow task. 

<table>
    <thead>
        <tr>
            <td>Phase</td>
            <td>instruction for react app</td>
            <td>Description</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Development (Dev)</td>
            <td><code>npm run start</code></td>
            <td>
            <ul>
                <li>Run for <b>development</b> only</li>
                <li>It runs a development server so is not used for production</li>
            </ul>
            </td>
        </tr>
         <tr>
            <td>Testing</td>
            <td><code>npm run test</code></td>
            <td>Hint: we push to production when tests pass</td>
        </tr>
        <tr>
            <td>Production (Prod)</td>
            <td><code>npm run build</code></td>
            <td>Generate a production-code stored in the <code>build</code> folder</td>
        </tr>
    </tbody>
</table>

The previously mentioned instructions are `node` specific, however, the same semantic is applicatable to other environments and frameworks.

## Development

We create a docker file [Dockerfile.dev](Dockerfile.dev) that is used to build a development version of the `frontend` app and run a local development server.

As `Dockerfile.dev` is not a standard input for `docker build` command, we need to tell the image build process which docker file to use. To do that, we execute the following statement: 

`> docker build -f Dockerfile.dev -t botour/reactappp-dev .`

To run the react app frontend container:

`docker run -it -p 3000:3000 botour/reactapp-dev` . 

Then, open your `localhost:3000` in browser.

## Live Editing

When we build an image, we take a snapshot of our porject directory (src, config, etc...) and put it in the working directory of the image  using `COPY . .`. Hence, when we run a the cotainer out of that image and later do changes to our react app code base, those changes are not reflected in running version of the app.s

We could however, hook the project files inside of a docker container to local project directory. 

This happen at runtime, i.e. when we run a container (not when we create the image). This is enabled by using a feature in Docker called **Volumes**.s

Note: this feature is usefull for **DEVELOPMENT ONLY** purposess.

Following is an example for creating and hooking our project directory inside of the container to local source code directory using volumes: 

`reactapp-dev > docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app <image_id>`

### Bookmarking Volumes

`-v /app/node_modules`, don't try to map it up to anything else inside of local directory.  

### Better Command?

`reactapp-dev > docker run -p 3000:3000 -v $(pwd)/src:/app/src <image_id>`

We can make us of [docker compose](frontend/docker-compose.yml) to make life much easeier when running containers.




