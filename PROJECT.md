# Project Instructions: generic-mcp-server

---

## 1. Project Overview & Goal

*   **What is the primary goal of this project?**
    *  to build a generic MCP server with Spring AI that can be used as the basis for other MCP Server Projects. It should still support STDIO and SSE.  The current app, supports both transports by having the user set an application property before compile time.  Instead, I would like to seperate SSE and STDIO into different app property files so we can pass a profile with the compile and get the right verion without having to change a file.  This will let the actual user determine the mode.

*   **Who are the end-users?**
    *  Developers of MCP Serves

## 2. Tech Stack

*   **Language(s) & Version(s)**: Java 21
*   **Framework(s)**: Spring Boot 3.5.3
*   **Database(s)**: n/a
*   **Key Libraries**: Spring AI 1.0.0 (THE GA RELEASE ONLI)
*   **Build/Package Manager**: MVN
*   **Base Package** com.baskettecase

## 3. Architecture & Design

We will be taking existing code and make it generic.  This code is for a weather app, but after we are done it should just be a generic MCP server.  It should have no mention of weather.  Replace the tools with some basic tools.  make one that will just capitalize a sentence of input, and then make another that takes 2 numbers and a math operator and returns the result.

## 4. Coding Standards & Conventions

*   **Code Style**: Spring Java

## 5. Important "Do's and Don'ts"

*   **DO**: Write Unit tests
*   **DO**: Log important events and errors.
*   **DO**: create a testing script with colors and graphics that will test the app in both modes.  Use --sse or --stdio on the script and then it can pass that to the cmdline as the correct spring profile.