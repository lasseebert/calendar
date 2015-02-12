Kalends
=======

[![Build
Status](https://travis-ci.org/lau/kalends.svg?branch=master)](https://travis-ci.org/lau/kalends) ![Hex Version](http://img.shields.io/hexpm/v/kalends.svg?style=flat)](https://hex.pm/packages/kalends)

Kalends is a date and time library for Elixir.

**kalends |ˈkalɪndz|**
_plural noun_ -
the first day of the month in the ancient Roman calendar. Agner Krarup Erlang was born on the first day of January 1878.

It is a priority to provide timezone information that is as accurate as
possible. The Olson/Eggert "Time Zone Database" is used. Years 1 through 2200
are supported.

## Getting started

Add Kalends as a dependency to an Elixir project by adding it to your mix.exs file:

    defp deps do
      [  {:kalends, "~> 0.2.3"},  ]
    end

Then run `mix deps.get` which will fetch Kalends via the hex package manager.

You can then call Kalends functions like this: `Kalends.DateTime.now("UTC")`. But in order to avoid typing Kalends all the time you can add `use Kalends` to your modules. This aliases Kalends modules such as DateTime and Date. Which means that you can call for instance `DateTime.now("UTC")` without writing `Kalends.` Example:

    defmodule NewYearsHttpLib do
      use Kalends

      def httpdate_new_years(year) do
        {:ok, dt} = DateTime.from_erl({{year,1,1},{0,0,0}}, "UTC")
        DateTime.Format.httpdate(dt)
      end

      # Calling httpdate_new_years(2015) will return
      # "Thu, 01 Jan 2015 00:00:00 GMT"
    end

## Usage examples

For these example first either alias DateTime with this command: `alias Kalends.DateTime` or for use within a model add `use Kalends` to the module.

Get a DateTime struct for the 4th of October 2014 at 23:44:32 in the city of
Montevideo:

    {:ok, mvd} = DateTime.from_erl {{2014,10,4},{23,44,32}}, "America/Montevideo"
    {:ok, %Kalends.DateTime{abbr: "UYT", day: 4, hour: 23, min: 44, month: 10,
                            sec: 32, std_off: 0, timezone: "America/Montevideo",
                            utc_off: -10800, year: 2014}}

A DateTime struct is now assigned to the variable `mvd`. Let's get a DateTime
struct for the same time in the London time zone:

    london = mvd |> DateTime.shift_zone! "Europe/London"
    %Kalends.DateTime{abbr: "BST", day: 5, hour: 3, min: 44, month: 10,
                      sec: 32, std_off: 3600, timezone: "Europe/London",
                      utc_off: 0, year: 2014}

...and then in UTC:

    london |> DateTime.shift_zone! "UTC"
    %Kalends.DateTime{abbr: "UTC", day: 5, hour: 2, min: 44, month: 10,
                   sec: 32, std_off: 0, timezone: "UTC", utc_off: 0, year: 2014}

Formatting a DateTime using "strftime":

    mvd |> DateTime.Format.strftime! "The day is %A. The time in 12 hour notation is %I:%M:%S %p"
    "The day is Saturday. The time in 12 hour notation is 11:44:32 PM"

Transforming a DateTime to a string in ISO 8601 / RFC 3339 format:

    mvd |> DateTime.Format.iso8601
    "2014-10-04T23:44:32-03:00"

Parsing the same string again back into a DateTime:

    DateTime.Parse.rfc3339 "2014-10-04T23:44:32-03:00", "America/Montevideo"
    {:ok, %Kalends.DateTime{abbr: "UYT", day: 4, hour: 23, min: 44,
            month: 10, sec: 32, std_off: 0, timezone: "America/Montevideo",
            utc_off: -10800, year: 2014}}

Parsing an RFC 3339 timestamp as UTC:

    DateTime.Parse.rfc3339_utc "2014-10-04T23:44:32.4999Z"
    {:ok, %Kalends.DateTime{abbr: "UTC", day: 4, microsec: 499900, hour: 23,
            min: 44, month: 10, sec: 32, std_off: 0, timezone: "UTC",
            utc_off: 0, year: 2014}}

The time right now for a specified time zone:

    cph = DateTime.now "Europe/Copenhagen"
    %Kalends.DateTime{abbr: "CEST", day: 5, hour: 21,
     min: 59, month: 10, sec: 24, std_off: 3600, timezone: "Europe/Copenhagen",
     utc_off: 3600, year: 2014}

Transform a DateTime struct to an Erlang style tuple:

    cph |> DateTime.to_erl
    {{2014, 10, 5}, {21, 59, 24}}

## Documentation

Documentation can be found at http://hexdocs.pm/kalends/

## Raison d'être

There are many different rules for time zones all over the world and they change
often. In order to correctly find out what time it is around the world, the
"tz database" is invaluable. This is (AFAIK) the first pure Elixir library that
uses the tz database correctly and can easily be updated whenever a new version
is released.

## Time Zone Database updates

The time zone database (tzdata) is regularly updated. When a new tzdata
version is released, developers of Kalends can easily download the new version
simply by running a command. Then when compiling Kalends, the new tzdata will be
used. The function `Kalends.TimeZoneData.tzdata_version` tells you the tzdata
release version in use.

## Known bugs

There are no confirmed bugs as this is written. But if you do find a problem,
please create an issue on the GitHub page: https://github.com/lau/kalends

## License

Kalends is released under the MIT license. See the LICENSE file.

The tzdata (found in the tzdata directory) is public domain.
