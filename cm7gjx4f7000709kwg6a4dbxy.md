---
title: "The Ultimate FastAPI + UV Workspaces Template"
datePublished: Sat Feb 22 2025 18:46:43 GMT+0000 (Coordinated Universal Time)
cuid: cm7gjx4f7000709kwg6a4dbxy
slug: the-ultimate-fastapi-uv-workspaces-template
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740249950244/1a706b0e-c313-4071-8709-eeff1c3f7aba.jpeg
tags: fastapi, uv

---

**Repository URL:**  
[https://github.com/elefher/fastapi-uv-workspaces-template](https://github.com/elefher/fastapi-uv-workspaces-template)

## Introduction

Recently, I learned about [UV workspaces](https://docs.astral.sh/uv/concepts/projects/workspaces/). The concept originates from Rust and [Cargo](https://doc.rust-lang.org/cargo/reference/workspaces.html), and the UV team has implemented it in UV as well. The main objective is to structure your code so that developing new features is both easy and clear, while also enabling multiple teams to work on the same project.

## What Are UV Workspaces?

[UV workspaces](https://docs.astral.sh/uv/concepts/projects/workspaces/) let you split your project into autonomous packages that can be imported into different parts of your code. The main benefit is that you can easily replace a package with updated or refactored code without worrying that your changes will break other parts of the project. While best software engineering practices can minimize such issues, UV workspaces provide clear guidelines by ensuring that each package is autonomous and encapsulates all its required functionality. Packages can also include other packages.

Another benefit of this approach is that you can share packages with others by uploading them as public or private artifacts. This flexibility makes it easier to manage and distribute your code.

## Template Overview

I built this template because I believe it will be useful for those starting a new project or for anyone interested in learning about [UV workspaces](https://docs.astral.sh/uv/concepts/projects/workspaces/). Later, I will also share suggestions on how to migrate an existing project to [UV workspaces](https://docs.astral.sh/uv/concepts/projects/workspaces/).

This template includes:

* Docker support
    
* A single PostgreSQL database connection
    
* SQLAlchemy
    
* GraphQL queries
    
* REST endpoints
    
* Test examples
    
* Tools such as Ruff, MyPy, and Pytest
    

## Project Structure

The template follows a simple structure:

* The FastAPI application lives inside the `app` folder.
    
* All packages reside in the `packages` folder.
    
* Each package has its own `pyproject.toml` file.
    
* UV installs all dependencies into a single `uv.lock` file to avoid conflicts.
    
* Each package should be autonomous and contain its own tests.
    

## Example Packages

For example, the `packages/crawler` package is imported into `app/`[`main.py`](http://main.py), making its functionality available in the main application. Another package, `storage/pg`, is also in the `packages` folder and provides a database connection. The connection is initialized in `app/`[`main.py`](http://main.py), and the models and repositories in the PG package can be used by the crawler package or any other package. Similarly, you can create:

* Multiple storage packages (e.g., one for MongoDB or any other database)
    
* A shared package for common code (e.g., `packages/libs`)
    
* Packages for connecting to other services in a microservices architecture (e.g., `packages/services`)
    

## Migrating an Existing Project

Now, regarding migrating an existing application, I’ll share my experience. When I migrated my project to UV workspaces, it wasn’t very large but did have a few features. The migration process wasn’t too difficult, and the project worked fine afterward. Using a good IDE was especially helpful—when I moved files into different folders, the IDE automatically updated the imports. The only refactoring required was related to environment variables, as I wanted each package to encapsulate all its logic. The most time-consuming part was setting up MyPy properly and moving the tests into their respective packages. MyPy didn’t handle these changes gracefully at first, and it took some time to diagnose the issues. The approach that worked best was to start with a dummy project using UV workspaces to understand how it works, then apply the same logic to my project. I found this particularly useful because it provided a clean slate to understand the fundamentals and ensured that everything worked with minimal files. In an existing project with a lot of code, one change might affect another.Recommendations

My suggestion is to first spend some time understanding how UV workspaces work, then plan how you want to structure your folders and files. Next, use my template or create a quick prototype to implement a basic architecture before migrating your existing code to UV workspaces. Alternatively, you can set up UV workspaces, develop new features within that structure, and gradually migrate the old code.

## Final Thoughts

I hope you find this template helpful for starting new projects or exploring UV workspaces. Let me know if you have any ideas or if there’s something you’d like me to include—I’d be happy to consider your suggestions!