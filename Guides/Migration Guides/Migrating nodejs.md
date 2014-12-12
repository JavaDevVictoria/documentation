# Migrating dotCloud nodejs service to Next dotCloud platform

With Next dotCloud you can use the latest [Node.js](http://nodejs.org/) and [npm](https://www.npmjs.com/) versions without any hassle. Migrating an existing app should take only a few steps. Please look at our [Introduction for dotCloud Developers](./an-introduction) to make yourself familiar with the platform differences.
This document covers the Node.js specific changes.

* Procfile to start the server
* Versions and dependencies
* Server port assigned by container
* Migrating a custom service using Node.js
* 

## Procfile to start the server
The new build process is based on [Buildpacks and Procfile](../../platform-documentation#buildpacks-and-the-procfile). The `Procfile` replaces the `dotcloud.yml` and is expected to be in the project root. It can be as simple as this:
~~~ini
web: node hellonode/server.js
~~~
For *web* containers we point the `node` command to start the `server.js` in the `hellonode` directory. The [convert dotcloud.yml](./converting-dotcloud-dot-yml) guide contains more details about other settings.


## Versions and dependencies
Versions and dependencies are managed via `package.json`. Like the `Procfile` our buildpack looks for it in the project root. 
~~~bash
~~~


## Server port assigned by container

    | dotCloud| Next dotCloud
----|---------------------------------------|-------------------------------
Supported Node.js Version |0.4.x - v0.8.x | 0.0.1 - latest
x|dotCloud.yml|	
y|supervisord.conf|
Project root | defined in dotCloud.yml| package.json in project root
Server listening | Port 8080 | assigned by container

