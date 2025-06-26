<p align="center">
  <img src="./pulse.png" width="100" alt="project-logo">
</p>
<p align="center">
    <h1 align="center">PULSE</h1>
</p>
<p align="center">
    <em>The modern MongoDB-powered job scheduler library for Node.js</em>
</p>
<p align="center">
	<img src="https://img.shields.io/github/license/yao-pulse/pulse?style=default&logo=opensourceinitiative&logoColor=white&color=24E0A4" alt="license">
	<img src="https://img.shields.io/github/last-commit/yao-pulse/pulse?style=default&logo=git&logoColor=white&color=24E0A4" alt="last-commit">
	<img src="https://img.shields.io/github/languages/top/yao-pulse/pulse?style=default&color=24E0A4" alt="repo-top-language">
	<img src="https://img.shields.io/github/languages/count/yao-pulse/pulse?style=default&color=24E0A4" alt="repo-language-count">
<p>
<p align="center">
	<!-- default option, no dependency badges. -->
</p>

> [!NOTE]
> **@pulsecrom/pulse** was archived by the owner on June 18, 2025. This fork is an attempt to keep the project alive and fix several known issues. If you want to contribute, open an issue and you can be invited as a collaborator. By having multiple people as collaborators we can hopefully avoid the same fate as the original project.

<br><!-- TABLE OF CONTENTS -->

<p align="center">
  <a href="https://docs-pulse.pulsecron.com">Documentation</a> | <a href="https://www.pulsecron.com">Website</a>
</p>
<details>
  <summary>Table of Contents</summary><br>

