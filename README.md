# ESPN Fantasy Football API
[![npm](https://img.shields.io/npm/v/espn-fantasy-football-api.svg?colorB=deepskyblue)](https://www.npmjs.com/package/espn-fantasy-football-api) [![node](https://img.shields.io/node/v/espn-fantasy-football-api.svg)](https://www.npmjs.com/package/espn-fantasy-football-api) [![Blazing Fast](https://img.shields.io/badge/speed-blazing%20%F0%9F%94%A5-brightgreen.svg)](https://twitter.com/acdlite/status/974390255393505280)

[![Build Status](https://travis-ci.org/mkreiser/ESPN-Fantasy-Football-API.svg?branch=master)](https://travis-ci.org/mkreiser/ESPN-Fantasy-Football-API) [![Maintainability](https://api.codeclimate.com/v1/badges/548bae8930b5efad0418/maintainability)](https://codeclimate.com/github/mkreiser/ESPN-Fantasy-Football-API/maintainability) [![Test Coverage](https://api.codeclimate.com/v1/badges/548bae8930b5efad0418/test_coverage)](https://codeclimate.com/github/mkreiser/ESPN-Fantasy-Football-API/test_coverage) [![dependencies Status](https://david-dm.org/mkreiser/ESPN-Fantasy-Football-API/status.svg)](https://david-dm.org/mkreiser/ESPN-Fantasy-Football-API) [![devDependencies Status](https://david-dm.org/mkreiser/ESPN-Fantasy-Football-API/dev-status.svg)](https://david-dm.org/mkreiser/ESPN-Fantasy-Football-API?type=dev) [![Known Vulnerabilities](https://snyk.io/test/github/mkreiser/ESPN-Fantasy-Football-API/badge.svg?targetFile=package.json)](https://snyk.io/test/github/mkreiser/ESPN-Fantasy-Football-API?targetFile=package.json)


A Javascript API client for both web and NodeJS that connects to the updated/v3 ESPN fantasy football API. Available as an npm package.

## Converting to v3 API

In February 2019, ESPN deprecated and removed their `v2` fantasy football API and upgraded their site to a new `v3` API. ESPN had already made this change for their baseball and basketball fantasy APIs as well. This change broke every project running on the `v2` API, including this project. This project will not work until the code is converted to consume the `v3` API.

Work for converting this project is being tracked in #95.

## Features

* Supports pulling data from ESPN.
* Private league support (NodeJS version only, see [Important Notes](#important-notes)).
* Highly documented.
* Built for speed and efficiency with caching support.
* Built for extensibility by using ES6 classes.

## Documentation Reference

Hosted documentation available at http://espn-fantasy-football-api.s3-website.us-east-2.amazonaws.com/.

## Installation

```
npm install --save espn-fantasy-football-api
```

There are four files exported in the package:

* `web.js` - Production file built for web environments (**main/default file**).
* `node.js` - Production file built for NodeJS environments.
* `web-dev.js` - Same as web, but not minified/obfused to make debugging/developing easier.
* `node-dev.js` - Same as node, but not minified/obfused to make debugging/developing easier.

## Important Notes

### ESPN Databases and Data Storage

This project simply retrieves data from ESPN and formats the responses in an easy to read and use format. ESPN is still responsible for maintaining and providing the data. Recently, many have noticed league data disappearing from previous years, including in other ESPN fantasy sports. This appears to be a result of ESPN deleting this data. While some data exists before 2017 (as of Feb. 1, 2019), some data (such as boxscores) is not longer available.

### ESPN API Changes

Since this project wraps the ESPN API, any breaking changes to the ESPN API will break this project. This occurred in February 2019 when ESPN migrated from their v2 API to a new v3 API (the original version of this project was completed in Janurary 2019). This project has since been updated to consume ESPN's v3 API.

### Private Leagues

Private leagues currently only work with the NodeJS version of this project. Since ESPN/Disney requires two auth cookies to make a valid request, we must provide those. However, modern web browsers forbid the setting of the `Cookie` header, causing authentication rejections in the web version, as the cookies are not passed on the request. Therefore, the only way to support private league requests is via NodeJS.

## How to use

### ESPN API Conventions

* `leagueId` is the id for your league.
  * Example: `387659`
* `seasonId` matches the year in which the season was played.
  * Example: `2018`
* `matchupPeriod` refers to an entire match-up, including if the match-up lasts multiple weeks (not rare in playoff settings for smaller leagues).
  * Example: `3` refers to the third matchup in your league.
* `scoringPeriod` refers to a single NFL week. Since most matchups are 1 week long, the `scoringPeriod` will typically match the `matchupPeriod`. However, for multi-week matchups, `scoringPeriod` allows one to get information about a specific week in the match-up (useful in multi-week playoff match-up).
  * Example: `3` refers to the third week of the NFL season.
  * **Note**: A `scoringPeriodId` of `0` refers to the preseason before any games are played. A `scoringPeriodId` of `18` refers to the end of the season.

* If both a `matchupPeriod` and a `scoringPeriod` are used, the `scoringPeriod` takes precedence.

### Importing ESPN Fantasy Football API

```javascript
import { ... } from 'espn-fantasy-football-api'; // web
import { ... } from 'espn-fantasy-football-api/node'; // node
import { ... } from 'espn-fantasy-football-api/web-dev'; // web development build
import { ... } from 'espn-fantasy-football-api/node-dev'; // node development build
```

### How to Get Data

#### Creating a Client

This will allow you to call the various methods on the `Client` class to grab data for the passed league. For working with multiple leagues, create multiple `Client` instances.

```javascript
import { Client } from 'espn-fantasy-football-api';
const myClient = new Client({ leagueId: 432132 });
```

#### Working with Private Leagues

You'll need two cookies from ESPN: `espn_s2` and `SWID`. These are found at "Application > Cookies > espn.com" in the Chrome DevTools when on espn.com.

**Note**: As specified before, this functionality only works in NodeJS.

```javascript
myClient.setCookies({ espnS2: 'YOUR_ESPN_S2', SWID: 'YOUR_SWID' });
```

#### Getting Boxscores

This will retrieve all boxscores for a week in a season.

**Note**: Due to the way ESPN populates data, both the `scoringPeriodId` and `matchupPeriodId` are required and must correspond with each other correctly.

```javascript
myClient.getBoxscoreForWeek({ seasonId: 2018, scoringPeriodId: 1, matchupPeriodId: 1 }).then((boxscores) => {
  // Do whatever with boxscores
});
```

#### Getting a List of Free Agents

This will retrieve all free agents for a week in a season.

```javascript
myClient.getFreeAgents({ seasonId: 2018, scoringPeriodId: 1 }).then((freeAgents) => {
  // Do whatever with free agents
});
```

#### Getting a List of FF Teams in your FF League

This will retrieve all teams in your league at a given week in a season.

```javascript
myClient.getTeamsAtWeek({ seasonId: 2018, scoringPeriodId: 1 }).then((teams) => {
  // Do whatever with teams
});
```

## Testing

This project includes an expansive test suite. The unit tests ensure specific logic works as intended. The integration tests (**TODO for v3**) make live calls to the ESPN API, ensuring that the project will work in the real world.

Travis CI is used to build and verify changes/pull requests in a clean environment. Additionally, the master branch runs a weekly build on Travis to catch any issues when development activity is sparse.

## Built With

[axios](https://github.com/axios/axios) - Promise based HTTP client.

[babel](https://github.com/babel/babel) + [webpack](https://github.com/webpack/webpack) - Compiles and bundles ES6 and next-gen Javascript to browser-compatible Javascript.

[eslint](https://github.com/eslint/eslint) - Fast code linting to maintain good style and code patterns.

[jest](https://github.com/facebook/jest) - Powerful and fast testing platform.

[jsdoc](https://github.com/jsdoc3/jsdoc) - Generated code documentation.

[lodash](https://github.com/lodash/lodash) - Utility library.

## Versioning

This project uses [Semantic Versioning](https://semver.org/). Until the 1.0.0 version is published, all major changes will be published with a minor version bump.

## License

This project is licensed under [LGPL-3.0](https://choosealicense.com/licenses/lgpl-3.0/) (see LICENSE for details). Essentially, don't take this project and close source it.

This is my first time writing OSS and picking a license. Feel free to reach out with questions and/or concerns.


## Contributing

See [CONTRIBUTING.MD](https://github.com/mkreiser/ESPN-Fantasy-Football-API/blob/master/CONTRIBUTING.md)

## npm scripts

| Script           | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| build            | Builds the module.                                           |
| build:docs       | Builds the docs.                                             |
| clean            | Runs all clean scripts.                                      |
| clean:dist       | Removes the dist folder.                                     |
| clean:docs       | Removes the docs folder.                                     |
| ci               | Runs continuous integration tasks. Currently runs lint, unit and integration tests, and build. |
| lint             | Ensures code style is correct.                               |
| serve:docs       | Builds and serves docs. Defaults to port 8080.               |
| test             | Starts a jest test runner with access to all tests. Pass `--watch` to keep jest alive and watching for changes. Pass a string as a file inclusion pattern. |
| test:all         | Runs the unit tests then the integration tests.              |
| test:integration | Runs the integration tests.                                  |
| test:unit        | Runs the unit tests.                                         |

## Acknowledgements

Thanks to the following projects for their work and documentation of the ESPN API. They served as the inspiration for this project.

[rbarton65/espnff](https://github.com/rbarton65/espnff)

[Possardt/espn-ff-api](https://github.com/Possardt/espn-ff-api)

