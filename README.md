bluemix-hello-iojs
================================================================================

A "Hello World" server in [io.js](https://iojs.org/) sample for Bluemix.

This repo contains a complete sample of a io.js program that you can deploy
on IBM's [BlueMix](https://bluemix.net/) PaaS, which is based on
the [Cloud Foundry open source project](http://cloudfoundry.org/).

Before jumping into the code, make sure you have an IBM ID, by
registering at the
[IBM ID registration](https://www.ibm.com/account/profile/us?page=reg)
page.  You will need the IBM ID to login to BlueMix from the command line.

You will also need to install the `cf` command-line tool, available
here:

* <https://github.com/cloudfoundry/cli/releases>

At the time of this writing, the most recent version is `cf v6.6`.



install the code for the sample program
--------------------------------------------------------------------------------

From a command/shell terminal
* `cd` into the parent directory you want to install the project in
* `git clone` the project into a child directory
* `cd` into that child directory
* run `npm install` to install dependencies

For example:

    $ cd Projects
    $ git clone https://hub.jazz.net/git/pmuellr/bluemix-hello-iojs

        ... git output here ...

    $ cd bluemix-hello-iojs

    $ npm install

        ... npm output here ...


run locally
--------------------------------------------------------------------------------

After installing, run the server using

    iojs server

This should print the following to the console.

    bluemix-hello-iojs: server starting on http://localhost:3000

If the port the program chooses is already being used, you can specify
a different port via the PORT environment variable.

To use a different port on Mac and Linux, set the PORT environment variable
and restart the iojs server

    PORT=3001 iojs server

On Windows, set the PORT environment variable and restart the iojs server

    set PORT=3001
    iojs server

Once the server is running, test it by visiting the following URL in your
browser:

    http://localhost:3000/any/url

You should see the same content in the browser for every URL, which will be

    Hello World

In the command/shell terminal, you will see the following output:

    bluemix-hello-iojs: server starting on http://localhost:3000
    bluemix-hello-iojs: request GET /any/url



logging into BlueMix
--------------------------------------------------------------------------------

Now that you have your IBM ID and the `cf` command-line tool (see above),
you can log into BlueMix and the deploy your app.

First you should tell the `cf` command which environment you want to operate
with, with the `cf api` command:

    cf api https://api.ng.bluemix.net

You should see the following output:

    Setting api endpoint to https://api.ng.bluemix.net...
    OK

    API endpoint: https://api.ng.bluemix.net (API version: 2.0.0)
    Not logged in. Use 'cf login' to log in.
    No org or space targeted, use 'cf target -o ORG -s SPACE'

Note that as long as you only ever interact with the BlueMix environment with the
`cf` command (and not any other CloudFoundry environments), you won't have to
run the `cf api` command again.

To login to BlueMix, use the following command:

    cf login

You will be prompted for your IBM ID userid and password, as in the following
example:

    $ cf login
    API endpoint: https://api.ng.bluemix.net

    Username> [enter your IBM ID here]

    Password> [enter your IBM ID password here]
    Authenticating...
    OK

You will then be prompted to select your 'org' and 'space', just select the defaults,
which should be your IBM ID userid and `dev`, respectively.

When complete, you should see the following:

    API endpoint: https://api.ng.bluemix.net (API version: 2.0.0)
    User:         [your IBM ID]
    Org:          [your IBM ID]
    Space:        dev



deploying to BlueMix
--------------------------------------------------------------------------------

You can deploy an application to BlueMix with the `cf push` command.

Before doing anything else, edit the `manifest.yml` file and change the
`host` property to a value which will be unique across the `mybluemix.net`
domain.  I use my initials, `pjm`.

Use the following command to have the application deployed to BlueMix:

    cf push

`cf push` will read the default manifest file `manifest.yml` for some
default values of options related to your application.

After running the `cf push` command above, you should see the following output:

    Creating app bluemix-hello-iojs in org <my-IBM-id> / space dev as <my-IBM-id>...
    OK

    ...

    Starting app bluemix-hello-iojs in org <my-IBM-id> / space dev as <my-IBM-id>...
    OK

    ...

    1 of 1 instances running

    App started

    Showing health and status for app bluemix-hello-iojs in org <my-IBM-id> / space dev as <my-IBM-id>...
    OK

    requested state: started
    instances: 1/1
    usage: 128M x 1 instances
    urls: bluemix-hello-iojs-pjm.mybluemix.net


         state     since                    cpu    memory          disk
    #0   running   2014-09-18 09:17:45 PM   0.0%   40.5M of 128M   38.1M of 1G

At this point, your application is running and you can visit it on the urls

    http://bluemix-hello-iojs-pjm.mybluemix.net
    https://bluemix-hello-iojs-pjm.mybluemix.net

If you'd like to continue to play with the server by changing the code, use
the following command when you are ready to push the new version to BlueMix:

    cf push

You can stop the server at any time, by using the following command:

    cf stop bluemix-hello-iojs

and then start it later, by using the following command:

    cf start bluemix-hello-iojs

When you're ready to delete the server, use the following command:

    cf delete bluemix-hello-iojs

For more information on the basics of pushing apps, see the Cloud Foundry docs:

* *[Prepare to Deploy an Application](http://docs.cloudfoundry.org/devguide/deploy-apps/prepare-to-deploy.html)*
* *[Deploy Applications](http://docs.cloudfoundry.org/devguide/deploy-apps/)*



files in this repository
--------------------------------------------------------------------------------

`server.js`

The server written with [io.js](https://iojs.org/).  This server was adapted from the
*[example provided in the node.js docs](http://nodejs.org/api/synopsis.html)*,
but uses the [express package](https://www.npmjs.org/package/express)
for the web server.

Another difference is that the port, binding host, and url are determined
via the [`cfenv` package](https://www.npmjs.org/package/cfenv).  This will
return appropriate values both when running in Cloud Foundry and when running
locally.

---

`.cfignore`

List of file patterns that should **NOT** be uploaded to BlueMix.

See the Cloud Foundry doc
*[Prepare to Deploy an Application](http://docs.cloudfoundry.org/devguide/deploy-apps/prepare-to-deploy.html)*
for more information.

In this case, the contents of the file are:

    node_modules

This indicates the io.js modules you installed with `npm install` will **NOT** be
uploaded to BlueMix.  When your app is "staged" (ie, built on BlueMix during
`cf push`), an
`npm install` will be run there to install the required modules.  By avoiding
sending your io.js modules when you push your app, your app will be uploaded
quicker than
if you **HAD** sent the modules.  But you can send the modules you have installed
if you like; just delete the `.cfignore` file.

---

`.gitignore`

List of file patterns that should **NOT** be stored in git.  If you aren't using
git, you don't need this file.  And the contents are personal preference.

See the npm google groups topic
*['node_modules in git' from FAQ](https://groups.google.com/forum/#!topic/npm-/8SRXhD6uMmk)*
for discussion.

---

`LICENSE`

The open source license for this sample; in this case, it's licensed under
[Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0).

---

`manifest.yml`

This file contains information that's used when you `cf push` the application.

See the Cloud Foundry doc
*[Deploying with Application Manifests](http://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html)*
for more information.

---

`package.json`

Standard package.json file for io.js packages.  You will need this file for two
reasons:

* identify your io.js package dependencies during `npm install`
* identify to BlueMix that this directory contains a io.js application

See the npm doc
*[package.json](https://npmjs.org/doc/json.html)*
for more information.

---

`README.md`

This file!
