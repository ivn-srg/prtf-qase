<img src="https://github.com/ivn-srg/prtf-qase/blob/main/logo-2.jpeg" alt="Лого" style="width: 50px; height: 50px;"/>

# Mobile app for Qase TMS 

## Overview

This project is an unofficial mobile client for Qase workflows. It gives users access to core test management entities from an iPhone through a native interface.

The app works with a personal API token and focuses on project, repository, and test entity management.

## Product Context

The idea came from a simple gap: the workflow was useful, but there was no native mobile client for the scenarios I wanted to support.

The app covers:
- browsing projects
- navigating repositories
- viewing test entities
- creating projects and repositories
- working with structured test case content on mobile

## My Role

This was a solo project.

I handled:
- product idea
- screen structure
- architecture
- UI
- API integration
- network layer
- app-level technical decisions

## Gallery
<img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.54.55.png" alt="main screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202024-11-05%20at%2017.40.11.png" alt="projects screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.55.07.png" alt="creating project screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.55.44.png" alt="test entities list screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.55.52.png" alt="creating test entity screen 1" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.56.05.png" alt="creating test entity screen 2" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.56.15.png" alt="detail test case main screen" width="250"><img src="https://github.com/ivn-srg/prtf-qase/blob/main/Simulator%20Screenshot%20-%20iPhone%2016%20Pro%20-%202025-01-07%20at%2020.56.19.png" alt="detail test case screen with properties" width="250">

## Tech stack

![Realm](https://img.shields.io/badge/Realm-5B8C4A?style=for-the-badge&logo=realm&logoColor=white)
![Swift](https://img.shields.io/badge/Swift-F05138?style=for-the-badge&logo=swift&logoColor=white)
![Apple UIKit](https://img.shields.io/badge/UIKit-007AFF?style=for-the-badge&logo=apple&logoColor=white)
![SnapKit](https://img.shields.io/badge/SnapKit-FF4B30?style=for-the-badge&logo=swift&logoColor=white)
![Async/Await](https://img.shields.io/badge/Async%20Await-4BCB1A?style=for-the-badge&logo=swift&logoColor=white)
![MVVM](https://img.shields.io/badge/MVVM-FF3D00?style=for-the-badge&logo=swift&logoColor=white)

## Project architecture
The project uses `MVVM` and `Clean Architecture`.

The structure is organized around feature boundaries so the app can stay maintainable as new flows are added.

Architecture snapshot:

<img src="https://github.com/ivn-srg/prtf-qase/blob/main/Снимок%20экрана%202024-11-05%20в%2018.01.59.png" alt="xcode code structure screen" width="250">

## What I Did

I implemented the app end to end, including:
- API token authentication flow
- navigation between projects, repositories, and entities
- network layer and request composition
- UI composition
- architecture and feature organization

## What This Project Shows

This case is relevant for:
- API-driven iOS development
- UIKit-based application structure
- internal or niche workflow tools
- B2B-style mobile flows
- products with structured entities and backend-heavy logic

It also supports my positioning at the intersection of iOS and QA-oriented product work.

## Links

- **Project code**: The code is located <a href="https://github.com/ivn-srg/iOSPetProjectWithQaseAPI">here</a>.
