---
layout: docs
title: Build notifications
---

# Build notifications

* [Email](#email)
* [HipChat](#hipchat)
* [Slack](#slack)
* [Webhooks](#webhooks)

Build notifications are defined on project level and triggered on build success or fail events. You can configure notification on **Notifications** tab of project settings or in `notifications` section of `appveyor.yml`:

	notifications:
	  - provider: <provider_1>
	    settings: ...
	
	  - provider: <provider_2>
	    settings: ...




<a id="email"></a>
## Email

Email notifications are sent by default. You can limit the amount of email notifications you receive:

* Notifications from all builds
* Only notifications for builds with your commits
* Do not send notifications at all
* Send only if build status has changed

![email notifications](/site/docs/images/notifications/email-notifications.png)




<a id="hipchat"></a>
## HipChat

HipChat has two versions of API: v1 and v2. AppVeyor supports both versions though notifications sent using API v1 allow specifying "from" field:

![hipchat-build-passed-api-v1](/site/docs/images/notifications/hipchat-build-passed-api-v1.png)

![hipchat-build-failed-api-v1](/site/docs/images/notifications/hipchat-build-failed-api-v1.png)

while API v2 always sends messages on behalf API token issuer:

![hipchat-build-passed-api-v2](/site/docs/images/notifications/hipchat-build-passed-api-v2.png)

![hipchat-build-failed-api-v2](/site/docs/images/notifications/hipchat-build-failed-api-v2.png)


### Authentication token

You can generate API v1 token on this page (you must be a group admin): <br/>
`https://<your_account>.hipchat.com/admin/api`

> **Notification** token type is enough for AppVeyor to post message to a room.

You can generate API v2 token on this page: <br/>
`https://<your_account>.hipchat.com/account/api`

### Message template

Default HipChat message template:
	
	<a href=""{{buildUrl}}"">Build {{projectName}} {{buildVersion}} {{status}}</a><br/>
	Commit <a href=""{{commitUrl}}"">{{commitId}}</a> by {{commitAuthor}} on {{commitDate}}:
	<i>{{commitMessage}}</i>

[Customizing message template](#message-template)

### appveyor.yml configuration

	notifications:
	  - provider: HipChat
	    auth_token:
          secure: RbOnSMSFKYzxzFRrxM1+XA==
	    room: ProjectA
	    template: "{message}, {commitId}, ..."

> Encrypt authentication token on [this page](https://ci.appveyor.com/tools/encrypt).

<a id="slack"></a>
## Slack

Slack notification nicely differentiates between passed and failed builds by changing color of AppVeyor logo:

![slack-build](/site/docs/images/notifications/slack-build.png)

### Authentication token

Slack API authentication token can be generated on this page (**Authentication** section): <br/>
[https://api.slack.com](https://api.slack.com)

### Message template

Default Slack message template:
	
	<{{buildUrl}}|Build {{projectName}} {{buildVersion}} {{status}}>
	Commit <{{commitUrl}}|{{commitId}}> by {{commitAuthor}} on {{commitDate}}:
	_{{commitMessage}}_

[Customizing message template](#message-template) <br/>
[Slack message formatting guide](https://api.slack.com/docs/formatting)


### appveyor.yml configuration

	notifications:
	  - provider: Slack
	    auth_token:
          secure: kBl9BlxvRMr9liHmnBs14A==
	    channel: development
	    template: "{message}, {commitId}, ..."

> Encrypt authentication token on [this page](https://ci.appveyor.com/tools/encrypt).



<a id="campfire"></a>
## Campfire

### Authentication token

Campfire API authentication token can be generated on **My info** page: <br/>
`https://<your_account>.campfirenow.com/member/edit`

### Message template

Default Campfire message template:
	
	Build {{projectName}} {{buildVersion}} {{status}}: {{buildUrl}}
	Commit #{{commitId}} by {{commitAuthor}} on {{commitDate}}: {{commitMessage}}

[Customizing message template](#message-template)

### appveyor.yml configuration

	notifications:
	  - provider: Campfire
	    account: appveyor
	    auth_token:
          secure: RifLRG8Vfyol+sNhj9u2JA==
	    room: ProjectA
	    template: "{message}, {commitId}, ..."

> Encrypt authentication token on [this page](https://ci.appveyor.com/tools/encrypt).



<a id="webhooks"></a>
## Webhooks

Configuring webhooks in `appveyor.yml`:

	notifications:

	  - provider: Webhook
	    url: http://www.myhook1.com

	  - provider: Webhook
	    url: http://www.myhook2.com
	    headers:
          User-Agent: myapp 1.0
          Authorization:
            secure: GhD+5xhLz/tkYY6AO3fcfQ==
	    on_build_success: false
	    on_build_failure: True

### Webhook payload

When webhook notification triggers AppVeyor makes POST request to the webhook URL and passes JSON data in the body:

	{
	   "eventName":"build_success",
	   "eventData":{
	      "passed":true,
	      "failed":false,
	      "status":"Success",
	      "started":"2014-04-14 7:57 PM",
	      "finished":"2014-04-14 7:58 PM",
	      "duration":"00:01:30.3741236",
	      "projectId":12064,
	      "projectName":"test-web",
	      "buildId":14636,
	      "buildNumber":26,
	      "buildVersion":"1.0.26",
	      "repositoryProvider":"gitHub",
	      "repositoryScm":"git",
	      "repositoryName":"JohnSmith/test-web",
	      "branch":"master",
	      "commitId":"ad2366f0c4",
	      "commitAuthor":"John Smith",
	      "commitAuthorEmail":"john@smith.com",
	      "commitDate":"2014-04-14 1:54 AM",
	      "commitMessage":"Some changes to appveyor.yml",
	      "committerName":"John Smith",
	      "committerEmail":"john@smith.com",
          "isPullRequest":true,
          "pullRequestId":1,
	      "buildUrl":"https://ci.appveyor.com/project/JohnSmith/test-web/build/1.0.26",
	      "notificationSettingsUrl":"https://ci.appveyor.com/notifications",
	      "messages":[],
	      "jobs":[
	         {
	            "id":"es941edratul5jm3",
	            "name":"",
	            "passed":true,
	            "failed":false,
	            "status":"Success",
	            "started":"2014-04-14 7:57 PM",
	            "finished":"2014-04-14 7:58 PM",
	            "duration":"00:01:27.9060155",
	            "messages":[
	 
	            ],
	            "compilationMessages":[
	               {
	                  "category":"warning",
	                  "message":"Found conflicts between different versions of the same dependent assembly....",
	                  "details":"MSB3247",
	                  "fileName":"..\\..\\Program%20Files%20(x86)\\MSBuild\\12.0\\bin\\Microsoft.Common.CurrentVersion.targets",
	                  "line":1635,
	                  "column":5,
	                  "projectName":"MyWebApp",
	                  "projectFileName":"MyWebApp\\MyWebApp.csproj",
	                  "created":"2014-04-14T19:57:54.0838622+00:00"
	               }
	            ],
	            "artifacts":[
	               {
	                  "fileName":"MyWebApp.zip",
	                  "name":"MyWebApp",
	                  "type":"WebDeployPackage",
	                  "size":3491576,
	                  "url":"https://ci.appveyor.com/api/buildjobs/es941edratul5jm3/artifacts/token/261761baaaa8337f0a13fa8b5587451ff2d13e4cff095c74e6eabb5d5dea0909/MyWebApp.zip"
	               }
	            ]
	         }
	      ]
	   }
	}

> `eventName` can be either `build_success` or `build_failure`




<a id="message-template"></a>
## Customizing message template

HipChat, Slack and Campfire providers support custom message template.

Message template is a [Mustache template](http://mustache.github.io/mustache.5.html) with the same data as `eventData` field in webhook JSON payload above, for example:

	{{#passed}} Build {{projectName}} {{buildVersion}} passed {{/passed}}
	{{#failed}} Build {{projectName}} {{buildVersion}} failed {{/failed}}