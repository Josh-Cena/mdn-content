---
title: Representing dates & times
slug: Web/JavaScript/Guide/Representing_dates_times
page-type: guide
sidebar: jssidebar
---

{{PreviousNext("Web/JavaScript/Guide/Numbers_and_strings", "Web/JavaScript/Guide/Regular_expressions")}}

Date and time handling is an indispensable part of most JavaScript applications. Whenever your application involves scheduling events, recording durations, or displaying instants, you need to deal with an appropriate date/time representation.

When working with dates, bear in mind one important lesson that the programmer community has learned the hard way: **date handling is complex**. Humans invent too many mechanisms around keeping time, and their rules are not going to be implemented correctly in a few lines of code. Therefore, there are two guiding principles as you move along:

- Write as little code as possible. Doing something that involves a lot of number additions/subtractions/comparisons? Doing a lot of manual string concatenation or parsing? Hardcoding any constants like `24 * 60 * 60`? You probably want to find a suitable utility method instead.
- If you are already using built-in methods as much as possible, but you are still writing a lot of code, don't worry—this is just due to the complexity of the problem. You are probably doing it right.

With that said, let us consider the landscape of the date-time handling APIs in JavaScript.

## A background: Date, Temporal, or others

JavaScript is currently in a somewhat awkward situation when it comes to choosing a date-time handling API. We'll start by recounting a brief history on the evolution of date-time APIs, and what options are available for you.