- [Overview](#overview)
- [Unique Features in Pulse](#unique-features-in-pulse)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
    - [Installation](#installation)
    - [Example](#example)
- [Project Roadmap](#project-roadmap)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)
</details>
<hr>

##  Overview

Pulse is a new fork of the [Agenda](https://github.com/agenda/agenda) project, created as the original project is no longer actively maintained. Positioned as a vital solution in the Node.js ecosystem for job scheduling, the hiatus of Agenda prompted the creation of Pulse. Utilizing MongoDB, Pulse introduces advanced functionalities, improved scalability, and contemporary features to address today’s complex scheduling challenges.

---
<br/>
<br/>


##  Unique Features in Pulse

- **Latest MongoDB Driver Support**: Pulse is fully compatible with the latest MongoDB driver, ensuring users can take advantage of the most current database features and enhancements.
- **Resume Incomplete Tasks After System Restart**: When the system restarts, Pulse resumes incomplete tasks that were in progress or queued for execution, providing seamless continuation without manual intervention.
- **Retry Failed Tasks**: Pulse offers retry mechanisms using exponential and fixed backoff strategies with configurable attempts, ensuring efficient retries of failed tasks without overwhelming the system.
- **Continuous Maintenance**: As an open-source project actively used in a production service, Pulse is consistently maintained and improved, providing users with reliable updates and support.
- **Extensive Documentation**: Provides detailed guides and examples for a quick and easy start.

---
<br/>
<br/>

##  Repository Structure

```sh
└── pulse/
    ├── LICENSE
    ├── README.md
    ├── es.js
    ├── examples
    │   └── concurrency.ts
    ├── package-lock.json
    ├── package.json
    ├── src
    │   ├── cjs.ts
    │   ├── index.ts
    │   ├── job
    │   ├── pulse
    │   └── utils
    ├── tsconfig.eslint.json
    └── tsconfig.json
```

---
<br/>
<br/>

##  Getting Started
| Take a look at our [Quick Start](https://docs-pulse.pulsecron.com/quick-start) guide.

####  Installation

 ```console
 $ npm install --save @pulsecron/pulse
```



####  Example

```typescript
import Pulse from '@yao-pulse/pulse';

const mongoConnectionString = 'mongodb://localhost:27017/pulse';

const pulse = new Pulse({
  db: { address: mongoConnectionString },
  defaultConcurrency: 4,
  maxConcurrency: 4,
  processEvery: '10 seconds',
  resumeOnRestart: true,
});

// Or override the default collection name:
// const pulse = new Pulse({db: {address: mongoConnectionString, collection: 'jobCollectionName'}});

// or pass additional connection options:
// const pulse = new Pulse({db: {address: mongoConnectionString, collection: 'jobCollectionName', options: {ssl: true}}});

// or pass in an existing mongodb-native MongoClient instance
// const pulse = new Pulse({mongo: myMongoClient});

/**
 * Example of defining a job
 */
pulse.define('delete old users', async (job) => {
  console.log('Deleting old users...');
  return;
});

/**
 * Example of repeating a job
 */
(async function () {
  // IIFE to give access to async/await
  await pulse.start();

  await pulse.every('3 minutes', 'delete old users');

  // Alternatively, you could also do:
  await pulse.every('*/3 * * * *', 'delete old users');
})();

/**
 * Example of defining a job with options
 */
pulse.define(
  'send email report',
  async (job) => {
    const { to } = job.attrs.data;

    console.log(`Sending email report to ${to}`);
  },
  { lockLifetime: 5 * 1000, priority: 'high', concurrency: 10 }
);

/**
 * Example of scheduling a job
 */
(async function () {
  await pulse.start();
  await pulse.schedule('in 20 minutes', 'send email report', { to: 'admin@example.com' });
})();

/**
 * Example of repeating a job
 */
(async function () {
  const weeklyReport = pulse.create('send email report', { to: 'example@example.com' });
  await pulse.start();
  await weeklyReport.repeatEvery('1 week').save();
})();

/**
 * Check job start and completion/failure
 */
pulse.on('start', (job) => {
  console.log(time(), `Job <${job.attrs.name}> starting`);
});
pulse.on('success', (job) => {
  console.log(time(), `Job <${job.attrs.name}> succeeded`);
});
pulse.on('fail', (error, job) => {
  console.log(time(), `Job <${job.attrs.name}> failed:`, error);
});

function time() {
  return new Date().toTimeString().split(' ')[0];
}


```


---
<br/>
<br/>

##  Project Roadmap

- [X] **Add Support for Latest Mongoose Version(8.x.x)**: Upgrade Pulse to be fully compatible with the latest version of Mongoose. This will enable Pulse to leverage the newest features and improvements in Mongoose, ensuring better performance, stability, and security for applications that rely on MongoDB through Mongoose.
- [X] **Refactoring to Modern TypeScript Syntax**: Undertake a comprehensive refactor of the codebase to utilize modern TypeScript features and syntax. This refactoring will improve code readability, maintainability, and make it easier for new contributors to understand and contribute to the project.
- [X] **Resolving Issues in Existing Agenda Projects**: Actively address and resolve outstanding issues within the original Agenda project. This initiative not only aids the community by improving the legacy codebase but also informs the development of Pulse by identifying and addressing past challenges.
- [ ] **Rewrite Test Code**: Revamp our testing suite to increase coverage and ensure tests are up-to-date with modern testing practices. This rewrite aims to enhance test reliability and efficiency, facilitating smoother development and deployment cycles.
- [X] **Rewrite Documentation**: Completely revise and update the documentation to reflect all new changes and features, ensure clarity of information, and improve navigation and readability for developers. This effort will include new getting started guides, API documentation, and use case examples to facilitate easier adoption and implementation by users.
---
<br/>
<br/>

##  Contributing

Contributions are welcome! Here are several ways you can contribute:

- **[Report Issues](https://github.com/yao-pulse/pulse/issues)**: Submit bugs found or log feature requests for the `pulse` project.
- **[Submit Pull Requests](https://github.com/yao-pulse/pulse/pulls)**: Review open PRs, and submit your own PRs.
- **[Join the Discussions](https://github.com/yao-pulse/pulse/discussions)**: Share your insights, provide feedback, or ask questions.

<details closed>
<summary>Contributing Guidelines</summary>

1. **Fork the Repository**: Start by forking the project repository to your github account.
2. **Clone Locally**: Clone the forked repository to your local machine using a git client.
   ```sh
   git clone https://github.com/yao-pulse/pulse
   ```
3. **Create a New Branch**: Always work on a new branch, giving it a descriptive name.
   ```sh
   git checkout -b new-feature-x
   ```
4. **Make Your Changes**: Develop and test your changes locally.
5. **Commit Your Changes**: Commit with a clear message describing your updates.
   ```sh
   git commit -m 'Implemented new feature x.'
   ```
6. **Push to github**: Push the changes to your forked repository.
   ```sh
   git push origin new-feature-x
   ```
7. **Submit a Pull Request**: Create a PR against the original project repository. Clearly describe the changes and their motivations.
8. **Review**: Once your PR is reviewed and approved, it will be merged into the main branch. Congratulations on your contribution!
</details>

<details closed>
<summary>Contributor Graph</summary>
<br>
<p align="center">
   <a href="https://github.com{/pulsecron/pulse/}graphs/contributors">
      <img src="https://contrib.rocks/image?repo=pulsecron/pulse">
   </a>
</p>
</details>

---
<br/>
<br/>

##  License

This project is protected under the [MIT](https://github.com/yao-pulse/pulse?tab=MIT-1-ov-file#readme) License. For more details, refer to the [LICENSE](https://github.com/yao-pulse/pulse/blob/main/LICENSE) file.

---
<br/>
<br/>


##  Acknowledgments
- Pulse was forked from [Agenda](https://github.com/agenda/agenda) by [@pulsecron](https://github.com/pulsecron).
