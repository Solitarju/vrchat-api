# VRChat API Library

VRChat API Library is a Node.js library that allows easy integration of the VRChat API into your applications.  

It provides real-time event handling through WebSockets, all endpoints and API sections are built according to the **community driven** [VRChat API Docs](https://vrchatapi.github.io/docs/api/).

Feel free to reach out on the Discord server below for support or inquiries.  

<div align="center">
  <a style="text-decoration: none;" href="https://github.com/Solitarju/vrchat-api-library">
    <img alt="GitHub" src="https://img.shields.io/github/license/Solitarju/vrchat-api-library?logo=github&label=GitHub&link=https%3A%2F%2Fgithub.com%2FSolitarju%2Fvrchat-api-library">
  </a>
  <a style="text-decoration: none;" href="https://www.npmjs.com/package/vrchat-api-library">
    <img alt="NPM" src="https://img.shields.io/npm/l/vrchat-api-library?logo=npm&label=npm&link=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fvrchat-api-library">
  </a>
  <a style="text-decoration: none;" href="https://discord.gg/ER3Z7NBzvv">
  <img alt="Discord" src="https://img.shields.io/discord/1163384360314081342?logo=discord&label=Discord&color=0%2C%200%2C%20255&link=https%3A%2F%2Fdiscord.gg%2FER3Z7NBzvv">
  </a>
</div>

## Table of Contents

- [Disclaimer](#disclaimer)
- [Installation](#installation)  
- [Getting Started](#getting-started)
- [Classes/API Coverage](#classesapi-coverage)
- [TODO](#todo)
- [Changelog](#changelog)

## Disclaimer

**Official Disclaimer from VRChat (Tupper):**  
Use of the VRChat API using applications other than the approved methods (website, VRChat application) is not officially supported. When using the API, please adhere to the following guidelines:

- We do not provide documentation or support for the API.
- Do not make queries to the API more than once per 60 seconds.
- Abuse of the API may result in account termination.
- Access to API endpoints may break at any given time, with no warning.

## Introduction

VRChat API Library is a Node.js module that streamlines integration with the VRChat platform.  

It allows you to interact with VRChat's APIs, enabling essential features and offering QOL utilities & improvements, such as:  

1. Setting & retrieving user information.
2. Real-time events.
3. Custom implemented user-online / user-offline events.  
4. QOL duplicate event prevention.

Each API section and endpoints are mostly built in reference to the **community-driven** [**VRChat API Docs**](https://vrchatapi.github.io/docs/api/), with exception to the real-time EventsApi.

## Installation

Install the library via **NPM**:  
Before installation, ensure you have **Node.js** installed.

```shell
npm install vrchat-api-library
```

## Getting Started

1. Install the library using the [above command](#installation).
2. Include the necessary classes in your application.

```javascript
const { VRChat, Enums } = require('vrchat-api-library');
```

3. Authenticate your application using a VRChat account and start using the library.

```javascript
const { VRChat, Enums } = require('vrchat-api-library');
const { stdin, stdout } = require('process');
const readline = require('readline');

const vrchat = new VRChat();

// Promise based console input function.
// Any promise-based input will work assuming it returns the two-factor authentication code.
function Prompt(query) {
    const question = readline.createInterface({
        input: stdin,
        output: stdout,
    });

    return new Promise(resolve => question.question(query, res => {
        question.close();
        resolve(res);
    }));
}

// Main asynchronous method.
const asyncMethod = async () => {
    const auth = await vrchat.Authenticate({
        username: "username",
        password: "password",
        authCookie: "",
        twoFactorAuth: ""
    }, async (type) => {
        return await Prompt(`Please input ${type} two factor code:\n`);
    });

    console.log(auth);

    console.log(await vrchat.FriendsApi.ListFriends({ n: 100, offline: true }));

    // Basic EventsApi usage, making use of the VRChat class.
    vrchat.EventsApi.Connect();

    // Usage of custom undocumented event type, this isn't valid, just for demonstration purposes.
    // Upon getting an undocumented event type, the library will warn you and ask to report it. (Please do this!!)
    vrchat.EventsApi.on("undocumented event", (data) => {
        console.log("Undocumented Event");
        console.log(data);
    });

    vrchat.EventsApi.on(Enums.EventType.error, (err) => {
        console.log("Error: " + err.message);
    });

    vrchat.EventsApi.on(Enums.EventType.userOnline, (data) => {
        console.log("User online");
        console.log(data);
    });

    vrchat.EventsApi.on(Enums.EventType.userOffline, (data) => {
        console.log("User offline");
        console.log(data);
    });

    // vrchat.EventsApi.Disconnect(); -- Optionally disconnect from the API
}

asyncMethod();
```

## **Classes/API Coverage**

Here's a comprehensive overview of the classes and APIs available in the library:  
All APIs can also be found at the **community-driven** [VRChat API Docs](https://vrchatapi.github.io/docs/api/), with exception to the EventsApi.

### **VRChat**

- **Description**: The VRChat class is designed to simplify authentication and automatically manage the library for you. It's the central component that connects you to each of VRChat's APIs.

### **EventsApi**

- **Description**: The EventsApi class offers a highly stable WebSocket connection for real-time events related to users and friends.

- **Features**:
  - Custom "user-online" and "user-offline" events to monitor user presence.
  - Event type modularity, allowing undocumented event types to work flawlessly.
  - Smart event de-duplication, making sure events don't repeat/spam while letting authentic & valid events pass.

### **AuthenticationApi**

- **Description**: The AuthenticationApi class focuses on handling authentication and user-related features, allowing you to securely log in and interact with VRChat's APIs.

### **AvatarsApi**

- **Description**: The AvatarsApi class enables interactions with user avatars, providing functionalities to work with avatars effectively.

### **EconomyApi**

- **Description**: The EconomyApi class is responsible for managing economic aspects in VRChat, such as transactions and VRC+ subscriptions.

### **FavouritesApi**

- **Description**: The FavouritesApi class offers features related to managing your favourited content in VRChat, including friends, worlds and avatars.

### **FilesApi**

- **Description**: The FilesApi class allows you to interact with files and their associated operations within VRChat.

### **FriendsApi**

- **Description**: The FriendsApi class provides methods to manage your friends list, retrieve information about friends, and monitor friend-related activities.

### **GroupsApi**

- **Description**: The GroupsApi class allows you to create and manage groups in VRChat, enabling collaboration and social interaction within these groups.

### **InvitesApi**

- **Description**: The InvitesApi class handles the process of inviting users to events or groups in VRChat, facilitating social connections and collaboration.

### **InstancesApi**

- **Description**: The InstancesApi class provides functionalities related to specific instances of VRChat worlds, including getting information about a particular instance.

### **NotificationsApi**

- **Description**: The NotificationsApi class manages notifications and alerts for users in VRChat, ensuring that users stay informed about relevant events.

### **PermissionsApi**

- **Description**: The PermissionsApi class allows you to retrieve information about user permissions in VRChat, enabling you to manage access and interactions effectively.

### **PlayerModerationApi**

- **Description**: The PlayerModerationApi class offers features for moderating user behavior and interactions in VRChat, ensuring a safe and enjoyable environment.

### **SystemApi**

- **Description**: The SystemApi class provides access to system-related information in VRChat. While it doesn't require authentication, it remains accessible within the library.

### **UsersApi**

- **Description**: The UsersApi class focuses on interactions with user profiles, allowing you to retrieve user information and manage user-related operations.

### **WorldsApi**

- **Description**: The WorldsApi class enables interactions with VRChat worlds, including features to retrieve world information and manage world-related activities.

### **Enums**

- **Description**: The Enums class consolidates various enumerations, including event types, for better organization and ease of use in the VRChat library.

## TODO

- Wiki/Documentation.
- Code Consistency and General improvements.
- User Class instead of raw JSON.
- Potentially adding support for OSC (Open Sound Control).

## **Changelog**  

All dates in this document are formatted as DD-MM-YYYY.

- v1.2.6 (06/12/2023)
  - Started refactoring API response handling. (Creating classes and patching the API responses, UsersApi complete so far with one minor thing to work out)
  - Updated User Agent parameters & contact information and made version update automatically with package.json, as it's impractical to manually change the hard code each time.
  - Slight README improvements.

- v1.2.5 (16/10/2023)
  - Fixed missing Enums import for GenerateParamater function in Util class.
  - Reformatted and updated README for improved clarity and more comprehensive general information.
  - Changed Discord support server.

- v1.2.4
  - Added missing v1.2.3 changelog.
  - Fixed any falsey arguments being completely omitted. (See [issues#1](https://github.com/Solitarju/vrchat-api-library/issues/1))

- v1.2.3
  - I'm going to be honest, I forgot to add the changelog last push and I don't remember what I did.

- v1.2.2
  - Singular markdown change.

- v1.2.1
  - Sorted each API section into their own modules for better organization.

- v1.2.0
  - Finally completed 100% API coverage!
  - Renamed "Vrchat" manager class to "VRChat".

- v1.1.0
  - Simple fix for major authentication bug on "Vrchat" manager class.

- v1.0.9
  - Added functionality for FriendsApi, WorldsApi & AvatarsApi to an extent.
  - Renamed classes to include 'Api' such as 'AuthenticationApi' including managed classes under the 'Vrchat' class.
  - Moved all Enums to a single class for better organization, the EventType class is now under Enums class and not usable in the same way as before.
  - Markdown changes.

- v1.0.8
  - Added the last changelog version.
  - Removed typescript declarations as it creates critical issues.
  - Reverted exports change.

- v1.0.7
  - Markdown changes.

- v1.0.6
  - Updated & more re-usable header generation and removed the apiKey parameter as it is no longer required.
  - Added typescript declarations file.
  - README markdown updates. (TODO, Changelogs, Disclamers).

License: [GNU GPLv3](https://github.com/Solitarju/vrchat-api-library/blob/main/LICENSE)
