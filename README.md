node-cron
=========

[![Build Status](https://travis-ci.org/kelektiv/node-cron.svg?branch=master)](https://travis-ci.org/#!/kelektiv/node-cron)
[![wercker status](https://app.wercker.com/status/0cadfe5d45ad7bc819efb636026cf230/s "wercker status")](https://app.wercker.com/project/bykey/0cadfe5d45ad7bc819efb636026cf230)
[![Dependency Status](https://david-dm.org/ncb000gt/node-cron.svg)](https://david-dm.org/ncb000gt/node-cron)

Originally this project was a NodeJS fork of [James Padolsey's][jamespadolsey] [cron.js](http://github.com/padolsey/cron.js).

After [Craig Condon][crcn] made some updates and changes to the code base this has evolved to something that has a bit of both. The cron syntax parsing is mostly James' while using timeout instead of interval is Craig's.

Additionally, this library goes beyond the basic cron syntax and allows you to supply a Date object. This will be used as the trigger for your callback. Cron syntax is still an acceptable CronTime format. Although the Cron patterns supported here extend on the standard Unix format to support seconds digits, leaving it off will default to 0 and match the Unix behavior.

There are tools that help when constructing your cronjobs. You might find
something like https://cronjob.xyz/ helpful. But, note that it doesn't accept
the exact same syntax as this library, for instance, it doesn't accept the
`seconds` field, so keep that in mind.


If You Are Submitting Bugs/Issues
=============

Because we can't magically know what you are doing to expose an issue, it is best if you provide a snippet of code. This snippet need not include your secret sauce, but it must replicate the issue you are describing. The issues that get closed without resolution tend to be the ones without code examples. Thanks.


Versions and Backwards compatibility breaks:
==========

As goes with semver, breaking backwards compatibility should be explicit in the versioning of your library. As such, we'll upgrade the version of this module in accordance with breaking changes (I'm not always great about doing it this way so if you notice that there are breaking changes that haven't been bumped appropriately please let me know). This table lists out the issues which were the reason for the break in backward compatibility.

<table>
<tr>
<td>Node Cron Ver</td><td>Issue #</td>
</tr>
<tr>
<td>1.0.0</td><td><ul><li><a href="https://github.com/ncb000gt/node-cron/pull/41">GH-41</a></li><li><a href="https://github.com/ncb000gt/node-cron/pull/36">GH-36</a></li></ul></td>
</tr>
</table>


Usage (basic cron usage):
==========

```javascript
var CronJob = require('cron').CronJob;
new CronJob('* * * * * *', function() {
  console.log('You will see this message every second');
}, null, true, 'America/Los_Angeles');
```

Note - You need to explictly start a job in order to make it run. This gives a little more control over running your jobs.

Available Cron patterns:
==========

    Asterisk. E.g. *
    Ranges. E.g. 1-3,5
    Steps. E.g. */2

[Read up on cron patterns here](http://crontab.org). Note the examples in the
link have five fields, and 1 minute as the finest granularity, but this library
has six fields, with 1 second as the finest granularity.

Cron Ranges
==========

When specifying your cron values you'll need to make sure that your values fall within the ranges. For instance, some cron's use a 0-7 range for the day of week where both 0 and 7 represent Sunday. We do not.

 * Seconds: 0-59
 * Minutes: 0-59
 * Hours: 0-23
 * Day of Month: 1-31
 * Months: 0-11 (Jan-Dec)
 * Day of Week: 0-6 (Sun-Sat)


Another cron example
==========

```javascript
var CronJob = require('cron').CronJob;
var job = new CronJob('00 30 11 * * 1-5', function() {
  /*
   * Runs every weekday (Monday through Friday)
   * at 11:30:00 AM. It does not run on Saturday
   * or Sunday.
   */
  }, function () {
    /* This function is executed when the job stops */
  },
  true, /* Start the job right now */
  timeZone /* Time zone of this job. */
);
```


Another example with Date
==========

```javascript
var CronJob = require('cron').CronJob;
var job = new CronJob(new Date(), function() {
  /* runs once at the specified date. */
  }, function () {
    /* This function is executed when the job stops */
  },
  true, /* Start the job right now */
  timeZone /* Time zone of this job. */
);
```


For good measure
==========

```javascript
var CronJob = require('cron').CronJob;
var job = new CronJob({
  cronTime: '00 30 11 * * 1-5',
  onTick: function() {
    /*
     * Runs every weekday (Monday through Friday)
     * at 11:30:00 AM. It does not run on Saturday
     * or Sunday.
     */
  },
  start: false,
  timeZone: 'America/Los_Angeles'
});
job.start();
```


Running a job every 5 minutes
==========

```javascript
var CronJob = require('cron').CronJob;
var job = new CronJob({
  cronTime: '00 */5 * * * *',
  onTick: function() {
    /*
     * Runs every weekday (Monday through Friday)
     * at 11:30:00 AM. It does not run on Saturday
     * or Sunday.
     */
  },
  start: false,
  timeZone: 'America/Los_Angeles'
});
job.start();
```


How to check if a cron pattern is valid:
==========

```javascript
try {
	new CronJob('invalid cron pattern', function() {
		console.log('this should not be printed');
	})
} catch(ex) {
	console.log("cron pattern not valid");
}
```


How to check if a job is running
==========

```javascript
var cron = require('cron');

var job1 = new cron.CronJob({
  cronTime: '* * * * *',
  onTick: function() {
    console.log('job 1 ticked');
  },
  start: false,
  timeZone: 'America/Los_Angeles'
});

var job2 = new cron.CronJob({
  cronTime: '* * * * *',
  onTick: function() {
    console.log('job 2 ticked');
  },
  start: false,
  timeZone: 'America/Los_Angeles'
});

console.log('job1 status', job1.running); // job1 status undefined
console.log('job2 status', job2.running); // job2 status undefined

job1.start(); // job 1 started

console.log('job1 status', job1.running); // job1 status true
console.log('job2 status', job2.running); // job2 status undefined
```


Install
==========

    From source: `npm install`
    From npm: `npm install cron`


API
==========

Parameter Based

* `job` - shortcut to `new cron.CronJob()`.
* `time` - shortcut to `new cron.CronTime()`.
* `sendAt` - tells you when a `CronTime` will be run.
* `timeout` - tells you when the next timeout is.
* `CronJob`
  * `constructor(cronTime, onTick, onComplete, start, timezone, context, runOnInit)` - Of note, the first parameter here can be a JSON object that has the below names and associated types (see examples above).
    * `cronTime` - [REQUIRED] - The time to fire off your job. This can be in the form of cron syntax or a JS [Date](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Date) object.
    * `onTick` - [REQUIRED] - The function to fire at the specified time. If an `onComplete` callback was provided, `onTick` will receive it as an argument. `onTick` may call `onComplete` when it has finished its work.
    * `onComplete` - [OPTIONAL] - A function that will fire when the job is stopped with `job.stop()`, and may also be called by `onTick` at the end of each run.
    * `start` - [OPTIONAL] - Specifies whether to start the job just before exiting the constructor. By default this is set to false. If left at default you will need to call `job.start()` in order to start the job (assuming `job` is the variable you set the cronjob to). This does not immediately fire your `onTick` function, it just gives you more control over the behavior of your jobs.
    * `timeZone` - [OPTIONAL] - Specify the timezone for the execution. This will modify the actual time relative to your timezone. If the timezone is invalid, an error is thrown. You can check all timezones available at [Moment Timezone Website](http://momentjs.com/timezone/). Probably don't use both
		`timeZone` and `utcOffset` together or weird things may happen.
    * `context` - [OPTIONAL] - The context within which to execute the onTick method. This defaults to the cronjob itself allowing you to call `this.stop()`. However, if you change this you'll have access to the functions and values within your context object.
    * `runOnInit` - [OPTIONAL] - This will immediately fire your `onTick` function as soon as the requisit initialization has happened. This option is set to `false` by default for backwards compatibility.
    * `utcOffset` - [OPTIONAL] - This allows you to specify the offset of your
		timezone rather than using the `timeZone` param. Probably don't use both
		`timeZone` and `utcOffset` together or weird things may happen.
  * `start` - Runs your job.
  * `stop` - Stops your job.
  * `setTime` - Change the time for the `CronJob`. Param must be a `CronTime`.
  * `lastDate` - Tells you the last execution date.
  * `nextDates` - Provides an array of the next set of dates that will trigger an `onTick`.
  * `fireOnTick` - Allows you to override the `onTick` calling behavior. This matters so only do this if you have a really good reason to do so.
  * `addCallback` - Allows you to add `onTick` callbacks.
* `CronTime`
  * `constructor(time)`
    * `time` - [REQUIRED] - The time to fire off your job. This can be in the form of cron syntax or a JS [Date](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Date) object.

Contributors
===========

* [Romain Beauxis][toots]
* [James Padolsey][jamespadolsey]
* [Craig Condon][crcn]
* [Finn Herpich][errorprone]
* [cliftonc][cliftonc]
* [neyric][neyric]
* [humanchimp][humanchimp]
* [danhbear][danhbear]
* [Jordan Abderrachid][jordanabderrachid]

License
==========

MIT


[toots]:http://github.com/toots
[jamespadolsey]:http://github.com/padolsey
[crcn]:http://github.com/crcn
[cliftonc]:http://github.com/cliftonc
[neyric]:http://github.com/neyric
[humanchimp]:http://github.com/humanchimp
[errorprone]:http://github.com/ErrorProne
[danhbear]:http://github.com/danhbear
[jordanabderrachid]:http://github.com/jordanabderrachid