- {{jsxref("Date")}} has been around since the first days of JavaScript. It was based on the `java.util.Date` class from Java, and both share the same set of design mistakes. We'll talk more about the pain points of `Date` later.
- Everyone realizes how unideal `Date` is. Java iterated twice, introducing `java.util.Calendar` (1997) and `java.time` (2014), one replacing another. In the meantime, JavaScript standardization stalled for more than a decade, and for a multitude of reasons, we remained with `Date` as the sole built-in API for over 30 years. This was when multiple userland date-time libraries were introduced, some of which are still widely used today:
  - [moment.js](https://momentjs.com/) was the first popular date-time library in JavaScript, and remains one of the most widely used libraries today. However, it is considered in maintenance mode, and its maintainers recommend using other libraries. It is also quite large and suffers from some bad designs that successors avoid.
  - [date-fns](https://date-fns.org/) takes a different approach by using `Date` as the primitive data structure, but providing a lot of utility functions to work with it.
  - [luxon](https://moment.github.io/luxon/) is positioned as a successor to `moment.js`, and the idea remains the same but with the design improved.
  - [Day.js](https://day.js.org/) is another replacement for `moment.js`, but with a focus on being lightweight.
- Finally, {{jsxref("Temporal")}} is a new built-in date-time API that is designed to be a successor to `Date`. It absorbs the design from many of the aforementioned libraries, and is designed to avoid many footguns that `Date` has. However, it is not yet widely supported in browsers, and you may need to use a polyfill.

If you want a silver bullet, **start by using {{jsxref("Temporal")}}**, via a polyfill if necessary—eventually you will be able to remove the polyfill and "go native". If there are core functionalities that are missing (of which they are few), choose a library that is lightweight and does what you need. Avoid using `Date` for new code and migrate existing code from `Date` to `Temporal` if possible.

## Temporal object

## Date object

JavaScript does not have a date data type. However, you can use the {{jsxref("Date")}} object and its methods to work with dates and times in your applications. The `Date` object has a large number of methods for setting, getting, and manipulating dates. It does not have any properties.

- Times cannot be represented in time zones other than local (device) and UTC, and there's no concept of wall-clock times (i.e., times without time zones).
- Dates cannot be represented in calendars other than ISO 8601 (Gregorian).
- Months are represented by 0-based rather than 1-based indices; for example, January is `0`, and July is `6`.
- The setters are _mutating_. For example, running `d.setMonth(0)` changes the value of `d`, which can cause unwanted side-effects.
- The date-time string is impossible to be parsed in a consistent way by `Date.parse()` and related APIs, because browsers each implement their own heuristics. To make matters worse, even the standard ISO 8601 format itself is handled inconsistently—a date-only string like `2021-01-01` is treated as midnight in UTC, but `2021-01-01T00:00:00` is treated as midnight in the local time zone.
- ...

JavaScript handles dates similarly to Java. The two languages have many of the same date methods, and both languages store dates as the number of milliseconds since midnight at the beginning of January 1, 1970, UTC, with a Unix Timestamp being the number of seconds since the same instant. The instant at the midnight at the beginning of January 1, 1970, UTC is called the [epoch](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date#the_epoch_timestamps_and_invalid_date).

The `Date` object range is -100,000,000 days to 100,000,000 days relative to the epoch.

To create a `Date` object:

```js
const dateObjectName = new Date([parameters]);
```

where `dateObjectName` is the name of the `Date` object being created; it can be a new object or a property of an existing object.

Calling `Date` without the `new` keyword returns a string representing the current date and time.

The `parameters` in the preceding syntax can be any of the following:

- Nothing: creates today's date and time. For example, `today = new Date();`.
- A string representing a date, in many different forms. The exact forms supported differ among engines, but the following form is always supported: `YYYY-MM-DDTHH:mm:ss.sssZ`. For example, `xmas95 = new Date("1995-12-25")`. If you omit hours, minutes, or seconds, the value will be set to zero.
- A set of integer values for year, month, and day. For example, `xmas95 = new Date(1995, 11, 25)`.
- A set of integer values for year, month, day, hour, minute, and seconds. For example, `xmas95 = new Date(1995, 11, 25, 9, 30, 0);`.

### Methods of the Date object

The `Date` object methods for handling dates and times fall into these broad categories:

- "set" methods, for setting date and time values in `Date` objects.
- "get" methods, for getting date and time values from `Date` objects.
- "to" methods, for returning string values from `Date` objects.
- parse and UTC methods, for parsing `Date` strings.

With the "get" and "set" methods you can get and set seconds, minutes, hours, day of the month, day of the week, months, and years separately. There is a `getDay` method that returns the day of the week, but no corresponding `setDay` method, because the day of the week is set automatically. These methods use integers to represent these values as follows:

- Seconds and minutes: 0 to 59
- Hours: 0 to 23
- Day: 0 (Sunday) to 6 (Saturday)
- Date: 1 to 31 (day of the month)
- Months: 0 (January) to 11 (December)
- Year: years since 1900

For example, suppose you define the following date:

```js
const xmas95 = new Date("1995-12-25");
```

Then `xmas95.getMonth()` returns 11, and `xmas95.getFullYear()` returns 1995.

The `getTime` and `setTime` methods are useful for comparing dates. The `getTime` method returns the number of milliseconds since the epoch for a `Date` object.

For example, the following code displays the number of days left in the current year:

```js
const today = new Date();
const endYear = new Date(1995, 11, 31, 23, 59, 59, 999); // Set day and month
endYear.setFullYear(today.getFullYear()); // Set year to this year
const msPerDay = 24 * 60 * 60 * 1000; // Number of milliseconds per day
let daysLeft = (endYear.getTime() - today.getTime()) / msPerDay;
daysLeft = Math.round(daysLeft); // Returns days left in the year
```

This example creates a `Date` object named `today` that contains today's date. It then creates a `Date` object named `endYear` and sets the year to the current year. Then, using the number of milliseconds per day, it computes the number of days between `today` and `endYear`, using `getTime` and rounding to a whole number of days.

The `parse` method is useful for assigning values from date strings to existing `Date` objects. For example, the following code uses `parse` and `setTime` to assign a date value to the `ipoDate` object:

```js
const ipoDate = new Date();
ipoDate.setTime(Date.parse("Aug 9, 1995"));
```

### Example

In the following example, the function `JSClock()` returns the time in the format of a digital clock.

```js
function JSClock() {
  const time = new Date();
  const hour = time.getHours();
  const minute = time.getMinutes();
  const second = time.getSeconds();
  let temp = String(hour % 12);
  if (temp === "0") {
    temp = "12";
  }
  temp += (minute < 10 ? ":0" : ":") + minute;
  temp += (second < 10 ? ":0" : ":") + second;
  temp += hour >= 12 ? " P.M." : " A.M.";
  return temp;
}
```

The `JSClock` function first creates a new `Date` object called `time`; since no arguments are given, time is created with the current date and time. Then calls to the `getHours`, `getMinutes`, and `getSeconds` methods assign the value of the current hour, minute, and second to `hour`, `minute`, and `second`.

The following statements build a string value based on the time. The first statement creates a variable `temp`. Its value is `hour % 12`, which is `hour` in the 12-hour system. Then, if the hour is `0`, it gets re-assigned to `12`, so that midnights and noons are displayed as `12:00` instead of `0:00`.

The next statement appends a `minute` value to `temp`. If the value of `minute` is less than 10, the conditional expression adds a string with a preceding zero; otherwise it adds a string with a demarcating colon. Then a statement appends a seconds value to `temp` in the same way.

Finally, a conditional expression appends "P.M." to `temp` if `hour` is 12 or greater; otherwise, it appends "A.M." to `temp`.

{{PreviousNext("Web/JavaScript/Guide/Numbers_and_strings", "Web/JavaScript/Guide/Regular_expressions")}}
